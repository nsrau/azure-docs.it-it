<properties
	pageTitle="Creare una macchina virtuale Windows con Gestione risorse di Azure e PowerShell"
	description="Usare una modalità di Gestione risorse di Microsoft Azure Powershell per creare facilmente una nuova macchina virtuale Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="davidmu"/>

# Creare una macchina virtuale Windows con Gestione risorse di Azure e PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione di una risorsa con il modello di distribuzione di gestione delle risorse. È anche possibile creare una risorsa con il [modello di distribuzione classica](virtual-machines-create-windows-powershell-service-manager.md).

Questo argomento descrive come creare rapidamente una macchina virtuale di Azure basata su Windows mediante Gestione risorse di Azure e PowerShell.

## Creare la macchina virtuale Windows

Se è già installato PowerShell di Microsoft Azure, è necessario installare Azure PowerShell versione 0.9.0 o versione successiva. Per verificare quale versione di Azure PowerShell è installata, è possibile usare il comando del relativo prompt di Microsoft PowerShell.

	Get-Module azure | format-table version

Se non è ancora stato installato o se è necessario aggiornare la versione di Microsoft Azure PowerShell, attenersi alle istruzioni riportate nel documento [Come installare e configurare Azure PowerShell](install-configure-powershell.md) per installare Microsoft Azure PowerShell nel computer locale. Quindi, aprire un prompt dei comandi di Azure PowerShell.

In primo luogo, è necessario accedere a Microsoft Azure con questo comando.

	Add-AzureAccount

Specificare l'indirizzo e-mail del proprio account Microsoft Azure e la relativa password nella finestra di accesso a Microsoft Azure.

Successivamente, se si dispone di più sottoscrizioni di Microsoft Azure, è necessario impostare la sottoscrizione di Microsoft Azure prescelta. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti della sottoscrizione, quindi eseguire questi comandi.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

Successivamente, è necessario creare un account di archiviazione. È necessario selezionare un nome univoco che contenga solo lettere minuscole e numeri. È possibile verificare l'univocità del nome dell'account di archiviazione con questo comando.

	Test-AzureName -Storage <Proposed storage account name>

Se questo comando restituisce "False", il nome proposto è univoco.

Occorre specificare la posizione di un data center Microsoft Azure. Per ottenere un elenco dei data center di Microsoft Azure, eseguire questo comando.

	Get-AzureLocation | sort Name | Select Name

Successivamente, è necessario attivare la modalità di Microsoft Azure PowerShell per Gestione risorse. Eseguire il comando seguente.

	Switch-AzureMode AzureResourceManager

A questo punto, copiare il seguente blocco di comandi di PowerShell in un editor di testo. Inserire le informazioni necessarie per l'account di archiviazione e la posizione prescelti, sostituendo tutti gli elementi all'interno delle virgolette, compresi i caratteri < and >.

	$stName="<chosen storage account name>"
	$locName="<chosen Azure location name>"
	$rgName="TestRG"
	New-AzureResourceGroup -Name $rgName -Location $locName
	$storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet=New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzurePublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Infine, copiare il set di comandi negli Appunti, quindi fare clic con il pulsante destro del mouse sul prompt dei comandi aperto di Azure PowerShell. In questo modo verrà eseguito il comando impostato come una serie di comandi di PowerShell ed è possibile che venga richiesto di specificare il nome e la password dell'account amministratore locale e di creare la macchina virtuale di Microsoft Azure.

Di seguito è riportato un esempio di tale file:

	PS C:\> $stName="contosost"
	PS C:\> $locName="West US"
	PS C:\> $rgName="TestRG"
	PS C:\> New-AzureResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *

	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


	PS C:\> $storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:\> $singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:\> $vnet=New-AzurevirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:\> $pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:\> $nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:\> $vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:\> $vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:\> $vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:\> $vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:\> New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm


	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## Risorse aggiuntive

[Provider di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md)

[Creare una macchina virtuale Windows con un modello di Gestione risorse e PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Creare una macchina virtuale Windows con PowerShell e la gestione del servizio Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Macchine virtuali - Documentazione](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Come installare e configurare Azure PowerShell](install-configure-powershell.md)

<!---HONumber=Sept15_HO4-->