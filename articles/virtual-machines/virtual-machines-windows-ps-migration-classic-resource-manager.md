<properties
	pageTitle="Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell | Microsoft Azure"
	description="Questo articolo illustra la migrazione supportata dalla piattaforma di risorse dal modello classico al modello di Azure Resource Manager tramite script di PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell

Questi passaggi mostrano come usare i comandi di Azure PowerShell per eseguire la migrazione di risorse IaaS (infrastruttura distribuita come servizio) dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager. Questi passaggi seguono un approccio basato sul completamento di valori predefiniti per la migrazione dell'ambiente personalizzato. È sufficiente usare i comandi sostituendo le variabili (le righe che iniziano con "$") con i valori personalizzati.

## Passaggio 1: Preparare la migrazione

Ecco alcune procedure consigliate per valutare la migrazione delle risorse IaaS dal modello classico al modello di Resource Manager:

- Leggere [l'elenco di configurazioni e funzionalità non supportate](virtual-machines-windows-migration-classic-resource-manager.md). Se sono disponibili macchine virtuali che usano configurazioni o funzionalità non supportate, è consigliabile attendere l'annuncio del supporto di tali configurazioni o funzionalità. In alternativa, è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione se ciò soddisfa le esigenze.
-	Se si hanno script automatizzati che consentono di distribuire subito l'infrastruttura e le applicazioni, provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.
- Poiché il servizio è in anteprima pubblica, assicurarsi che l'ambiente di test per la migrazione sia isolato dall'ambiente di produzione. Non combinare account di archiviazione, reti virtuali o altre risorse tra gli ambienti di test e di produzione.

## Passaggio 2: Installare la versione più recente di Azure PowerShell

Per l'installazione sono disponibili due opzioni principali, [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) e [Installazione guidata piattaforma Web (WebPI)](http://aka.ms/webpi-azps). WebPI riceverà degli aggiornamenti mensili. La Raccolta di PowerShell riceverà degli aggiornamenti su base continua.

Per altre informazioni, vedere [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/).

## Passaggio 3: Impostare la sottoscrizione e iscriversi per l'anteprima pubblica della migrazione

Avviare prima un prompt di PowerShell. Per gli scenari di migrazione è necessario configurare l'ambiente per il modello classico e di Resource Manager.

Accedere con l'account per il modello di Resource Manager.

	Login-AzureRmAccount

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure per la sessione corrente. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Effettuare l'iscrizione per l'anteprima pubblica con il comando seguente.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Attendere cinque minuti che la registrazione venga completata. È possibile controllare lo stato dell'approvazione con il comando seguente. Assicurarsi che RegistrationState sia `Registered` prima di procedere.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Accedere ora con l'account per il modello classico.

	Add-AzureAccount

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare la sottoscrizione di Azure per la sessione corrente. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## Passaggio 4: Eseguire i comandi per la migrazione delle risorse IaaS

>[AZURE.NOTE] Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma proverà di nuovo a eseguire l'azione.

### Eseguire la migrazione delle macchine virtuali in un servizio cloud e non in una rete virtuale

Ottenere l'elenco dei servizi cloud con il comando seguente e selezionare il servizio cloud di cui si vuole eseguire la migrazione. Si noti che se le VM nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web/di lavoro, verrà visualizzato un messaggio di errore.

	Get-AzureService | ft Servicename

Ottenere il nome della distribuzione per il servizio cloud con il comando seguente.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Preparare le macchine virtuali nel servizio cloud per la migrazione. È possibile scegliere tra due opzioni.

Se si vuole eseguire la migrazione delle VM a una rete virtuale creata dalla piattaforma, usare il comando seguente.

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Se si vuole eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Resource Manager, usare il comando seguente.

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

Dopo aver completato l'operazione di preparazione, è possibile eseguire query sullo stato di migrazione delle VM e assicurarsi che il relativo stato sia `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Controllare la configurazione per le risorse preparate tramite PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### Eseguire la migrazione delle macchine virtuali in una rete virtuale

Selezionare la rete virtuale per cui si vuole eseguire la migrazione. Si noti che se la rete virtuale contiene ruoli Web/di lavoro o VM con configurazioni non supportate, verrà visualizzato un messaggio di errore di convalida.

Preparare la rete virtuale per la migrazione con il comando seguente.

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Controllare la configurazione per le macchine virtuali preparate tramite PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## Passaggi successivi

- [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell della community](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0601_2016-->