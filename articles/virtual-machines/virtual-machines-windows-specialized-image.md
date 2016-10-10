<properties
	pageTitle="Creare una copia della macchina virtuale di Windows | Microsoft Azure"
	description="Informazioni su come creare una copia della macchina virtuale di Azure che esegue Windows nel modello di distribuzione Resource Manager creando un'*immagine specializzata*."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>  

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>  

# Creare una copia di una macchina virtuale Windows nel modello di distribuzione Azure Resource Manager


Questo articolo illustra come creare una copia della macchina virtuale (VM) di Azure che esegue Windows. In particolare, indica come eseguire questa operazione nel modello di distribuzione Azure Resource Manager tramite Azure PowerShell e il portale di Azure. Mostra come creare un'immagine *specializzata* della VM di Azure, che mantiene gli account utente e altri dati sullo stato dalla VM originale. Un'immagine specializzata può risultare utile per eseguire la migrazione di una VM Windows dal modello di distribuzione classica al modello di distribuzione Resource Manager oppure per creare una copia di backup della VM Windows creata nel modello di distribuzione Resource Manager. È possibile eseguire la copia dei dischi dati e del sistema operativo in questo modo e quindi configurare le risorse di rete per creare la nuova macchina virtuale.

Se occorre creare distribuzioni di massa di VM Windows simili, è necessario usare un'immagine *generalizzata*. A tale scopo, vedere [Come acquisire una macchina virtuale di Windows nel modello di distribuzione Resource Manager](virtual-machines-windows-capture-image.md).



## Prima di iniziare

Accertarsi che prima di iniziare la procedura siano soddisfatti i prerequisiti seguenti:

- **È presente una macchina virtuale di Azure che esegue Windows**, creata con il modello di distribuzione classica o il modello di distribuzione Resource Manager. Il sistema operativo è stato configurato, i dischi dati sono stati collegati e sono state eseguite altre personalizzazioni, ad esempio l'installazione delle applicazioni necessarie. Questa VM verrà usata per creare la copia. Per assistenza nella creazione della VM di origine, vedere [Creare una VM Windows con Resource Manager e PowerShell](virtual-machines-windows-ps-create.md).

- **Azure PowerShell 1.0 (o versione successiva)** è installato sul computer ed è stato eseguito l'accesso alla propria sottoscrizione di Azure. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

- È stato scaricato e installato lo **strumento AzCopy**. Per altre informazioni su questo strumento, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](../storage/storage-use-azcopy.md).

- È presente un **gruppo di risorse** in cui sono stati creati un **account di archiviazione** e un **contenitore BLOB** in cui eseguire la copia dei dischi rigidi virtuali. Per usare un account di archiviazione esistente o per crearne uno nuovo, vedere [Creare o individuare un account di archiviazione di Azure](virtual-machines-windows-upload-image.md#createstorage).

> [AZURE.NOTE] La procedura per le VM create con i due modelli di distribuzione come immagine di origine è simile. Questo articolo mette in rilievo le piccole differenze, laddove presenti.


## Copiare i dischi rigidi virtuali nell'account di archiviazione di Resource Manager


1. Liberare i dischi rigidi virtuali usati dalla VM di origine, seguendo una di queste due procedure:

	- Per copiare la macchina virtuale di origine, arrestarla e deallocarla.

		- Per una VM creata con il modello di distribuzione classica, è possibile usare il [portale](https://portal.azure.com) e fare clic su **Sfoglia** > **Macchine virtuali (classico)** > *VM desiderata* > **Arresta** oppure usare il comando PowerShell `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`.

		- Per una VM creata con il modello di distribuzione Resource Manager, è possibile usare il portale e fare clic su **Sfoglia** > **Macchine virtuali** > *VM desiderata* > **Arresta** oppure usare il comando PowerShell `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`. Si noti che lo stato della VM nel portale passa da **In esecuzione** a **Arrestato (deallocato)**.

	- Per eseguire la migrazione della macchina virtuale di origine, eliminarla e usare il disco rigido virtuale rimanente. Passare alla macchina virtuale nel [portale](https://portal.azure.com) e fare clic su **Elimina**.

1. Trovare le chiavi di accesso per l'account di archiviazione che contengono il disco rigido virtuale di origine, nonché l'account di archiviazione in cui verrà copiato il disco rigido virtuale per creare la nuova VM. La chiave per l'account da cui viene copiato il disco rigido virtuale è chiamata *chiave di origine*, mentre quella per l'account in cui viene copiato il disco rigido virtuale viene chiamata *chiave di destinazione*. Per altre informazioni sulle chiavi di accesso, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

	- Se la VM di origine è stata creata con il modello di distribuzione classica, fare clic su **Sfoglia** > **Account di archiviazione (versione classica)** > *account di archiviazione desiderato* > **Tutte le impostazioni** > **Chiavi**. Copiare la chiave denominata **CHIAVE DI ACCESSO PRIMARIA**.

	- Se la VM di origine è stata creata usando il modello di distribuzione Resource Manager o per l'account di archiviazione da usare per la nuova VM, fare clic su **Sfoglia** > **Account di archiviazione** > *account di archiviazione desiderato* > **Tutte le impostazioni** > **Chiavi di accesso**. Copiare la chiave denominata **key1**.

1. Ottenere gli URL per accedere agli account di archiviazione di origine e di destinazione. Nel portale passare all'account di archiviazione e fare clic su **BLOB**. Fare quindi clic sul contenitore che ospita il disco rigido virtuale di origine, ad esempio *vhds* per il modello di distribuzione classica, oppure sul contenitore in cui si vuole copiare il disco rigido virtuale. Fare clic su **Proprietà** per il contenitore e copiare il testo etichettato **URL**. Sono necessari gli URL di entrambi i contenitori di origine e di destinazione. Gli URL saranno simili a `https://myaccount.blob.core.windows.net/mycontainer`.

1. Nel computer locale aprire una finestra di comando e passare alla cartella in cui è installato AzCopy. Il percorso sarà simile a *C:\\Programmi (x86)\\Microsoft SDKs\\Azure\\AzCopy*. Dalla posizione specificata eseguire questo comando: </br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] È necessario copiare il sistema operativo e i dischi dati separatamente, usando AzCopy come descritto nel passaggio precedente.


## Creare una VM usando il disco rigido virtuale copiato

È ora possibile usare Azure PowerShell per creare una VM Windows basata su Resource Manager in una nuova rete virtuale tramite il disco rigido virtuale copiato nei passaggi precedenti. Il disco rigido virtuale deve essere presente nello stesso account di archiviazione della nuova macchina virtuale che verrà creata.


Configurare una rete virtuale e una scheda di interfaccia di rete per la nuova VM, come indicato nello script seguente. Usare i valori per le variabili, rappresentate dal segno **$**, nel modo appropriato per l'applicazione.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


A questo punto, impostare le configurazioni della VM e usare i dischi rigidi virtuali copiati per creare una nuova macchina virtuale. </br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk by using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows

	#Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach

Gli URL dei dischi dei dati e del sistema operativo sono simili al seguente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Per trovarlo nel portale, passare al contenitore di archiviazione di destinazione, fare clic sul disco rigido virtuale del sistema operativo o dei dati copiato e quindi copiare il contenuto dell'URL.

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Se il comando ha esito positivo, viene visualizzato un output simile al seguente:

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


La VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** oppure usando i comandi di PowerShell seguenti:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

Per accedere alla nuova macchina virtuale, passare alla VM nel [portale](https://portal.azure.com), fare clic su **Connetti** e aprire il file RDP di Desktop remoto. Usare le credenziali dell'account della macchina virtuale originale per accedere alla nuova macchina virtuale.


## Passaggi successivi

Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [Gestire macchine virtuali di Azure con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0928_2016-->