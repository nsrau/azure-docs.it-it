<properties
	pageTitle="Caricare un disco rigido virtuale Windows per Gestione risorse | Microsoft Azure"
	description="Informazioni su come caricare un'immagine di macchina virtuale Windows da usare con il modello di distribuzione Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="cynthn"/>

# Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager


Questo articolo mostra come caricare un disco rigido virtuale (VHD, Virtual Hard Disk) con un sistema operativo Windows, in modo che sia possibile usarlo per creare nuove macchine virtuali (VM) Windows mediante il modello di distribuzione Azure Resource Manager. Per informazioni dettagliate sui dischi e sui dischi rigidi virtuali in Azure, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md).



## Prerequisiti

Questo articolo presuppone che l'utente abbia:

- **Una sottoscrizione di Azure**: se non si ha una sottoscrizione, [aprire un account di Azure gratuito](/pricing/free-trial/?WT.mc_id=A261C142F) e [attivare i vantaggi per i titolari di sottoscrizioni MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- **Azure PowerShell versione 1.0 o successiva**: se la soluzione non è già installata, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

- **Una macchina virtuale che esegue Windows**: sono disponibili numerosi strumenti per la creazione di macchine virtuali in locale. Vedere ad esempio [Installare Hyper-V e creare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx). Per conoscere i sistemi operativi Windows supportati da Azure, vedere [Supporto di software server Microsoft per le macchine virtuali Microsoft Azure](https://support.microsoft.com/kb/2721672).


## Verificare che la VM abbia il formato di file corretto

Azure può accettare solo le immagini di [macchine virtuali di generazione 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) salvate nel formato di file VHD. La dimensione del disco rigido virtuale deve essere fissa e corrispondere a un numero intero di megabyte, ovvero un numero divisibile per 8. La dimensione massima consentita per il disco rigido virtuale è 1023 GB.

- Se si ha un'immagine di VM Windows in formato VHDX, convertirla in formato VHD con uno dei metodi seguenti:

	- Hyper-V: aprire Hyper-V e selezionare il computer locale a sinistra. Nel menu superiore fare quindi clic su **Azione** > **Modifica disco**. Spostarsi tra le schermate facendo clic su **Avanti** e immettendo le opzioni seguenti: *Percorso del file VHDX* > **Converti** > **VHD** > **A dimensione fissa** > *Percorso del nuovo file VHD*. Fare clic su **Fine** per chiudere.

	- [Cmdlet di PowerShell Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx): leggere il post di blog relativo alla [conversione dei formati di file Hyper-V VHDX in VHD](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) per altre informazioni.

- Se si ha un'immagine di VM Windows in [formato di file VMDK](https://en.wikipedia.org/wiki/VMDK), convertirla in formato VHD usando [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Per altre informazioni, leggere l'argomento del blog relativo a [come convertire un file VMDK VMWare in un file VHD Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).


## Preparare il disco rigido virtuale per il caricamento

Questa sezione illustra come generalizzare la macchina virtuale di Windows. Questa operazione rimuove anche tutte le informazioni sull'account personale. Questa operazione viene solitamente eseguita quando si vuole usare questa immagine di VM per distribuire rapidamente macchine virtuali simili. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

1. Accedere alla macchina virtuale Windows.

2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\\system32\\sysprep**, quindi eseguire `sysprep.exe`.

3. Nella finestra di dialogo **Utilità preparazione sistema** seguire questa procedura:

	1. In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.

	2. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

	3. Fare clic su **OK**.

	![Avvio di Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br> <a id="createstorage"></a>
## Creare o individuare un account di archiviazione di Azure

Per caricare l'immagine della VM è necessario un account di archiviazione di Azure. È possibile usare un account di archiviazione esistente o crearne uno nuovo. A tale scopo, è possibile usare il portale di Azure o PowerShell.

### Per creare o trovare un account di archiviazione di Azure usando il portale di Azure

1. Accedere al [portale](https://portal.azure.com).

2. Fare clic su **Sfoglia** > **Account di archiviazione**.

3. Verificare se è disponibile un account di archiviazione da usare per il caricamento dell'immagine. Annotare il nome dell'account di archiviazione. Se si usa un account di archiviazione esistente, è possibile passare alla sezione [Caricare l'immagine della VM](#uploadvm).

4. Se si vuole creare un nuovo account di archiviazione, fare clic su **Aggiungi** e immettere le informazioni seguenti:

	1. Immettere il **Nome** dell'account di archiviazione. Tale nome dovrebbe essere di lunghezza compresa tra 3 e 24 caratteri e contenere soltanto lettere minuscole e numeri. Questo nome diventa parte dell'URL che verrà usato per accedere a BLOB, file e altre risorse dall'account di archiviazione.
	
	2. Selezionare *Resource Manager* come **Modello di distribuzione**.

	3. Selezionare i valori appropriati per **Tipologia account**, **Prestazioni** e **Replica**. È possibile passare il puntatore sulle icone delle informazioni per ottenere maggiori informazioni su questi valori.

	4. Selezionare *+ Nuovo* per **Gruppo di risorse** oppure scegliere un gruppo di risorse esistente. Immettere il nome del nuovo gruppo di risorse nel caso in cui se ne voglia creare uno.

	5. Scegliere la posizione dell'account di archiviazione. e fare clic su **Crea**. L'account verrà visualizzato nel pannello **Account di archiviazione**.

		![Immissione dei dettagli dell'account di archiviazione](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. Questo passaggio e quelli successivi mostrano come creare un contenitore BLOB nell'account di archiviazione. La procedura è facoltativa, perché per creare un nuovo contenitore BLOB per l'immagine è anche possibile usare il comando di PowerShell per il caricamento dell'immagine. Se non si vuole creare il contenitore, passare alla sezione [Caricare l'immagine della VM](#uploadvm). In caso contrario, fare clic su **BLOB** nel riquadro **Servizi**.

		![Servizio BLOB](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. Quando viene visualizzato il pannello BLOB, fare clic su **+ Contenitore** per creare un nuovo contenitore di archiviazione BLOB. Immettere il nome del contenitore e il tipo di accesso.

		![Creazione di un nuovo BLOB](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] Per impostazione predefinita, il contenitore è privato ed è accessibile solo al proprietario dell'account. Per concedere l'autorizzazione di lettura pubblica per i BLOB presenti nel contenitore, ma non per le proprietà e i metadati del contenitore, usare l'opzione **BLOB**. Per concedere l'autorizzazione di lettura completa per il contenitore e i BLOB, usare l'opzione **Contenitore**.

	8. Il nuovo contenitore BLOB verrà elencato nel pannello **Servizio BLOB**. Annotare l'URL del contenitore, che sarà necessario per consentire al comando di PowerShell di caricare l'immagine. A seconda della lunghezza dell'URL e della risoluzione dello schermo, l'URL potrebbe essere parzialmente nascosto. In tal caso,ingrandire il pannello facendo clic sull'icona **Ingrandisci** nell'angolo superiore destro.


### Per creare o trovare un account di archiviazione di Azure usando PowerShell

1. Aprire Azure PowerShell 1.0.x e accedere al proprio account di Azure.

		Login-AzureRmAccount

	Questo comando apre una finestra popup in cui è possibile immettere le credenziali di Azure.

2. Se l'ID sottoscrizione selezionato per impostazione predefinita è diverso da quello che si vuole usare, impostare la sottoscrizione appropriata con uno dei comandi seguenti.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	oppure

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	È possibile trovare le sottoscrizioni dell'account di Azure usando il comando `Get-AzureRmSubscription`.

3. Trovare gli account di archiviazione disponibili per la sottoscrizione.

		Get-AzureRmStorageAccount

	Se si vuole usare un account di archiviazione esistente, passare alla sezione [Caricare l'immagine della VM](#uploadvm).

4. Se si vuole creare un nuovo account di archiviazione per l'immagine, seguire questa procedura:

	1. Assicurarsi di avere un gruppo di risorse per l'account di archiviazione. Trovare tutti i gruppi di risorse inclusi nella sottoscrizione usando il comando seguente:

			Get-AzureRmResourceGroup

	2. Se si vuole creare un nuovo gruppo di risorse, usare il comando seguente:

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. Creare un nuovo account di archiviazione nel gruppo di risorse usando il comando seguente:

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>

## Caricare l'immagine della VM nell'account di archiviazione

Per caricare l'immagine della VM nell'account di archiviazione, seguire questa procedura in Azure PowerShell. L'immagine verrà caricata in un contenitore di archiviazione BLOB in questo account. È possibile usare un contenitore esistente o crearne uno nuovo.

1. Accedere ad Azure PowerShell 1.0.x con il comando `Login-AzureRmAccount`. Assicurarsi di usare la sottoscrizione corretta mediante il comando `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`, come indicato nella sezione precedente.

2. Aggiungere il disco rigido virtuale generalizzato di Azure all'account di archiviazione usando il cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx):

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	Dove:
	- **StorageAccountURL** è l'URL dell'account di archiviazione. In genere sarà nel formato seguente: `https://YourStorageAccountName.blob.core.windows.net`. Si noti che sarà necessario usare il nome dell'account di archiviazione nuovo o esistente al posto di *YourStorageAccountName*.
	- **BlobContainer** corrisponde al contenitore BLOB in cui archiviare le immagini. Se il cmdlet non trova un contenitore BLOB esistente con lo stesso nome, ne creerà uno nuovo per l'utente.
	- **TargetVHDName** è il nome con cui si vuole salvare l'immagine.
	- **LocalPathOfVHDFile** indica il percorso completo e il nome del file VHD nel computer locale.

	Se l'esecuzione di `Add-AzureRmVhd` avviene correttamente, il risultato sarà simile al seguente:

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	Il completamento del comando può richiedere diverso tempo, a seconda della connessione di rete e delle dimensioni del file con estensione vhd.

</br>
## Distribuire una nuova VM dall'immagine caricata

È ora possibile usare l'immagine caricata per creare una nuova VM di Windows. I passaggi seguenti mostrano come usare Azure PowerShell e l'immagine della VM caricata nei passaggi precedenti per creare una VM in una nuova rete virtuale.

>[AZURE.NOTE] L'immagine della VM deve essere presente nello stesso account di archiviazione della macchina virtuale effettiva che verrà creata.

### Creare risorse di rete

Usare lo script di esempio PowerShell seguente per impostare una rete virtuale e una scheda di interfaccia di rete per la nuova VM. Usare i valori per le variabili, rappresentate da **$**, nel modo appropriato per l'applicazione.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Creare una nuova macchina virtuale

Lo script di PowerShell seguente illustra come impostare le configurazioni della macchina virtuale e usare l'immagine della VM caricata come origine per la nuova installazione. </br>

	#Enter a new user name and password in the pop-up window for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Il flusso di lavoro potrebbe ad esempio essere simile al seguente:

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

La VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** oppure usando i comandi di PowerShell seguenti:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Passaggi successivi

Per gestire la nuova macchina virtuale con Azure PowerShell, vedere l'articolo relativo a come [gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0525_2016-->