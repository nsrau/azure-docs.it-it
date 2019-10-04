---
title: 'Esercitazione: Distribuzione in una rete virtuale esistente con PowerShell - Modulo di protezione hardware dedicato di Azure | Microsoft Docs'
description: Esercitazione che illustra come distribuire un modulo di protezione hardware dedicato in una rete virtuale esistente con PowerShell.
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 53fb4fa344839957a3f98275d174bbb787fa5e38
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880997"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Esercitazione: Distribuzione di moduli di protezione hardware in una rete virtuale esistente con PowerShell

Il servizio HSM dedicato di Azure offre un dispositivo fisico ad uso esclusivo del cliente, con controllo amministrativo completo e piena responsabilità di gestione. Fornendo hardware fisico, Microsoft deve controllare come i dispositivi vengono allocati per garantire una gestione efficace della capacità. Di conseguenza, il servizio HSM dedicato non sarà normalmente visibile per il provisioning delle risorse all'interno di una sottoscrizione di Azure. Per accedere al servizio HSM dedicato, i clienti di Azure dovranno prima contattare il rispettivo account executive Microsoft per richiedere la registrazione per tale servizio. Solo al termine di tale processo sarà possibile effettuare il provisioning.
Questa esercitazione ha lo scopo di illustrare un processo di provisioning tipico in cui:

- Un cliente ha già una rete virtuale.
- Il cliente ha una macchina virtuale.
- Il cliente deve aggiungere risorse HSM (Hardware Security Module, modulo di protezione hardware) all'ambiente esistente.

L'architettura di una tipica distribuzione in più aree a disponibilità elevata potrebbe essere simile alla seguente:

![Distribuzione in più aree](media/tutorial-deploy-hsm-powershell/high-availability.png)

L'esercitazione è incentrata sull'integrazione di una coppia di moduli di protezione hardware e del necessario gateway ExpressRoute (subnet 1 nella figura sopra) in una rete virtuale esistente (rete virtuale 1 nella figura sopra).  Tutte le altre risorse sono risorse standard di Azure. Lo stesso processo di integrazione può essere usato per i moduli di protezione hardware nella subnet 4 della rete virtuale 3 della figura sopra.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

HSM dedicato di Azure non è attualmente disponibile nel portale di Azure, di conseguenza l'interazione con il servizio avverrà interamente tramite riga di comando o con PowerShell. In questa esercitazione verrà usato PowerShell in Azure Cloud Shell. Se non si ha familiarità con PowerShell, seguire le istruzioni introduttive disponibili nell'[introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Presupposti:

- È stato completato il processo di registrazione di HSM dedicato di Azure e si è ottenuta l'approvazione per l'uso del servizio. In caso contrario, contattare il rappresentante Microsoft per informazioni dettagliate. 
- È stato creato un gruppo di risorse per le risorse di seguito, a cui verranno aggiunte le nuove risorse distribuite nel corso dell'esercitazione.
- Sono già state create la rete virtuale, la subnet e le macchine virtuali necessarie, come illustrato nel diagramma sopra, e si intende ora integrare 2 moduli di protezione hardware in tale distribuzione.

Tutte le istruzioni riportate di seguito presuppongono che si sia già passati al portale di Azure e si sia già aperto Cloud Shell, selezionando "\>\_" in alto a destra nel portale.

## <a name="provisioning-a-dedicated-hsm"></a>Provisioning di un modulo di protezione hardware dedicato

Il provisioning dei moduli di protezione hardware e la relativa integrazione in una rete virtuale esistente tramite gateway ExpressRoute verranno convalidati con lo strumento da riga di comando ssh per verificare la raggiungibilità e la disponibilità di base del dispositivo HSM per qualsiasi ulteriore attività di configurazione. I comandi riportati di seguito useranno un modello di Resource Manager per creare le risorse HSM e le risorse di rete associate.

### <a name="validating-feature-registration"></a>Convalida della registrazione della funzionalità

Come indicato sopra, per qualsiasi attività di provisioning è necessaria la registrazione del servizio HSM dedicato per la sottoscrizione. Per convalidare la registrazione, eseguire questo comando di PowerShell in Cloud Shell nel portale di Azure. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

Il comando seguente verifica le funzionalità di rete necessarie per il servizio HSM dedicato.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowBaremetalServers
```

Per poter procedere ulteriormente, entrambi i comandi devono restituire lo stato "Registered" come illustrato di seguito.  Se è necessario registrarsi per il servizio, contattare il rappresentante Microsoft.

![Stato della sottoscrizione](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>Creazione delle risorse HSM

Viene effettuato il provisioning di un dispositivo HSM nella rete virtuale di un cliente. Di conseguenza è necessaria una subnet. Per la comunicazione tra rete virtuale e dispositivo fisico, il modulo di protezione hardware dipende da un gateway ExpressRoute. È infine necessaria una macchina virtuale per accedere al dispositivo HSM usando il software client Gemalto. Queste risorse sono state raccolte in un file di modello, con il corrispondente file di parametri, per facilitare l'uso. Per ottenere i file, contattare direttamente Microsoft all'indirizzo HSMrequest@Microsoft.com.

Dopo aver ottenuto i file, è necessario modificare il file di parametri per inserire i nomi preferiti per le risorse. A tale scopo, modificare le righe con "value": "".

- `namingInfix`: prefisso per i nomi delle risorse HSM
- `ExistingVirtualNetworkName`: nome della rete virtuale usata per i moduli di protezione hardware
- `DedicatedHsmResourceName1`: nome della risorsa HSM nel data center 1
- `DedicatedHsmResourceName2`: nome della risorsa HSM nel data center 2
- `hsmSubnetRange`: intervallo di indirizzi IP della subnet per i moduli di protezione hardware
- `ERSubnetRange`: intervallo di indirizzi IP della subnet per il gateway di rete virtuale

Di seguito è riportato un esempio delle modifiche:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

Con queste informazioni, il file del modello di Resource Manager creerà 6 risorse:

- Una subnet per i moduli di protezione hardware nella rete virtuale specificata
- Una subnet per il gateway di rete virtuale 
- Un gateway di rete virtuale che connette la rete virtuale ai dispositivi HSM
- Un indirizzo IP pubblico per il gateway
- Un modulo di protezione hardware nel data center 1
- Un modulo di protezione hardware nel data center 2

Dopo l'impostazione dei valori dei parametri, per usare i file è necessario caricarli nella condivisione file di Cloud Shell nel portale di Azure. Nel portale di Azure fare clic sul simbolo di Cloud Shell "\>\_" in alto a destra. La parte inferiore della schermata si trasformerà in un ambiente di riga di comando. Le opzioni disponibili sono BASH e PowerShell. Se non è già impostato, selezionare BASH.

Sulla barra degli strumenti della shell è disponibile un'opzione per il caricamento e il download. Selezionare questa opzione per caricare i file del modello e dei parametri nella condivisione file:

![Condivisione file](media/tutorial-deploy-hsm-powershell/file-share.png)

Dopo aver caricato i file, è possibile creare le risorse.
Prima di creare le nuove risorse HSM, verificare che siano presenti alcune risorse che ne costituiscono i prerequisiti. È necessario avere una rete virtuale con intervalli di subnet per il calcolo, i moduli di protezione hardware e il gateway. I comandi seguenti offrono un esempio di come creare una rete virtuale di questo tipo.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>La configurazione più importante da tenere presente per la rete virtuale è l'impostazione delle deleghe della subnet per il dispositivo HSM su "Microsoft.HardwareSecurityModules/dedicatedHSMs".  Senza questa impostazione, il provisioning dei moduli di protezione hardware non funzionerà.

Quando tutti i prerequisiti sono presenti, eseguire questo comando per usare il modello di Resource Manager, assicurandosi di aver aggiornato i valori con i propri nomi univoci (almeno per il nome del gruppo di risorse):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Il completamento di questo comando richiederà circa 20 minuti. L'opzione "-verbose" usata garantirà la visualizzazione continua dello stato.

![Stato del provisioning](media/tutorial-deploy-hsm-powershell/progress-status.png)

Al termine verrà visualizzato "provisioningState": "Succeeded" e sarà possibile accedere alla macchina virtuale esistente e usare SSH per verificare la disponibilità del dispositivo del modulo di protezione hardware.

## <a name="verifying-the-deployment"></a>Verifica della distribuzione

Per verificare che i dispositivi siano stati sottoposti a provisioning e visualizzarne gli attributi, eseguire il set di comandi riportato di seguito. Assicurarsi che il gruppo di risorse sia impostato correttamente e che il nome delle risorse sia esattamente quello riportato nel file di parametri.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![Stato di provisioning](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Sarà ora possibile anche visualizzare le risorse con [Azure Resource Explorer](https://resources.azure.com/).   In Resource Explorer espandere "subscriptions" (sottoscrizioni) a sinistra, la sottoscrizione specifica per HSM dedicato, "resource groups" (gruppi di risorse) e quindi il gruppo di risorse usato e infine selezionare l'elemento "resources" (risorse).

## <a name="testing-the-deployment"></a>Test della distribuzione

Il test della distribuzione prevede la connessione a una macchina virtuale che può accedere a uno o più moduli di protezione hardware e quindi la connessione diretta al dispositivo HSM. Queste azioni confermeranno la raggiungibilità del modulo di protezione hardware.
Per la connessione alla macchina virtuale viene usato lo strumento ssh. Il comando sarà simile al seguente, ma con il nome amministratore e il nome DNS specificati nel parametro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

La password da usare è quella contenuta nel file di parametri.
Dopo aver eseguito l'accesso alla macchina virtuale Linux, è possibile accedere al modulo di protezione hardware usando l'indirizzo IP privato riportato nel portale per la risorsa \<prefix>hsm_vnic.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Quando si ha l'indirizzo IP, eseguire questo comando:

`ssh tenantadmin@<ip address of HSM>`

Se il comando ha esito positivo, verrà richiesta una password. La password predefinita è PASSWORD. Dato che il modulo di protezione hardware chiederà di modificarla, impostare una password complessa e usare il meccanismo preferito dalla propria organizzazione per archiviare la password ed evitarne la perdita.  

>[!IMPORTANT]
>In caso di perdita di questa password, sarà necessario reimpostare il modulo di protezione hardware perdendo così le chiavi.

Dopo aver stabilito la connessione al dispositivo HSM con ssh, eseguire questo comando per verificare che il modulo di protezione hardware sia operativo.

`hsm show`

L'output sarà simile all'immagine seguente:

![Stato di provisioning](media/tutorial-deploy-hsm-powershell/output.png)

A questo punto, sono state allocate tutte le risorse per una distribuzione a disponibilità elevata con due moduli di protezione hardware e sono stati convalidati l'accesso e lo stato operativo. Qualsiasi ulteriore configurazione o test prevede operazioni aggiuntive con il dispositivo HSM stesso. A tale scopo, seguire le istruzioni riportate nel capitolo 7 della guida all'amministrazione di Gemalto Luna Network HSM 7 per inizializzare il modulo di protezione hardware e creare partizioni. Tutta la documentazione e il software sono scaricabili direttamente da Gemalto dopo aver effettuato la registrazione nel portale di supporto tecnico di Gemalto e aver acquisito un ID cliente. Per ottenere tutti i componenti necessari, scaricare la versione 7.2 del software client.

## <a name="delete-or-clean-up-resources"></a>Eliminazione o pulizia delle risorse

Se il dispositivo HSM non è più necessario, può essere eliminato come risorsa e tornare a far parte del pool libero. In questo caso, la fonte di preoccupazione è ovviamente costituita dagli eventuali dati sensibili dei clienti presenti nel dispositivo. Per rimuovere i dati sensibili dei clienti, è necessario eseguire il ripristino delle impostazioni predefinite del dispositivo con il client Gemalto. Vedere la guida dell'amministratore di Gemalto per il dispositivo SafeNet Network Luna 7 e considerare i comandi seguenti nell'ordine indicato.

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `network interface delete -device eth0`
4. `network interface delete -device eth1`
5. `network interface delete -device eth2`
6. `network interface delete -device eth3`
7. `my file clear -f`
8. `my public-key clear -f`
9. `syslog rotate`


> [!NOTE]
> In caso di problemi con la configurazione del dispositivo Gemalto, contattare il [supporto tecnico di Gemalto](https://safenet.gemalto.com/technical-support/).

Se le risorse di questo gruppo di risorse non sono più necessarie, è possibile rimuoverle tutte con il comando seguente:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Passaggi successivi

Al termine dei passaggi di questa esercitazione, le risorse di HSM dedicato sono state sottoposte a provisioning e sono disponibili nella rete virtuale. Si è ora in condizione di completare la distribuzione con le altre risorse necessarie per l'architettura di distribuzione preferita. Per altre informazioni utili per la pianificazione della distribuzione, vedere i documenti della sezione relativa ai concetti. È consigliabile una progettazione con due moduli di protezione hardware in un'area primaria per la disponibilità a livello di rack e due moduli di protezione hardware in un'area secondaria per la disponibilità a livello di area. Il file di modello usato in questa esercitazione può essere facilmente sfruttato come base per una distribuzione con due moduli di protezione hardware, ma è necessario modificarne i parametri in base ai propri requisiti.

* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Monitoraggio](monitoring.md)
* [Supporto](supportability.md)
