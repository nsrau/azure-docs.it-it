<properties
	pageTitle="Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell | Microsoft Azure"
	description="Questo articolo illustra la migrazione supportata dalla piattaforma di risorse dal modello classico al modello di Azure Resource Manager tramite script di PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="danlep"/>

# Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell

Questi passaggi mostrano come usare i comandi di Azure PowerShell per eseguire la migrazione di risorse IaaS (infrastruttura distribuita come servizio) dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager. Questi passaggi seguono un approccio basato sul completamento di valori predefiniti per la migrazione dell'ambiente personalizzato. Usare i comandi sostituendo le variabili (le righe che iniziano con "$") con i valori personalizzati.

## Passaggio 1: Preparare la migrazione

Ecco alcune procedure consigliate per valutare la migrazione delle risorse IaaS dal modello classico al modello di Resource Manager:

- Leggere [l'elenco di configurazioni e funzionalità non supportate](virtual-machines-windows-migration-classic-resource-manager.md). Se sono disponibili macchine virtuali che usano configurazioni o funzionalità non supportate, è consigliabile attendere l'annuncio del supporto di tali configurazioni o funzionalità. In alternativa, in base alle esigenze è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione.
-	Se si hanno script automatizzati che consentono di distribuire subito l'infrastruttura e le applicazioni, provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.

## Passaggio 2: Installare la versione più recente di Azure PowerShell

Per l'installazione sono disponibili due opzioni principali, [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) e [Installazione guidata piattaforma Web (WebPI)](http://aka.ms/webpi-azps). WebPI riceve aggiornamenti mensili. PowerShell Gallery riceve aggiornamenti su base continua.

Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Passaggio 3: Impostare la sottoscrizione e iscriversi per la migrazione

Avviare prima un prompt di PowerShell. Per gli scenari di migrazione è necessario configurare l'ambiente per il modello classico e di Resource Manager.

Accedere con l'account per il modello di Resource Manager.

	Login-AzureRmAccount

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure per la sessione corrente. Sostituire tutti gli elementi racchiusi tra virgolette, inclusi i caratteri < e >, con il nome corretto.

	Select-AzureRmSubscription –SubscriptionName "<subscription name>"

>[AZURE.NOTE] La registrazione è un passaggio da eseguire una sola volta, ma è necessario provvedervi prima di tentare la migrazione. Senza la registrazione, verrà visualizzato il seguente messaggio di errore:

>	*BadRequest : Subscription is not registered for migration.* 

Registrarsi con il provider di risorse di migrazione utilizzando il comando seguente.
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Attendere cinque minuti che la registrazione venga completata. È possibile controllare lo stato dell'approvazione con il comando seguente. Assicurarsi che RegistrationState sia `Registered` prima di procedere.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Accedere ora con l'account per il modello classico.

	Add-AzureAccount

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure per la sessione corrente. Sostituire tutti gli elementi racchiusi tra virgolette, inclusi i caratteri < e >, con il nome corretto.

	Select-AzureSubscription –SubscriptionName "<subscription name>"

## Passaggio 4: Verificare che siano disponibili sufficienti memorie centrali delle macchine virtuali di Azure Resource Manager nell'area di Azure di cui fa parte la distribuzione corrente o la rete virtuale

È possibile usare il comando PowerShell seguente per controllare la quantità corrente di memorie centrali in Azure Resource Manager. Per altre informazioni sulle quote di memoria centrale, vedere [Limiti e Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
Get-AzureRmVMUsage -Location "<Your VNET or Deployment's Azure region"
```

## Passaggio 5: Eseguire i comandi per la migrazione delle risorse IaaS

>[AZURE.NOTE] Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma tenterà di ripetere l'azione.

### Eseguire la migrazione delle macchine virtuali in un servizio cloud e non in una rete virtuale

Ottenere l'elenco dei servizi cloud con il comando seguente e selezionare il servizio cloud di cui si vuole eseguire la migrazione. Se le VM nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web/di lavoro, il comando restituisce un messaggio di errore.

	Get-AzureService | ft Servicename

Ottenere il nome della distribuzione per il servizio cloud con il comando seguente.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Preparare le macchine virtuali nel servizio cloud per la migrazione. È possibile scegliere tra due opzioni.

1. Per eseguire la migrazione delle VM a una rete virtuale creata dalla piattaforma

	Per prima cosa, verificare se è possibile migrare il servizio cloud utilizzando il comando seguente:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
		$validate.ValidationMessages

	Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con la seguente procedura di preparazione.

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

2. Per eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Resource Manager

		$existingVnetRGName = "<Existing VNET's Resource Group Name>"
		$vnetName = "<Virtual Network Name>"
		$subnetName = "<Subnet name>"

	Per prima cosa, verificare se è possibile migrare il servizio cloud utilizzando il comando seguente:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
		$validate.ValidationMessages

	Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con la seguente procedura di preparazione.

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

Dopo aver completato l'operazione di preparazione, è possibile eseguire query sullo stato di migrazione delle VM e assicurarsi che il relativo stato sia `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Controllare la configurazione per le risorse preparate tramite PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName

### Eseguire la migrazione delle macchine virtuali in una rete virtuale

Selezionare la rete virtuale per cui si vuole eseguire la migrazione.

	$vnetName = "VNET-Name"

>[AZURE.NOTE] Se la rete virtuale contiene ruoli Web/di lavoro o VM con configurazioni non supportate, viene visualizzato un messaggio di errore di convalida.

Per prima cosa, verificare se sia possibile eseguire la migrazione della rete virtuale utilizzando il comando seguente:

	Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con la seguente procedura di preparazione.
	
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Controllare la configurazione per le macchine virtuali preparate tramite PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### Migrare un account di archiviazione

Dopo aver completato la migrazione delle macchine virtuali, si consiglia di migrare l'account di archiviazione.

Preparare l'account di archiviazione per la migrazione con il comando seguente.

	$storageAccountName = "storagename"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Controllare la configurazione per l'account di archiviazione preparato tramite PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## Passaggi successivi

- [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell della community](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0914_2016-->