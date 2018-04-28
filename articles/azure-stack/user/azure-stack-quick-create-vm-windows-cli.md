---
title: Creare una macchina virtuale Windows nello Stack di Azure mediante Azure CLI | Documenti Microsoft
description: Informazioni su come creare una macchina virtuale Windows sullo Stack di Azure mediante Azure CLI
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 381c1c37b0675d97adc058979a5d9b5c4fd2cc8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Guida introduttiva: creare una macchina virtuale di Windows Server usando l'interfaccia CLI di Azure nello Stack di Azure

‎*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile creare una macchina virtuale di Windows Server 2016 usando l'interfaccia CLI di Azure. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale. In questo articolo offre inoltre la procedura seguente:

* Connettersi alla macchina virtuale con un client remoto.
* Installare il server web IIS e visualizzare la home page predefinita.
* Pulire le risorse.

## <a name="prerequisites"></a>Prerequisiti

* Verificare che l'operatore di Stack di Azure è stato aggiunto il **Windows Server 2016** immagine nel Marketplace dello Stack di Azure.

* Stack di Azure richiede una versione specifica di CLI di Azure per creare e gestire le risorse. Se non è configurato per Azure Stack CLI di Azure, seguire i passaggi per [installare e configurare Azure CLI](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui è possibile distribuire e gestire le risorse di Azure Stack. Dall'ambiente dello Stack di Azure, eseguire la [az gruppo creare](/cli/azure/group#az_group_create) comando per creare un gruppo di risorse.

>[!NOTE]
 I valori vengono assegnati per tutte le variabili negli esempi di codice. Tuttavia, è possibile assegnare nuovi valori se si desidera.

Nell'esempio seguente viene creato un gruppo di risorse denominato myResourceGroup nel percorso locale.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale (VM) usando il [creare vm az](/cli/azure/vm#az_vm_create) comando. L'esempio seguente crea una macchina virtuale denominata myVM. Questo esempio viene utilizzato Demouser per un nome utente amministrativo e Demouser@123 come password dell'utente. Modificare questi valori in un valore appropriato per l'ambiente.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Quando viene creata la macchina virtuale, il **PublicIPAddress** parametro nell'output contiene l'indirizzo IP pubblico per la macchina virtuale. Annotare questo indirizzo, poiché è necessario per accedere alla macchina virtuale.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Poiché questa macchina virtuale è in esecuzione il server web IIS, è necessario aprire la porta 80 per il traffico Internet.

Usare la [az vm open-port](/cli/azure/vm#open-port) comando per aprire la porta 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

Utilizzare il comando successivo per creare una connessione Desktop remoto alla macchina virtuale. Sostituire "Indirizzo IP pubblico" con l'indirizzo IP della macchina virtuale. Quando richiesto, immettere il nome utente e la password utilizzata per la macchina virtuale.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installare IIS tramite PowerShell

Ora che è effettuato l'accesso alla macchina virtuale, è possibile utilizzare PowerShell per installare IIS. Avviare PowerShell nella macchina virtuale ed eseguire il comando seguente:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

Per visualizzare la pagina iniziale predefinita di IIS, è possibile utilizzare un browser web di propria scelta. Usare l'indirizzo IP pubblico documentato nella sezione precedente per visitare la pagina predefinita.

![Sito IIS predefinito](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire le risorse che non è necessario più. Usare la [eliminazione di gruppi di az](/cli/azure/group#az_group_delete) comando per rimuovere il gruppo di risorse, la macchina virtuale, e tutte le risorse.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida, è stato distribuito una macchina virtuale di Windows Server base. Per ulteriori informazioni sulle macchine virtuali di Azure Stack, continuare a [considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
