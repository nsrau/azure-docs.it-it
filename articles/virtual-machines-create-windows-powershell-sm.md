<properties 
	pageTitle="Creare una macchina virtuale Windows con PowerShell e Service Manager di Microsoft Azure" 
	description="Utilizzare Azure PowerShell per creare rapidamente una nuova macchina virtuale Windows." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Creare una macchina virtuale Windows con PowerShell e Service Manager di Microsoft Azure

Se è già installato PowerShell di Microsoft Azure, è necessario installare Azure PowerShell versione 0.8.0 o versione successiva. Per verificare quale versione di Azure PowerShell è installata, è possibile utilizzare il comando del relativo prompt di Microsoft PowerShell.

	Get-Module azure | format-table version

Se non è ancora stato installato, attenersi alle istruzioni incluse nell’argomento [Come installare e configurare Azure PowerShell](install-configure-powershell.md) per installare Azure PowerShell nel computer locale. Quindi, aprire un prompt dei comandi di Azure PowerShell.

In primo luogo, è necessario accedere a Microsoft Azure con questo comando.

	Add-AzureAccount

Specificare l'indirizzo e-mail del proprio account Microsoft Azure e la relativa password nella finestra di accesso a Microsoft Azure.

Successivamente, se si dispone di più sottoscrizioni di Microsoft Azure, è necessario impostare la sottoscrizione di Microsoft Azure prescelta. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti della sottoscrizione, quindi eseguire questi comandi.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

Successivamente, è necessario creare un account di archiviazione. È possibile visualizzare l'elenco corrente degli account di archiviazione con questo comando.

	Get-AzureStorageAccount | sort Label | Select Label

Se non già disponibile, creare un nuovo account di archiviazione. È necessario selezionare un nome univoco che contenga solo lettere minuscole e numeri. È possibile verificare l'univocità del nome dell'account di archiviazione con questo comando.

	Test-AzureName -Storage <Proposed storage account name>

Se questo comando restituisce "False", il nome proposto è univoco. Durante la creazione di un account di archiviazione, occorre specificare la posizione di un data center Microsoft Azure. Per ottenere un elenco dei data center di Microsoft Azure, eseguire questo comando.

	Get-AzureLocation | sort Name | Select Name

A questo punto, reare e impostare l'account di archiviazione con i seguenti comandi. Inserire le informazioni necessarie per l'account di archiviazione e sostituire tutti gli elementi all'interno delle virgolette, compresi i caratteri < and >.

	$stAccount="<chosen storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Successivamente, è necessario creare un servizio cloud. Se non si dispone di un servizio cloud, è necessario crearne uno. È necessario selezionare un nome univoco che contenga solo lettere e numeri e trattini. Il primo e l’ultimo carattere nel campo deve essere una lettera o un numero.

È ad esempio possibile specificare il nome TestCS-\*UniqueSequence\*, in cui *UniqueSequence* è un'abbreviazione dell'organizzazione. Se ad esempio il nome dell'organizzazione è Tailspin Toys, è possibile assegnare il nome TestCS-Tailspin al servizio cloud.

Per verificare l'univocità del nome, è possibile utilizzare il seguente comando di Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Se questo comando restituisce "False", il nome proposto è univoco. Creare il servizio cloud con questi comandi.

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Successivamente, copiare il seguente set di comandi PowerShell in un editor di testo.

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

Nell'editor di testo, specificare il nome della macchina virtuale, il nome del servizio cloud e il percorso.

Infine, copiare il set di comandi negli Appunti, quindi fare clic con il pulsante destro del mouse sul prompt dei comandi aperto di Azure PowerShell. In questo modo verrà eseguito il comando impostato come una serie di comandi di PowerShell ed è possibile che venga richiesto di specificare il nome e la password dell'account amministratore locale e di creare la macchina virtuale di Microsoft Azure. Di seguito è riportato un esempio dell’esecuzione del set di comandi.

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	PS C:\> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password

	
	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :
	
	PS C:\> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest
	
	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded
	

## Risorse aggiuntive

[Creare una macchina virtuale Windows con un modello di Gestione risorse di Microsoft Azure e PowerShell](virtual-machines-create-windows-powershell-rm.md)

[Creare una macchina virtuale Windows con un modello di Gestione risorse e PowerShell](virtual-machines-create-windows-powershell-rm-template-simple.md)

[Documentazione delle macchine virtuali](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Come installare e configurare Azure PowerShell](install-configure-powershell.md)

[Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)


<!--HONumber=52-->
