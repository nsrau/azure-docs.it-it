<properties
	pageTitle="Creare e gestire una macchina virtuale Windows con Azure PowerShell e la gestione dei servizi di Azure"
	description="Utilizzare Azure PowerShell per creare rapidamente una nuova macchina virtuale basata su Windows in Gestione servizio e eseguire funzioni di gestione."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="kathydav"/>

# Creare e gestire una macchina virtuale basata su Windows nella gestione del servizio utilizzando Azure PowerShell

Questo articolo descrive come creare e gestire una macchina virtuale di Azure basata su Windows mediante Azure PowerShell e la gestione dei servizi di Azure.

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Distribuire e gestire le macchine virtuali utilizzando modelli di Gestione risorse di Azure e PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Configurare Azure PowerShell

Se è già installato PowerShell di Microsoft Azure, è necessario installare Azure PowerShell versione 0.8.0 o versione successiva. Per verificare la versione di Azure PowerShell installata, usare il comando seguente al prompt dei comandi di Azure PowerShell.

	Get-Module azure | format-table version

Se non è ancora stato installato, attenersi alle istruzioni incluse nell’argomento [Come installare e configurare Azure PowerShell](../install-configure-powershell.md) per installare Azure PowerShell nel computer locale. Quindi, aprire un prompt dei comandi di Azure PowerShell.

In primo luogo, è necessario accedere a Azure tramite questo comando:

	Add-AzureAccount

Specificare l'indirizzo e-mail del proprio account Microsoft Azure e la relativa password nella finestra di accesso a Microsoft Azure.

Successivamente, se si dispone di più sottoscrizioni di Microsoft Azure, è necessario impostare la sottoscrizione di Microsoft Azure prescelta. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti della sottoscrizione, quindi eseguire questi comandi.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

## Creazione di una macchina virtuale

È necessario prima creare un account di archiviazione. È possibile visualizzare l'elenco corrente degli account di archiviazione con questo comando.

	Get-AzureStorageAccount | sort Label | Select Label

Se non già disponibile, creare un nuovo account di archiviazione. È necessario selezionare un nome univoco che contenga solo lettere minuscole e numeri. È possibile verificare l'univocità del nome dell'account di archiviazione con questo comando.

	Test-AzureName -Storage <Proposed Storage account name>

Se questo comando restituisce "False", il nome proposto è univoco.

Durante la creazione di un account di archiviazione, occorre specificare la posizione di un data center Microsoft Azure. Per ottenere un elenco dei data center di Microsoft Azure, eseguire questo comando.

	Get-AzureLocation | sort Name | Select Name

A questo punto, creare e impostare l'account di archiviazione utilizzando i comandi seguenti. Inserire le informazioni necessarie per l'account di archiviazione e sostituire tutti gli elementi all'interno delle virgolette, compresi i caratteri < and >.

	$stAccount="<chosen Storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Successivamente, è necessario creare un servizio cloud. Se non si dispone di un servizio cloud, è necessario crearne uno. È necessario selezionare un nome univoco che contenga solo lettere e numeri e trattini. Il primo e l'ultimo carattere nel campo devono essere una lettera o un numero.

È ad esempio possibile specificare il nome TestCS-*UniqueSequence*, in cui *UniqueSequence* è un'abbreviazione dell'organizzazione. Se ad esempio il nome dell'organizzazione è Tailspin Toys, è possibile assegnare il nome TestCS-Tailspin al servizio cloud.

Per verificare l'univocità del nome, è possibile usare questo comando di Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Se questo comando restituisce "False", il nome proposto è univoco. Creare il servizio cloud utilizzando i comandi:

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Successivamente, copiare il seguente set di comandi Azure PowerShell in un editor di testo.

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

Nell'editor di testo, specificare il nome della macchina virtuale, il nome del servizio cloud e il percorso.

Infine, copiare il set di comandi negli Appunti, quindi fare clic con il pulsante destro del mouse sul prompt dei comandi aperto di Azure PowerShell. In questo modo verrà eseguito il comando impostato come una serie di comandi Azure PowerShell ed è possibile che venga richiesto di specificare il nome e la password dell'account amministratore locale e di creare la macchina virtuale di Microsoft Azure. Di seguito è riportato un esempio dell’esecuzione del set di comandi.

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	VERBOSE: 3:01:17 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Completed Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:23 PM - Completed Operation: Get-AzureVMImage
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
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

## Visualizzare informazioni relative a una macchina virtuale
Si tratta di un'attività di base che si utilizzerà spesso. È possibile utilizzarla per ottenere informazioni su una macchina virtuale, eseguire le attività in una macchina virtuale o recuperare l'output da archiviare in una variabile.

Per ottenere informazioni sulla macchina virtuale, eseguire questo comando, sostituendo tutto ciò che è racchiuso tra virgolette, inclusi i caratteri < and >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Per archiviare l'output in una variabile $vm, eseguire:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Accedere a una macchina virtuale Windows

Eseguire i comandi seguenti.

	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

>[AZURE.NOTE]È possibile ottenere il nome del servizio delle macchine virtuali e cloud dalla visualizzazione della **Get-AzureVM** comando.

## Arrestare una macchina virtuale

Eseguire questo comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilizzare questo **parametro** per mantenere l'IP virtuale (VIP) del servizio cloud, qualora fosse l'ultima macchina virtuale inclusa nel servizio cloud specifico. Se si utilizza questo parametro, verrà ancora configurato per la macchina virtuale.

## Avviare una macchina virtuale

Eseguire questo comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Collegamento di un disco dati
Questa operazione richiede alcuni passaggi. Utilizzare innanzitutto la cmdlet**Aggiungi-DiscoDatiAzure**per aggiungere il disco per l'oggetto $vm. Quindi è possibile utilizzare il cmdlet Aggiorna-MacchinaVirtualeAzure per aggiornare la configurazione della macchina virtuale.

È inoltre necessario decidere se collegare un nuovo disco o uno che contiene già dati. Per un nuovo disco, il comando permette di creare il file con estensione VHD e contemporaneamente di collegarlo.

Per collegare un nuovo disco, eseguire questo comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB <disk size> -DiskLabel "<label name>" -LUN <LUN number> -VM $vm | Update-AzureVM

Per collegare un disco dati esistente, eseguire questo comando:

    Add-AzureDataDisk -Import -DiskName "<existing disk name>" -LUN <LUN number> | Update-AzureVM

Per collegare dischi dati da un file con estensione vhd esistente nell'archiviazione blob, è necessario eseguire questo comando:

    $diskLoc="https://mystorage.blob.core.windows.net/mycontainer/" + "<existing disk name>" + ".vhd"
	Add-AzureDataDisk -ImportFrom -MediaLocation  $diskLoc -DiskLabel "<label name>" -LUN <LUN number> | Update-AzureVM

## Risorse aggiuntive

[Creare una macchina virtuale Windows con Gestione risorse di Azure e Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Creare una macchina virtuale Windows con un modello di Gestione risorse e Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Macchine virtuali - Documentazione](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Come installare e configurare Azure PowerShell](../install-configure-powershell.md)

[Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=July15_HO4-->