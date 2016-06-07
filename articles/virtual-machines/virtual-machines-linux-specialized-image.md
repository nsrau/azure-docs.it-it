<properties
	pageTitle="Creare una copia della macchina virtuale Linux | Microsoft Azure"
	description="Informazioni su come creare una copia della macchina virtuale di Azure che esegue Linux nel modello di distribuzione Resource Manager creando un'*immagine specializzata*."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="cynthn"/>

# Creare una copia di una macchina virtuale Linux nel modello di distribuzione Azure Resource Manager


Questo articolo illustra come creare una copia della macchina virtuale di Azure (VM) che esegue Linux. In particolare, indica come eseguire questa operazione nel modello di distribuzione Resource Manager tramite l'interfaccia della riga di comando di Azure e il portale di Azure. Mostra come creare un'immagine *specializzata* della VM di Azure, che mantiene gli account utente e altri dati sullo stato dalla VM originale. Un'immagine specializzata può risultare utile per eseguire la migrazione di una VM Linux dal modello di distribuzione classica al modello di distribuzione Resource Manager oppure per creare una copia di backup della VM Linux creata nel modello di distribuzione Resource Manager. È possibile eseguire la copia dei dischi dati e del sistema operativo in questo modo e quindi configurare le risorse di rete per creare la nuova macchina virtuale.

Se occorre creare distribuzioni di massa di VM Linux simili, è necessario usare un'immagine *generalizzata*. A tale scopo, vedere [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager](virtual-machines-linux-capture-image.md).



## Prima di iniziare

Accertarsi che prima di iniziare la procedura siano soddisfatti i prerequisiti seguenti:

- È presente una macchina virtuale di Azure che esegue Linux, creata con il modello di distribuzione classica o il modello di distribuzione Resource Manager. Il sistema operativo è stato configurato, i dischi dati sono stati collegati e sono state eseguite altre personalizzazioni, ad esempio l'installazione delle applicazioni necessarie. Questa VM verrà usata per creare la copia. Per assistenza nella creazione della VM di origine, vedere [Creare una VM Linux in Azure tramite l'interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md).

- L'interfaccia della riga di comando di Azure è stata scaricata e installata nel computer ed è stato eseguito l'accesso alla propria sottoscrizione di Azure. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

- È presente un gruppo di risorse in cui sono stati creati un account di archiviazione e un contenitore BLOB in cui eseguire la copia dei dischi rigidi virtuali. Per altre informazioni su come creare gli account di archiviazione e i contenitori BLOB, vedere [Utilizzo dell'interfaccia della riga di comando di Azure con Archiviazione di Azure](../storage/storage-azure-cli.md).

> [AZURE.NOTE] La procedura per le VM create con i due modelli di distribuzione come immagine di origine è simile. Questo articolo mette in rilievo le piccole differenze, laddove presenti.


## Copiare i dischi rigidi virtuali nell'account di archiviazione di Resource Manager


1. Liberare i dischi rigidi virtuali usati dalla VM di origine, seguendo una di queste due procedure:

	- Per copiare la macchina virtuale di origine, arrestarla e deallocarla. Nel portale fare clic su **Sfoglia** > **Macchine virtuali** o **Macchine virtuali (classico)** > *VM desiderata* > **Arresta**. Per le VM create nel modello di distribuzione Resource Manager, è possibile usare il comando `azure vm stop <yourResourceGroup> <yourVmName>` dell'interfaccia della riga di comando di Azure, seguito da `azure vm deallocate <yourResourceGroup> <yourVmName>`. Si noti che lo stato della VM nel portale passa da **In esecuzione** a **Arrestato (deallocato)**.

	- Per eseguire la migrazione della macchina virtuale di origine, eliminarla e usare il disco rigido virtuale rimanente. Passare alla macchina virtuale nel [portale](https://portal.azure.com) e fare clic su **Elimina**.

1. Trovare la chiave di accesso per l'account di archiviazione contenente il disco rigido virtuale di origine. Per altre informazioni sulle chiavi di accesso, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

	- Se la VM di origine è stata creata con il modello di distribuzione classica, fare clic su **Sfoglia** > **Account di archiviazione (versione classica)** > *account di archiviazione desiderato* > **Tutte le impostazioni** > **Chiavi**. Copiare la chiave denominata **CHIAVE DI ACCESSO PRIMARIA**. In alternativa, nell'interfaccia della riga di comando di Azure passare alla modalità classica usando i comandi `azure config mode asm` e quindi `azure storage account keys list <yourSourceStorageAccountName>`.

	- Se la VM di origine è stata creata con il modello di distribuzione Resource Manager, fare clic su **Sfoglia** > **Account di archiviazione** > *account di archiviazione desiderato* > **Tutte le impostazioni** > **Chiavi di accesso**. Copiare il testo denominato **key1**. In alternativa, nell'interfaccia della riga di comando di Azure passare alla modalità Resource Manager usando il comando `azure config mode arm` e quindi usare `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>`.

1. Copiare i file VHD usando i [comandi dell'interfaccia della riga di comando con archiviazione di Azure](../storage/storage-azure-cli.md), come descritto nella procedura seguente. In alternativa, se si preferisce un approccio tramite interfaccia utente, è possibile usare [Microsoft Azure Storage Explorer](http://storageexplorer.com/) (Esplora archivi di Microsoft Azure). </br>
	1. Configurare la stringa di connessione per l'account di archiviazione di destinazione. La stringa di connessione conterrà la chiave di accesso per l'account di archiviazione.

			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>

	2. Creare una [firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md) per il file VHD nell'account di archiviazione di origine. Prendere nota dell'**URL di accesso condiviso** nell'output del comando seguente.

			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>

	3. Copiare il file VHD dall'archiviazione di origine a quella di destinazione usando il comando seguente.

			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>

	4. Il file VHD viene copiato in modo asincrono. Per monitorarne lo stato di avanzamento, usare il comando seguente.

			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>

</br>

>[AZURE.NOTE] È necessario copiare il sistema operativo e i dischi dati separatamente, come descritto in precedenza.


## Creare una VM usando il disco rigido virtuale copiato

È ora possibile usare l'interfaccia della riga di comando di Azure per creare una VM Linux basata su Resource Manager in una nuova rete virtuale usando il disco rigido virtuale copiato nei passaggi precedenti. Il disco rigido virtuale deve essere presente nello stesso account di archiviazione della nuova macchina virtuale che verrà creata.


Configurare una rete virtuale e una scheda di interfaccia di rete per la nuova VM, come indicato nello script seguente. Usare i valori per le variabili nel modo appropriato per l'applicazione.

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


Creare la nuova macchina virtuale con i dischi rigidi virtuali copiati usando il comando seguente. </br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>


Gli URL dei dischi dei dati e del sistema operativo sono simili al seguente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Per trovarlo nel portale, passare al contenitore di archiviazione, fare clic sul disco rigido virtuale del sistema operativo o dei dati copiato e quindi copiare il contenuto dell'URL.


Se il comando ha esito positivo, viene visualizzato un output simile al seguente:

	$azure vm create -g "testcopyRG" -n "redhatcopy" -f "LinCopyNic" -d https://testcopystore.blob.core.windows.net/testcopyblob/RedHat201631816334.vhd -x https://testcopystore.blob.core.windows.net/testcopyblob/RedHat-data.vhd -e 10 -Y -l "West US" -y Linux -z "Standard_A1" -o "testcopystore" -R "testcopyblob"
	info:    Executing command vm create
	+ Looking up the VM "redhatcopy"
	info:    Using the VM Size "Standard_A1"
	+ Looking up the NIC "LinCopyNic"
	info:    Found an existing NIC "LinCopyNic"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourceGroups/testcopyRG/providers/Microsoft.Network/virtualNetworks/LinCopyVnet/subnets/LinCopySub" in the NIC "LinCopyNic"
	info:    This NIC IP configuration has a public ip already configured "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourcegroups/testcopyrg/providers/microsoft.network/publicipaddresses/lincopyip", any public ip parameters if provided, will be ignored.
	+ Looking up the storage account testcopystore
	info:    The storage URI 'https://testcopystore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
	+ Creating VM "redhatcopy"
	info:    vm create command OK

La VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali**.

Connettersi alla nuova macchina virtuale con un client SSH a scelta e usare le credenziali dell'account della macchina virtuale originale, ad esempio `ssh OldAdminUser@<IPaddressOfYourNewVM>`. Per altre informazioni, vedere [Come usare SSH con Linux in Azure](virtual-machines-linux-ssh-from-linux.md).


## Passaggi successivi

Per altre informazioni su come usare l'interfaccia della riga di comando di Azure per gestire la nuova macchina virtuale, vedere [Comandi dell'interfaccia della riga di comando Azure per Azure Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0525_2016-->