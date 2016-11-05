---
title: Eseguire la migrazione a Resource Manager con PowerShell | Microsoft Docs
description: Questo articolo illustra la migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager tramite comandi di Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: danlep

---
# Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell
Questi passaggi mostrano come usare i comandi di Azure PowerShell per eseguire la migrazione di risorse IaaS (infrastruttura distribuita come servizio) dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager. Questi passaggi seguono un approccio basato sul completamento di valori predefiniti per la migrazione dell'ambiente personalizzato. Usare i comandi sostituendo le variabili (le righe che iniziano con "$") con i valori personalizzati.

È anche possibile eseguire la migrazione delle risorse usando l'[interfaccia della riga di comando di Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md).

Per informazioni di base su scenari di migrazione supportati, vedere [Migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md). Per una guida approfondita e la procedura dettagliata di migrazione, vedere [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## Passaggio 1: Pianificare la migrazione
Ecco alcune procedure consigliate per valutare la migrazione delle risorse IaaS dal modello classico al modello di Resource Manager:

* Leggere con attenzione le [funzionalità e configurazioni supportate e non supportate](virtual-machines-windows-migration-classic-resource-manager.md). Se sono disponibili macchine virtuali che usano configurazioni o funzionalità non supportate, è consigliabile attendere l'annuncio del supporto di tali configurazioni o funzionalità. In alternativa, in base alle esigenze è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione.
* Se si hanno script automatizzati che consentono di distribuire subito l'infrastruttura e le applicazioni, provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.

> [!IMPORTANT]
> I gateway di rete virtuale (da sito a sito, Azure ExpressRoute, gateway applicazione, da punto a sito) non sono attualmente supportati per la migrazione dal modello di distribuzione classica a Resource Manager. Per eseguire la migrazione di una rete virtuale classica con un gateway, rimuovere il gateway prima di eseguire un'operazione di commit per spostare la rete. (È possibile eseguire un passaggio di preparazione senza eliminare il gateway). Quindi, dopo aver completato la migrazione, riconnettere il gateway in Azure Resource Manager.
> 
> 

## Passaggio 2: Installare la versione più recente di Azure PowerShell
Per l'installazione di Azure PowerShell sono previste due opzioni principali: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) e [Installazione guidata piattaforma Web (WebPI)](http://aka.ms/webpi-azps). WebPI riceve aggiornamenti mensili. PowerShell Gallery riceve aggiornamenti su base continua. Questo articolo si basa su Azure PowerShell versione 2.1.0.

Per le istruzioni di installazione, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Passaggio 3: Impostare la sottoscrizione e iscriversi per la migrazione
Avviare prima un prompt di PowerShell. Per gli scenari di migrazione è necessario configurare l'ambiente per il modello classico e di Resource Manager.

Accedere con l'account per il modello di Resource Manager.

    Login-AzureRmAccount

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure per la sessione corrente. Sostituire tutti gli elementi racchiusi tra virgolette, inclusi i caratteri < e >, con il nome corretto.

    Select-AzureRmSubscription –SubscriptionName "<subscription name>"

> [!NOTE]
> La registrazione è un passaggio da eseguire una sola volta, ma è necessario provvedervi prima di tentare la migrazione. Senza la registrazione, verrà visualizzato il seguente messaggio di errore:
> 
> *BadRequest : Subscription is not registered for migration.* 
> 
> 

Registrarsi con il provider di risorse di migrazione usando il comando seguente:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Attendere cinque minuti che la registrazione venga completata. È possibile controllare lo stato dell'approvazione con il comando seguente:

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Assicurarsi che RegistrationState sia `Registered` prima di procedere.

Accedere ora con l'account per il modello classico.

    Add-AzureAccount

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure per la sessione corrente. Sostituire tutti gli elementi racchiusi tra virgolette, inclusi i caratteri < e >, con il nome corretto.

    Select-AzureSubscription –SubscriptionName "<subscription name>"

## Passaggio 4: Verificare che siano disponibili sufficienti memorie centrali delle macchine virtuali di Azure Resource Manager nell'area di Azure di cui fa parte la distribuzione corrente o la rete virtuale
È possibile usare il comando PowerShell seguente per controllare il numero corrente di memorie centrali in Azure Resource Manager. Per altre informazioni sulle quote di memoria centrale, vedere [Limiti e Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

```
Get-AzureRmVMUsage -Location "<Your VNET or Deployment's Azure region"
```

## Passaggio 5: Eseguire i comandi per la migrazione delle risorse IaaS
> [!NOTE]
> Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma tenterà di ripetere l'azione.
> 
> 

### Eseguire la migrazione delle macchine virtuali in un servizio cloud e non in una rete virtuale
Ottenere l'elenco dei servizi cloud con il comando seguente e selezionare il servizio cloud di cui si vuole eseguire la migrazione. Se le VM nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web o di lavoro, il comando restituisce un messaggio di errore.

    Get-AzureService | ft Servicename

Ottenere il nome della distribuzione per il servizio cloud con i comandi seguenti:

    $serviceName = "<service name>"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName

Preparare le macchine virtuali nel servizio cloud per la migrazione. È possibile scegliere tra due opzioni.

* **Opzione 1. Eseguire la migrazione delle VM a una rete virtuale creata dalla piattaforma**
  
    Per prima cosa, verificare se è possibile eseguire la migrazione del servizio cloud usando i comandi seguenti:
  
        $validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
        $validate.ValidationMessages
  
    Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con il seguente passaggio di preparazione:
  
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
* **Opzione 2. Eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Resource Manager**
  
        $existingVnetRGName = "<Existing VNET's Resource Group Name>"
        $vnetName = "<Virtual Network Name>"
        $subnetName = "<Subnet name>"
  
    Per prima cosa, verificare se è possibile migrare il servizio cloud utilizzando il comando seguente:
  
        $validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
        $validate.ValidationMessages
  
    Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con il seguente passaggio di preparazione:
  
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

Dopo aver completato la procedura di preparazione tramite una delle opzioni precedenti, eseguire una query dello stato di migrazione delle VM, verificando che sia `Prepared`.

    $vmName = "<vm-name>"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $migrationState = $vm.VM.MigrationState

Controllare la configurazione per le risorse preparate tramite PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente:

    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName

### Eseguire la migrazione delle macchine virtuali in una rete virtuale
Per eseguire la migrazione delle macchine virtuali in una rete virtuale, migrare la rete. Le macchine virtuali migreranno automaticamente con la rete. Selezionare la rete virtuale per cui si vuole eseguire la migrazione.

    $vnetName = "<Virtual Network Name>"

> [!NOTE]
> Se la rete virtuale contiene ruoli Web o di lavoro, o VM con configurazioni non supportate, viene visualizzato un messaggio di errore di convalida.
> 
> 

Per prima cosa, verificare se sia possibile eseguire la migrazione della rete virtuale usando il comando seguente:

    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con il seguente passaggio di preparazione:

    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Controllare la configurazione per le macchine virtuali preparate usando Azure PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente:

    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### Migrare un account di archiviazione
Dopo aver completato la migrazione delle macchine virtuali, si consiglia di migrare gli account di archiviazione.

Preparare ogni account di archiviazione per la migrazione con il comando seguente:

    $storageAccountName = "<storage account name>"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Controllare la configurazione per l'account di archiviazione preparato tramite Azure PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente:

    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## Passaggi successivi
* Per informazioni sulla migrazione, vedere [Migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).
* Per eseguire la migrazione di altre risorse di rete a Resource Manager tramite Azure PowerShell, eseguire una procedura simile con **Move-AzureNetworkSecurityGroup**, **Move-AzureReservedIP** e **Move-AzureRouteTable**.
* Per gli script open source utilizzabili per eseguire la migrazione di risorse dal modello di distribuzione classica ad Azure Resource Manager, vedere [Strumenti della community per la migrazione ad Azure Resource Manager](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0921_2016-->