<properties
	pageTitle="Creare un'immagine di VM da una VM di Azure | Microsoft Azure"
	description="Informazioni su come creare un'immagine di VM generalizzata da una VM di Azure esistente creata nel modello di distribuzione di Resource Manager"
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
	ms.date="08/04/2016"
	ms.author="cynthn"/>  

# Come creare un'immagine di VM da una VM di Azure esistente


Questo articolo illustra come usare Azure PowerShell per creare un'immagine generalizzata di una VM di Azure esistente. È quindi possibile usare l'immagine per creare un'altra VM. Questa immagine include il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. L'immagine non include le risorse della rete virtuale, quindi è necessario configurare queste risorse quando si crea una VM usando l'immagine. Questo processo consente di creare un'[immagine generalizzata di Windows](https://technet.microsoft.com/library/hh824938.aspx).


## Prerequisiti

- Questa procedura presuppone che sia già disponibile una macchina virtuale di Azure nel modello di distribuzione di Resource Manager, da usare per creare l'immagine. È necessario conoscere il nome della VM e il nome del gruppo di risorse. È possibile ottenere un elenco dei gruppi di risorse nella sottoscrizione tramite il cmdlet di PowerShell `Get-AzureRmResourceGroup`. È possibile ottenere un elenco delle VM nella sottoscrizione digitando `Get-AzureRMVM`.

- È necessario aver installato Azure PowerShell 1.0.x. Se non è già stato installato PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per la procedura di installazione.

## Preparare la VM di origine 

Questa sezione illustra come generalizzare la macchina virtuale di Windows in modo da poterla usare come immagine.

> [AZURE.WARNING] Non è possibile accedere alla VM tramite RDP dopo la generalizzazione, perché il processo rimuove tutti gli account utente. Le modifiche sono irreversibili.

1. Accedere alla macchina virtuale di Windows. Nel [Portale di Azure](https://portal.azure.com) selezionare **Sfoglia** > **Macchine virtuali** > macchina virtuale di Windows personale > **Connetti**.

2. Aprire una finestra del Prompt dei comandi come amministratore.

3. Impostare la directory su `%windir%\system32\sysprep`, quindi eseguire sysprep.exe.

4. Nella finestra di dialogo **Utilità preparazione sistema** seguire questa procedura:

	- In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione **Generalizza** sia selezionata. Per ulteriori informazioni sull'utilizzo di Sysprep, vedere il relativo [documento introduttivo](http://technet.microsoft.com/library/bb457073.aspx).

	- In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

	- Fare clic su **OK**.

	![Eseguire Sysprep.](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   Sysprep arresta la macchina virtuale. Lo stato viene modificato in **Arrestato** nel portale di Azure.


## Accedere ad Azure PowerShell

1. Aprire Azure PowerShell e accedere al proprio account di Azure.

		Login-AzureRmAccount

	Verrà visualizzata una finestra popup in cui immettere le credenziali dell'account Azure.

2. Ottenere l'ID di sottoscrizione per le sottoscrizioni disponibili.

		Get-AzureRmSubscription

3. Impostare la sottoscrizione corretta utilizzandone l'ID.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"


## Deallocare la VM e impostare lo stato generalizzato		

1. Deallocare le risorse della VM.

		Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>

	Lo *Stato* della VM nel portale di Azure cambia da **Arrestato** a **Arrestato (deallocato)**.

2. Impostare lo stato della macchina virtuale su **Generalizzato**.

		Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized

3. Controllare lo stato della VM. Nella sezione **OSState/generalized** per la VM, il valore **DisplayStatus** dovrebbe essere impostato su **Macchina virtuale generalizzata**.
		
		$vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -status
		$vm.Statuses

		
## Creare l'immagine 

1. Copiare l'immagine della macchina virtuale nel contenitore di archiviazione di destinazione usando questo comando. L'immagine viene creata nello stesso account di archiviazione della macchina virtuale originale. La variabile `-Path` salva una copia del modello JSON in locale. La variabile `-DestinationContainerName` è il nome del contenitore in cui si vuole salvare le immagini. Se il contenitore non esiste, verrà creato.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	È possibile ottenere l'URL dell'immagine dal modello del file JSON. Passare alla sezione **resources** > **storageProfile** > **osDisk** > **image** > **uri** per il percorso completo dell'immagine. L'URL dell'immagine è simile a questo: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
	
	È anche possibile verificare l'URI nel portale. L'immagine viene copiata in un BLOB denominato **system** nell'account di archiviazione.

2. Creare una variabile per il percorso all'immagine.

		$imageURI = "<https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd>"


## Crea rete virtuale

Creare la rete virtuale e la subnet della [rete virtuale](../virtual-network/virtual-networks-overview.md).
		

1. Sostituire il valore delle variabili con le informazioni personalizzate. Specificare il prefisso dell'indirizzo della subnet nel formato CIDR. Creare le variabili e la subnet.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Sostituire il valore di **$vnetName** con il nome della rete virtuale. Specificare il prefisso dell'indirizzo della rete virtuale nel formato CIDR. Creare la variabile e la rete virtuale con la subnet.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Creare un indirizzo IP pubblico e un'interfaccia di rete

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Sostituire il valore di **$ipName** con un nome per l'indirizzo IP pubblico. Creare la variabile e l'indirizzo IP pubblico.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Sostituire il valore di **$nicName** con un nome per l'interfaccia di rete. Creare la variabile e l'interfaccia di rete.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


## Creare la VM

Lo script di PowerShell seguente illustra come impostare le configurazioni della macchina virtuale e usare l'immagine della VM caricata come origine per la nuova installazione.

>[AZURE.NOTE] La VM deve trovarsi nello stesso account di archiviazione del disco rigido virtuale originale.

</br>  

	
	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account 
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"
	
	# Assign a SKU name
	# Valid values for -SkuName are: **Standard_LRS** - locally redundant storage, **Standard_ZRS** - zone redundant storage, **Standard_GRS** - geo redundant storage, **Standard_RAGRS** - read access geo redundant storage, **Premium_LRS** - premium locally redundant storage. 
	$skuName = "<skuName>"
	
	# Create a new storage account for the VM
	New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $storageAccName -Location $location -SkuName $skuName -Kind "Storage"

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
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $imageURI -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



Al termine, la VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** oppure usando i comandi di PowerShell seguenti:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Passaggi successivi

Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [Gestire macchine virtuali di Azure con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0810_2016-->