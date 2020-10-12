---
title: Cloud Shell in una rete virtuale di Azure
description: Distribuire Cloud Shell in una rete virtuale di Azure
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 722d935c242a51ddfc01377676f026b71a8951b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468539"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Distribuire Cloud Shell in una rete virtuale di Azure
> [!NOTE]
> Questa funzionalità è in anteprima pubblica.

Una normale sessione di Cloud Shell viene eseguita in un contenitore in una rete Microsoft separata dalle risorse. Ciò significa che i comandi in esecuzione all'interno del contenitore non possono accedere alle risorse a cui è possibile accedere solo da una rete virtuale specifica. Ad esempio, non è possibile usare SSH per connettersi da Cloud Shell a una macchina virtuale che ha solo un indirizzo IP privato oppure usare kubectl per connettersi a un cluster Kubernetes che ha bloccato l'accesso. 

Questa funzionalità facoltativa risolve queste limitazioni e consente di distribuire Cloud Shell in una rete virtuale di Azure controllata dall'utente. Da qui, il contenitore è in grado di interagire con le risorse all'interno della rete virtuale selezionata.  

Di seguito è possibile visualizzare l'architettura delle risorse che verrà distribuita e usata in questo scenario.

![Viene illustrato il Cloud Shell architettura VNET isolata.](media/private-vnet/data-diagram.png)

Prima di poter usare Cloud Shell nella propria rete virtuale di Azure, sarà necessario creare diverse risorse per supportare questa funzionalità. Questo articolo illustra come configurare le risorse necessarie usando un modello ARM.

> [!NOTE]
> Queste risorse devono essere configurate solo una volta per la rete virtuale. Possono quindi essere condivise da tutti gli amministratori con accesso alla rete virtuale.

## <a name="required-network-resources"></a>Risorse di rete necessarie

### <a name="virtual-network"></a>Rete virtuale
Una rete virtuale definisce lo spazio degli indirizzi in cui vengono create una o più subnet.

È necessario identificare la rete virtuale desiderata da usare per Cloud Shell. Si tratta in genere di una rete virtuale esistente che contiene le risorse che si desidera gestire o una rete che esegue il peering con reti che contengono le risorse.

### <a name="subnet"></a>Subnet
All'interno della rete virtuale selezionata è necessario usare una subnet dedicata per i contenitori Cloud Shell. Questa subnet è delegata al servizio istanze di contenitore di Azure (ACI).  Quando un utente richiede un contenitore Cloud Shell in una rete virtuale, Cloud Shell USA ACI per creare un contenitore che si trova in questa subnet delegata.  Non è possibile creare altre risorse in questa subnet.

### <a name="network-profile"></a>Profilo di rete
Un profilo di rete è un modello di configurazione di rete per le risorse di Azure che specifica determinate proprietà di rete per la risorsa.

### <a name="azure-relay"></a>Servizio di inoltro di Azure
Un [inoltro di Azure](../azure-relay/relay-what-is-it.md) consente due endpoint che non sono direttamente raggiungibili per la comunicazione. In questo caso, viene usato per consentire al browser dell'amministratore di comunicare con il contenitore nella rete privata.

L'istanza di inoltro di Azure usata per Cloud Shell può essere configurata in modo da controllare le reti che possono accedere alle risorse del contenitore: 
- Accessibile dalla rete Internet pubblica: in questa configurazione Cloud Shell fornisce un modo per raggiungere le risorse interne altrimenti dall'esterno. 
- Accessibili dalle reti specifiche: in questa configurazione gli amministratori dovranno accedere al portale di Azure da un computer in esecuzione nella rete appropriata per poter usare Cloud Shell.

## <a name="storage-requirements"></a>Requisiti di archiviazione
Come nel Cloud Shell standard, è necessario un account di archiviazione durante l'uso di Cloud Shell in una rete virtuale. Ogni amministratore deve disporre di una condivisione file per archiviare i file.  L'account di archiviazione deve essere accessibile dalla rete virtuale usata da Cloud Shell. 

## <a name="virtual-network-deployment-limitations"></a>Limitazioni alla distribuzione nella rete virtuale
* A causa delle risorse di rete aggiuntive, l'avvio Cloud Shell in una rete virtuale è in genere più lento rispetto a una sessione di Cloud Shell standard.

* Durante l'anteprima, sono supportate meno aree per Cloud Shell in una rete virtuale. Questo è attualmente limitato a: Westus e WestCentralUS.

* Il servizio di [inoltro di Azure](../azure-relay/relay-what-is-it.md) non è gratuito. Visualizza i [prezzi](https://azure.microsoft.com/pricing/details/service-bus/). Nello scenario Cloud Shell viene utilizzata una connessione ibrida per ogni amministratore durante l'utilizzo di Cloud Shell. La connessione verrà arrestata automaticamente dopo il completamento della sessione di Cloud Shell.

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Il provider di risorse Microsoft. ContainerInstances deve essere registrato nella sottoscrizione che include la rete virtuale che si vuole usare. Selezionare la sottoscrizione appropriata con `Set-AzContext -Subscription {subscriptionName}` , quindi eseguire:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Se **RegistrationState** è `Registered` , non è richiesta alcuna azione. In caso contrario `NotRegistered` , eseguire `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` . 

## <a name="deploy-network-resources"></a>Distribuire le risorse di rete
 
### <a name="create-a-resource-group-and-virtual-network"></a>Creare un gruppo di risorse e una rete virtuale
Se si dispone già di un VNET desiderato a cui si desidera connettersi, ignorare questa sezione.

Nel portale di Azure o usando l'interfaccia della riga di comando di Azure, Azure PowerShell e così via. creare un gruppo di risorse e una rete virtuale nel nuovo gruppo di risorse, **il gruppo di risorse e la rete virtuale devono trovarsi nella stessa area**.

> [!NOTE]
> Durante l'anteprima pubblica, il gruppo di risorse e la rete virtuale devono trovarsi in WestCentralUS o Westus.

### <a name="arm-templates"></a>Modelli di Gestione risorse di Azure
Usare il [modello di avvio rapido di Azure](https://aka.ms/cloudshell/docs/vnet/template) per la creazione di cloud Shell risorse in una rete virtuale e il modello di [avvio rapido di Azure](https://aka.ms/cloudshell/docs/vnet/template/storage) per la creazione dell'archiviazione necessaria. Prendere nota dei nomi delle risorse, principalmente il nome della condivisione file.

### <a name="open-relay-firewall"></a>Apri firewall di inoltro
Passare all'inoltro creato con il modello precedente, selezionare "rete" in impostazioni, consentire l'accesso dalla rete del browser al relè. Per impostazione predefinita, l'inoltro è accessibile solo dalla rete virtuale in cui è stato creato. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Configurazione di Cloud Shell per l'utilizzo di una rete virtuale.
> [!NOTE]
> Questo passaggio deve essere completato per ogni amministratore utilizzerà Cloud Shell.

Dopo aver distribuito il completamento dei passaggi precedenti, passare a Cloud Shell nel portale di Azure o in https://shell.azure.com . Una di queste esperienze deve essere utilizzata ogni volta che si desidera connettersi a un'esperienza di Cloud Shell isolata.

> [!NOTE]
> Se Cloud Shell è stato usato in passato, è necessario smontare il CloudDrive esistente. Per eseguire questa operazione `clouddrive unmount` da una sessione di cloud shell attiva, aggiornare la pagina.

Connetti a Cloud Shell ti verrà richiesto di eseguire la prima esecuzione. Selezionare l'esperienza della shell preferita, selezionare "Mostra impostazioni avanzate" e selezionare la casella "Mostra impostazioni di isolamento VNET". Inserire i campi nella finestra popup.  La maggior parte dei campi consente di compilare automaticamente le risorse disponibili che possono essere associate a Cloud Shell in una rete virtuale.  Il nome della condivisione file dovrà essere compilato dall'utente.


![Viene illustrata l'Cloud Shell impostazioni di prima esperienza VNET isolate.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sulle reti virtuali di Azure](../virtual-network/virtual-networks-overview.md)