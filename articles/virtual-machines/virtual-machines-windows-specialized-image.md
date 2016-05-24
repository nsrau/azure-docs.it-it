<properties
	pageTitle="Creare una copia della macchina virtuale di Windows | Microsoft Azure"
	description="Informazioni su come creare una copia della macchina virtuale di Azure che esegue Windows nel modello di distribuzione Resource Manager creando un'*immagine specializzata*."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# Come creare una copia di una macchina virtuale Windows nel modello di distribuzione Resource Manager


Questo articolo descrive come creare una copia di una macchina virtuale di Azure che esegue Windows nel modello di distribuzione Resource Manager usando Azure PowerShell e il portale di Azure. Mostra come creare un'immagine **_specializzata_** della macchina virtuale di Azure, che mantiene gli account utente e altri dati sullo stato dalla macchina virtuale originale. Un'immagine specializzata può risultare utile in alcuni scenari particolari, ad esempio per eseguire la migrazione di una macchina virtuale Windows dal modello di distribuzione classica al modello di distribuzione Resource Manager oppure per creare una copia della macchina virtuale Windows creata nel modello di distribuzione Resource Manager. È possibile effettuare la copia dei dischi dati e del sistema operativo in questo modo e quindi configurare le risorse di rete per creare la nuova macchina virtuale.

Se invece occorre creare distribuzioni di massa di macchine virtuali Windows simili, è necessario usare un'immagine *generalizzata*. Per informazioni, vedere [Come acquisire una macchina virtuale Windows](virtual-machines-windows-capture-image.md).



## Operazioni preliminari

Questo articolo presuppone che l'utente abbia:

1. Una **macchina virtuale di Azure che esegue Windows** nel modello di distribuzione classica o Resource Manager con il sistema operativo configurato, i dischi dati collegati e le applicazioni necessarie installate. Per assistenza nella creazione di questa macchina virtuale, vedere [Creare una VM Windows con Resource Manager e PowerShell](virtual-machines-windows-ps-create.md). 

1. **Azure PowerShell 1.0 o versione successiva** installato nel computer e connesso alla sottoscrizione di Azure. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

1. **Strumento AzCopy** installato nel computer. Per altre informazioni, vedere [Trasferire dati con lo strumento da riga di comando AzCopy](../storage/storage-use-azcopy.md).

1. Un **gruppo di risorse** con un **account di archiviazione** e un **contenitore BLOB** creati al suo interno per copiarvi i dischi rigidi virtuali. Vedere la sezione [Creare o individuare un account di archiviazione di Azure](virtual-machines-windows-upload-image.md#createstorage) per la procedura necessaria per usare un account di archiviazione esistente o per crearne uno nuovo.



> [AZURE.NOTE] La procedura per le macchine virtuali create con i due modelli di distribuzione come immagine di origine è simile. Verranno indicate le differenze, ove applicabili.


## Copiare i dischi rigidi virtuali nell'account di archiviazione di Resource Manager


1. Prima di tutto, liberare i dischi rigidi virtuali usati dalla macchina virtuale di origine, seguendo una di queste due procedure:

	- Per **_copiare_** la macchina virtuale di origine, **arrestarla** e **deallocarla**.
	
		- Per una VM creata usando il modello di distribuzione classica, è possibile usare il [portale](https://portal.azure.com) e fare clic su **Esplora** > **Macchine virtuali (classico)** > *VM preferita* > **Arresta** oppure usare il comando di PowerShell `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`. 
		
		- Per una VM nel modello di distribuzione Resource Manager, è possibile accedere al portale e fare clic su **Esplora** > **Macchine virtuali** > *VM preferita* > **Arresta** oppure usare il comando PowerShell `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`. Si noti che lo *Stato* della macchina virtuale nel portale passa da **In esecuzione** ad **Arrestato (deallocato)**.

	
	- In alternativa, per **_eseguire la migrazione_** della macchina virtuale di origine, **eliminarla** e usare il disco rigido virtuale rimanente. Fare clic su **Esplora** per passare alla macchina virtuale nel [portale](https://portal.azure.com) e quindi scegliere **Elimina**.
	
1. Trovare le chiavi di accesso per l'account di archiviazione che contengono il disco rigido virtuale di origine, nonché l'account di archiviazione in cui verrà copiato il disco rigido virtuale per creare la nuova macchina virtuale. La chiave per l'account da cui viene copiato il disco rigido virtuale è chiamata *chiave di origine*, mentre quella per l'account in cui viene copiato il disco rigido virtuale viene chiamata *chiave di destinazione*. Per altre informazioni sulle chiavi di accesso, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

	- Se la VM di origine è stata creata con il modello di distribuzione classica, fare clic su **Esplora** > **Account di archiviazione (versione classica)** > *account di archiviazione desiderato* > **Tutte le impostazioni** > **Chiavi** e copiare la chiave con l'etichetta **CHIAVE DI ACCESSO PRIMARIA**. 
	
	- Per una macchina virtuale creata usando il modello di distribuzione Resource Manager o per l'account di archiviazione da usare per la nuova VM, fare clic su **Esplora** > **Account di archiviazione** > *account di archiviazione desiderato* > **Tutte le impostazioni** > **Chiavi di accesso** e copiare la chiave con l'etichetta **key1**.

1. Ottenere gli URL per accedere agli account di archiviazione di origine e di destinazione. Nel portale fare clic su **Esplora** per passare all'account di archiviazione e fare clic su **BLOB**. Fare quindi clic sul contenitore che ospita il disco rigido virtuale di origine, ad esempio *vhds* per il modello di distribuzione classica, oppure sul contenitore in cui si vuole copiare il disco rigido virtuale. Fare clic su **Proprietà** per il contenitore e copiare il testo con l'etichetta **URL**. Sono necessari gli URL di entrambi i contenitori di origine e di destinazione. L'URL sarà simile a `https://myaccount.blob.core.windows.net/mycontainer`.

1. Nel computer locale aprire una finestra di comando e passare alla cartella in cui è installato AzCopy. Il percorso sarà simile a *C:\\Programmi (x86)\\Microsoft SDKs\\Azure\\AzCopy*. Dalla posizione specificata eseguire questo comando: </br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] È necessario copiare i dischi del sistema operativo e dei dati separatamente usando AzCopy come descritto in precedenza.


## Creare una macchina virtuale usando il disco rigido virtuale copiato

Questa procedura mostra come usare Azure PowerShell per creare una macchina virtuale Windows basata su Resource Manager in una nuova rete virtuale usando il disco rigido virtuale copiato nei passaggi precedenti. Il disco rigido virtuale deve essere presente nello stesso account di archiviazione della nuova macchina virtuale che verrà creata.


Configurare prima di tutto una rete virtuale e una scheda di interfaccia di rete per la nuova macchina virtuale come indicato nello script seguente. Usare i valori per le variabili, rappresentate dal segno **$**, nel modo appropriato per l'applicazione.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


A questo punto, impostare le configurazioni della VM e usare i dischi rigidi virtuali copiati per creare una nuova macchina virtuale come illustrato di seguito. </br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
	
	#Add data disks using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
	
Gli URL dei dischi dei dati e del sistema operativo sono simili al seguente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Per trovarlo nel portale, passare al contenitore di archiviazione di destinazione, fare clic sul disco rigido virtuale del sistema operativo o dei dati copiato e quindi copiare il contenuto dell'**URL**.
	
	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
	
Se il comando riesce, viene visualizzato un output simile al seguente:

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


La VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Esplora** > **Macchine virtuali** OPPURE usando i comandi di PowerShell seguenti:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

Per accedere alla nuova macchina virtuale, fare clic su **Esplora** per passare alla macchina virtuale nel [portale](https://portal.azure.com), scegliere **Connetti** e aprire il file RDP di *Desktop remoto*. Usare le credenziali dell'account della macchina virtuale originale per accedere alla nuova macchina virtuale.


## Passaggi successivi

Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [Gestire le macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0511_2016-->