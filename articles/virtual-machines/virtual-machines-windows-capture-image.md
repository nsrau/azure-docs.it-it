<properties
	pageTitle="Acquisire una VM di Windows in Gestione risorse | Microsoft Azure"
	description="Informazioni su come acquisire l'immagine di una VM di Azure basata su Windows creata con il modello di distribuzione di Gestione risorse di Azure."
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
	ms.date="01/29/2016"
	ms.author="dkshir"/>


# Come acquisire una macchina virtuale di Windows nel modello di distribuzione di Gestione risorse

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-capture-image.md).


Questo articolo illustra come usare Azure PowerShell per acquisire una macchina virtuale di Azure che esegue Windows, in modo da usarla per creare altre macchine virtuali. Questa immagine include il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. Non include le risorse della rete virtuale necessarie per creare una VM di Windows, quindi nella maggior parte dei casi è necessario configurarle prima di creare un'altra macchina virtuale che usa l'immagine. Questa immagine verrà anche preparata per essere un'[immagine generalizzata di Windows](https://technet.microsoft.com/library/hh824938.aspx).


## Prerequisiti

Questa procedura presuppone che sia già stata creata una macchina virtuale di Azure nel modello di distribuzione di Gestione risorse e che sia stato configurato il sistema operativo, incluse le connessioni di eventuali dischi dati e l'applicazione di altre personalizzazioni, ad esempio l'installazione di applicazioni. Se queste operazioni non sono state eseguite, vedere l'articolo relativo a [come creare una macchina virtuale Windows con Resource Manager e PowerShell](virtual-machines-windows-ps-create.md). È possibile creare facilmente una macchina virtuale di Windows usando il [portale di Azure](https://portal.azure.com). Vedere l'articolo relativo a [come creare una macchina virtuale Windows nel portale di Azure](virtual-machines-windows-hero-tutorial.md).


## Preparare la VM per l'acquisizione di immagini

Questa sezione illustra come generalizzare la macchina virtuale di Windows. Questa operazione rimuove, fra le altre cose, tutte le informazioni sull'account personale. Questa operazione viene solitamente eseguita quando si vuole usare questa immagine di VM per distribuire rapidamente macchine virtuali simili.

1. Accedere alla macchina virtuale di Windows. Nel [portale di Azure](https://portal.azure.com) selezionare **Sfoglia** > **Macchine virtuali** > macchina virtuale di Windows personale > **Connetti**.

2. Aprire una finestra del Prompt dei comandi come amministratore.

3. Impostare la directory su `%windir%\system32\sysprep`, quindi eseguire sysprep.exe.

4. Nella finestra di dialogo **Utilità preparazione sistema** eseguire le operazioni seguenti:

	- In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione **Generalizza** sia selezionata. Per ulteriori informazioni sull'utilizzo di Sysprep, vedere il relativo [documento introduttivo](http://technet.microsoft.com/library/bb457073.aspx).

	- In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

	- Fare clic su **OK**.

	![Eseguire Sysprep.](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

5.	Sysprep arresta la macchina virtuale. Lo stato viene modificato in **Arrestato** nel portale di Azure.


</br>
## Acquisire la VM

È possibile acquisire la VM generalizzata di Windows usando Azure PowerShell o il nuovo strumento Esplora risorse di Gestione risorse di Azure. Questa sezione illustra la procedura per entrambi.

### Tramite PowerShell

Questo articolo presuppone che sia installata la versione 1.0.x di Azure PowerShell. È consigliabile usare questa versione, in quanto le nuove funzionalità di Gestione risorse non verranno aggiunte alle versioni precedenti di PowerShell. Leggere [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) per altre informazioni sulle differenze tra le versioni.

1. Aprire Azure PowerShell 1.0.x e accedere al proprio account Azure.

		Login-AzureRmAccount

	Questo comando apre una finestra popup per immettere le credenziali di Azure.

2. Se l'ID della sottoscrizione selezionato per impostazione predefinita è diverso da quello che si vuole usare, impostare la sottoscrizione appropriata tramite uno dei comandi seguenti.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	oppure

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	È possibile trovare le sottoscrizioni dell'account Azure tramite il comando `Get-AzureRmSubscription`.

3. A questo punto è necessario deallocare le risorse usate dalla macchina virtuale.

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	Si noti che lo stato della VM nel portale di Azure è cambiato da **Arrestato** a **Arrestato (deallocato)**.

	>[AZURE.TIP] È anche possibile trovare lo stato della macchina virtuale in PowerShell usando:</br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br> Il campo **DisplayStatus** corrisponde allo **Stato** visualizzato nel portale di Azure.

4. È quindi necessario impostare lo stato della macchina virtuale su _Generalizzato_. Questa operazione è necessaria, perché il passaggio di generalizzazione precedente (`sysprep`) non esegue l'operazione in modo comprensibile da parte di Azure.

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] Lo stato generalizzato come impostato in precedenza non viene visualizzato nel portale. È tuttavia possibile verificarlo tramite il comando Get-AzureRmVM, come illustrato nel suggerimento precedente.

5. Acquisire l'immagine della macchina virtuale in un contenitore di archiviazione di destinazione usando questo comando.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	La variabile `-Path` è facoltativa e può essere usata per salvare il modello JSON in locale. La variabile `-DestinationContainerName` è il nome del contenitore in cui si vuole salvare le immagini. L'URL dell'immagine archiviata è simile a `https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`. Viene creato nello stesso account di archiviazione della macchina virtuale originale.

	>[AZURE.NOTE] Per trovare la posizione dell'immagine, aprire il modello di file JSON locale. Andare alla sezione **resources** > **storageProfile** > **osDisk** > **image** > **uri** per il percorso completo dell'immagine. Al momento non esiste un modo semplice per controllare queste immagini nel portale, poiché il contenitore del _sistema_ nell'account di archiviazione è nascosto. Per questo motivo, anche se la variabile `-Path` è facoltativa, è consigliabile usarla per salvare il modello in locale e trovare facilmente l'URL dell'immagine. In alternativa, trovarlo usando uno strumento denominato **Esplora archivi di Azure**, descritto nella sezione successiva.


### Uso di Esplora risorse di Azure (anteprima)

[Esplora risorse di Azure (anteprima)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/) è un nuovo strumento sviluppato per gestire le risorse di Azure create nel modello di distribuzione di Gestione risorse. Questo strumento consente di trovare

- facilmente le API di Gestione risorse di Azure e
- la documentazione delle API, nonché di
- eseguire chiamate API direttamente nelle proprie sottoscrizioni di Azure.

Per altre informazioni sulle operazioni che è possibile eseguire con questo potente strumento, guardare il video in [Esplora risorse di Azure con David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo).

È possibile usare Esplora risorse per acquisire la macchina virtuale, in alternativa al metodo PowerShell.

1. Aprire il [sito Web di Esplora risorse](https://resources.azure.com/) e accedere al proprio account Azure.

2. Nella parte superiore destra dello strumento selezionare **Lettura/Scrittura** per consentire le operazioni _PUT_ e _POST_. L'impostazione predefinita è **Sola lettura** e questo indica che per impostazione predefinita è possibile eseguire solo operazioni _GET_.

	![Lettura/Scrittura in Esplora risorse](./media/virtual-machines-windows-capture-image/ArmExplorerReadWrite.png)

3. Trovare ora la macchina virtuale di Windows. È possibile digitare il nome nella _casella di ricerca_ nella parte superiore dello strumento o selezionare le opzioni seguenti nel menu a sinistra: **sottoscrizioni** > sottoscrizione Azure > **resourceGroups** > gruppo di risorse > **provider** > **Microsoft.Compute** > **virtualMachines** > macchina virtuale di Windows personale. Quando si fa clic sulla macchina virtuale nel riquadro di spostamento sinistro, viene visualizzato il relativo modello sul lato destro dello strumento.

4. Nella parte superiore destra della pagina del modello vengono visualizzate schede per le varie operazioni disponibili per questa macchina virtuale. Fare clic sulla scheda per **Azioni (POST/DELETE)**.

	![Menu Azione di Esplora risorse](./media/virtual-machines-windows-capture-image/ArmExplorerActionMenu.png)

5. Viene visualizzato un elenco di tutte le azioni che è possibile eseguire nella macchina virtuale.

	![Elementi del menu Azione di Esplora risorse](./media/virtual-machines-windows-capture-image/ArmExplorerActionItems.png)

6. Deallocare la macchina virtuale facendo clic sul pulsante di azione per **deallocare**. Lo stato della VM viene modificato da **Arrestato** ad **Arrestato (deallocato)**.

7. Contrassegnare la macchina virtuale come generalizzata facendo clic sul pulsante di azione per **generalizzare**. È possibile verificare le modifiche dello stato facendo clic sul menu **InstanceView** sotto il nome della macchina virtuale sul lato sinistro e passando alla sezione **stati** sul lato destro.

8. Sotto il pulsante di azione **acquisisci**, impostare i valori per l'acquisizione dell'immagine. I valori compilati possono avere un aspetto simile al seguente.

	![Acquisizione in Esplora risorse](./media/virtual-machines-windows-capture-image/ArmExplorerCaptureAction.png)

	Fare clic sul pulsante di azione **acquisisci** per acquisire l'immagine della macchina virtuale. Questo crea un nuovo disco rigido virtuale per l'immagine, nonché un file di modello JSON, che al momento non sono accessibili tramite Esplora risorse o il [portale di Azure](https://portal.azure.com).

9. Per accedere al nuovo disco rigido virtuale dell'immagine e al modello, scaricare e installare lo strumento di Azure per la gestione delle risorse di archiviazione, [Esplora archivi di Azure](http://storageexplorer.com/). Esplora archivi di Azure viene installato localmente nel computer in uso.

	- Aprire Esplora archivi e accedere alla sottoscrizione di Azure. Vengono visualizzati tutti gli account di archiviazione disponibili per la sottoscrizione.

	- Sul lato sinistro viene visualizzato l'account di archiviazione della macchina virtuale acquisita nei passaggi precedenti. Fare doppio clic sul menu **sistema** sottostante. Vengono visualizzati i contenuti della cartella **sistema** sul lato destro.

		![Sistema di Esplora archivi](./media/virtual-machines-windows-capture-image/StorageExplorer1.png)

	- Fare doppio clic su **Microsoft.Compute** e quindi su **Immagini** per visualizzare tutte le cartelle di immagini. Fare doppio clic sul nome della cartella immesso per la variabile **destinationContainerName** durante l'acquisizione dell'immagine da Esplora risorse. Viene visualizzato sia il disco rigido virtuale che il file di modello JSON.

	- Da qui è possibile trovare l'URL o scaricare il disco rigido virtuale o il modello facendo clic con il pulsante destro del mouse.

		![Modello di Esplora archivi](./media/virtual-machines-windows-capture-image/StorageExplorer2.png)


## Distribuire una nuova VM dall'immagine acquisita

È ora possibile usare l'immagine acquisita per creare una nuova VM di Windows. Questi passaggi mostrano come usare Azure PowerShell e l'immagine della VM acquisita nei passaggi precedenti per creare la VM in una nuova rete virtuale.

>[AZURE.NOTE] L'immagine della VM deve essere presente nello stesso account di archiviazione della macchina virtuale effettiva che verrà creata.

### Creare risorse di rete

Usare lo script di esempio PowerShell seguente per impostare una rete virtuale e una scheda di interfaccia di rete per la nuova VM. Usare i valori per le variabili rappresentate dal segno **$** come appropriato per l'applicazione.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Creare una nuova VM

Lo script PowerShell seguente illustra come impostare le configurazioni della macchina virtuale e usare l'immagine della VM acquisita come origine per la nuova installazione. </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

La VM appena creata viene visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** OPPURE usando i comandi PowerShell seguenti:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Passaggi successivi

Per gestire la nuova macchina virtuale con Azure PowerShell, vedere l'articolo relativo alla [gestione di macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0323_2016-->