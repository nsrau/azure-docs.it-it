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
	ms.date="08/02/2016"
	ms.author="cynthn"/>

# Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager


In questo articolo viene illustrato come creare e caricare un'immagine di disco rigido virtuale (VHD) di Windows per creare VM rapidamente. Per informazioni dettagliate sui dischi e sui dischi rigidi virtuali in Azure, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md).


## Prerequisiti

Questo articolo presuppone che l'utente abbia:

- **Una sottoscrizione di Azure**: se non si ha una sottoscrizione, [aprire un account di Azure gratuito](/pricing/free-trial/?WT.mc_id=A261C142F) e [attivare i vantaggi per i titolari di sottoscrizioni MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- **Azure PowerShell versione 1.4 o successiva**: se la soluzione non è già installata, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

- **Una macchina virtuale che esegue Windows**: sono disponibili numerosi strumenti per la creazione di macchine virtuali in locale. Vedere ad esempio [Installare Hyper-V e creare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx). Per informazioni sui sistemi operativi Windows supportati in Azure, vedere l'articolo sul [supporto di software server Microsoft per le macchine virtuali di Microsoft Azure](https://support.microsoft.com/kb/2721672).

- Assicurarsi che i ruoli server in esecuzione sulla macchina supportino Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).


## Verificare che il formato di file della VM sia corretto

In Azure è possibile utilizzare solo [macchine virtuali di prima generazione](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) con formato di file VHD. Il disco rigido virtuale deve avere dimensione fissa e corrispondere a un numero intero di megabyte, ovvero un numero divisibile per 8. La dimensione massima consentita per il disco rigido virtuale è 1023 GB.

- Se si ha un'immagine di VM Windows in formato VHDX, convertirla in formato VHD con uno dei metodi seguenti:

	- Hyper-V: aprire Hyper-V e selezionare il computer locale a sinistra. Nel menu superiore fare quindi clic su **Azione** > **Modifica disco**. Spostarsi tra le schermate facendo clic su **Avanti** e immettendo le opzioni seguenti: *Percorso del file VHDX* > **Converti** > **VHD** > **A dimensione fissa** > *Percorso del nuovo file VHD*. Fare clic su **Fine** per chiudere.

	- [Cmdlet di PowerShell Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx): leggere il post di blog relativo alla [conversione dei formati di file Hyper-V VHDX in VHD](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) per altre informazioni.

- Se si ha un'immagine di VM Windows in [formato di file VMDK](https://en.wikipedia.org/wiki/VMDK), convertirla in formato VHD usando [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Per altre informazioni, leggere l'argomento del blog relativo a [come convertire un file VMDK VMWare in un file VHD Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).


## Preparare il disco rigido virtuale per il caricamento

Questa sezione illustra come generalizzare la macchina virtuale di Windows. Sysprep rimuove anche tutte le informazioni sull'account personale. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

1. Accedere alla macchina virtuale Windows.

2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\\system32\\sysprep**, quindi eseguire `sysprep.exe`.

3. Nella finestra di dialogo **Utilità preparazione sistema** seguire questa procedura:

	1. In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.

	2. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

	3. Fare clic su **OK**.

	![Avvio di Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>


## Accedere ad Azure

1. Aprire Azure PowerShell e accedere al proprio account di Azure.

		Login-AzureRmAccount

	Verrà visualizzata una finestra popup in cui immettere le credenziali dell'account Azure.

2. Ottenere l'ID di sottoscrizione per le sottoscrizioni disponibili.

		Get-AzureRmSubscription

3. Impostare la sottoscrizione corretta utilizzandone l'ID.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"

	
## Ottenere l'account di archiviazione

Per ospitare l'immagine della VM caricata, è necessario un account di archiviazione di Azure. È possibile usare un account di archiviazione esistente o crearne uno nuovo.

Mostra gli account di archiviazione disponibili.

		Get-AzureRmStorageAccount

Se si vuole usare un account di archiviazione esistente, passare alla sezione [Caricare l'immagine della VM](#upload-the-vm-image-to-your-storage-account).

Se si vuole creare un account di archiviazione, seguire questa procedura:

1. Assicurarsi di avere un gruppo di risorse per l'account di archiviazione. Trovare tutti i gruppi di risorse inclusi nella sottoscrizione usando il comando seguente:

		Get-AzureRmResourceGroup

2. Per creare un gruppo di risorse, usare il comando seguente:

		New-AzureRmResourceGroup -Name <resourceGroupName> -Location <location>

3. Creare un account di archiviazione in questo gruppo di risorse con il cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx).

		New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Location "<location>" -SkuName "<skuName>" -Kind "Storage"
			
I valori validi -SkuName validi sono:

- **Standard\_LRS**: archiviazione con ridondanza locale.
- **Standard\_ZRS**: archiviazione con ridondanza della zona.
- **Standard\_GRS**: archiviazione con ridondanza geografica.
- **Standard\_RAGRS**: archiviazione con ridondanza geografica e accesso in lettura.
- **Premium\_LRS**: archiviazione con ridondanza locale Premium.



## Caricare l'immagine della VM nell'account di archiviazione

Utilizzare il cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) per caricare l'immagine in un contenitore nell'account di archiviazione:

		$rgName = "<resourceGroupName>"
		$urlOfUploadedImageVhd = "<storageAccount>/<blobContainer>/<targetVHDName>.vhd"
		Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath <localPathOfVHDFile>

Dove:

- **storageAccount** è il nome dell'account di archiviazione dell'immagine.

- **blobContainer** è il contenitore BLOB in cui si desidera archiviare l'immagine. Se non esiste ancora un contenitore BLOB con questo nome, ne viene creato uno automaticamente.

- **targetVHDName** è il nome che si desidera utilizzare per il file VHD caricato.

- **localPathOfVHDFile** è il percorso completo e il nome del file VHD nel computer locale.


Se l'operazione riesce, si ottiene una risposta simile alla seguente:

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

L'esecuzione del comando potrebbe richiedere del tempo, a seconda della connessione di rete e delle dimensioni del file VHD.




## Crea rete virtuale

Creare la rete virtuale e la subnet della [rete virtuale](../virtual-network/virtual-networks-overview.md).

1. Sostituire il valore delle variabili con le informazioni personalizzate. Specificare il prefisso dell'indirizzo della subnet nel formato CIDR. Creare le variabili e la subnet.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Sostituire il valore di **$vnetName** con il nome della rete virtuale. Specificare il prefisso dell'indirizzo della rete virtuale nel formato CIDR. Creare la variabile e la rete virtuale con la subnet.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Creare un indirizzo IP pubblico e un'interfaccia di rete

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Sostituire il valore di **$ipName** con un nome per l'indirizzo IP pubblico. Creare la variabile e l'indirizzo IP pubblico.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
        
2. Sostituire il valore di **$nicName** con un nome per l'interfaccia di rete. Creare la variabile e l'interfaccia di rete.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

		

## Creare la VM

Lo script di PowerShell seguente illustra come impostare le configurazioni della macchina virtuale e usare l'immagine della VM caricata come origine per la nuova installazione.

>[AZURE.NOTE] La VM deve trovarsi nello stesso account di archiviazione del file VHD caricato.

</br>

	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account where the VHD file is and where the OS disk will be created
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You set this variable when you uploaded the VHD
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



Al termine, la VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** oppure usando i comandi di PowerShell seguenti:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Passaggi successivi

Per gestire la nuova macchina virtuale con Azure PowerShell, vedere l'articolo relativo a come [gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0817_2016-->