<properties title="Create a Virtual Machine Running Windows Server" pageTitle="Come creare una macchina virtuale che esegue Windows Server" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Creare una macchina virtuale che esegue Windows Server #

Questa esercitazione illustra come creare una macchina virtuale di Azure che esegue Windows Server usando la Raccolta immagini presente nel portale di gestione di Windows Azure. Nella Raccolta immagini è disponibile un'ampia gamma di immagini, ad esempio immagini di sistemi operativi Windows, sistemi operativi basati su Linux e di applicazioni. 

> [WACOM.NOTE] Per completare questa esercitazione, non è necessario essere esperti di macchine virtuali di Azure. È invece richiesto un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Creare un account Azure](http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/). 

In questa esercitazione si apprenderà come:

- [Come creare la macchina virtuale](#createvirtualmachine)
- [Come accedere alla macchina virtuale dopo averla creata](#logon)
- [Come collegare un disco dati alla nuova macchina virtuale](#attachdisk)

Per altre informazioni, vedere [Macchine virtuali](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Come creare la macchina virtuale##

Questa sezione illustra come usare l'opzione **Da raccolta** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida**. Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta**.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Come accedere alla macchina virtuale dopo averla creata ##

Questa sezione descrive come accedere alla macchina virtuale per gestirne le impostazioni e le applicazioni che verranno eseguite al suo interno.

1. Accedere al [portale di gestione](http://manage.windowsazure.com) di Azure.

2. Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale **MyTestVM**.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. Sulla barra dei comandi fare clic su **Connetti**.

	![Connect to MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. Fare clic su **Apri** per usare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale.

	![Open the rdp file](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. Fare clic su **Connetti**.

	![Continue with connecting](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. Nella casella per l'immissione della password digitare il nome utente e la password specificati al momento della creazione della macchina virtuale, quindi fare clic su **OK**.

7. Fare clic su **Sì** per verificare l'identità della macchina virtuale.

	![Verify the identity of the machine](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con un altro server dell'ufficio.

## <a id="attachdisk"> </a>Come collegare un disco dati alla nuova macchina virtuale ##

Questa sezione descrive come collegare un disco dati vuoto alla macchina virtuale. Per altre informazioni su come collegare dischi vuoti e dischi esistenti, vedere l'esercitazione [Collegamento di un disco dati] (http://www.windowsazure.com/it-it/documentation/articles/storage-windows-attach-disk/).

1. Accedere al [portale di gestione](http://manage.windowsazure.com) di Azure.

2. Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale **MyTestVM**.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. L'utente potrebbe essere indirizzato alla pagina Avvio rapido. In questo caso, fare clic su **Dashboard** nella parte superiore.

	![Select Dashboard](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. Sulla barra dei comandi fare clic su **Connetti** e quindi su **Connetti disco vuoto**.

	![Select Attach from the command bar](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)	

5. Le impostazioni **Nome macchina virtuale**, **Posizione di memorizzazione**, **Nome file** e **Preferenze cache dell'host** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Digitare **5** nel campo **Dimensione**. Fare quindi clic sul segno di spunta per connettere il disco vuoto alla macchina virtuale.

	![Specify the size of the empty disk](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)	
	
	>[WACOM.NOTE] tutti i dischi vengono creati da un file VHD in Archiviazione di Microsoft Azure. In **Nome file** è possibile specificare un nome per il file VHD aggiunto alla risorsa di archiviazione, tuttavia il nome del disco viene generato automaticamente in Azure.

6. Tornare al dashboard per verificare che il disco dati sia stato connesso correttamente alla macchina virtuale. Verrà visualizzato nell'elenco **Dischi** come secondo disco, insieme al disco del sistema operativo.

	![Attach empty disk](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	Quando viene collegato alla macchina virtuale, il disco dati è offline e non inizializzato. Prima di poterlo usare per l'archiviazione dei dati, sarà necessario accedere alla macchina virtuale e inizializzarlo.

7. Connettersi alla macchina virtuale usando i passaggi descritti nella sezione precedente [Come accedere alla macchina virtuale dopo averla creata] (#logon).

8. Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro selezionare **Servizi file e archiviazione**.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. Selezionare **Dischi** nel menu espanso.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)	
	
10. Nella sezione **Dischi** è visualizzato un elenco con tre dischi: disco 0, disco 1 e disco 2. Disco 0 è il disco del sistema operativo, disco 1 è un disco di risorse temporaneo (che non deve essere usato per l'archiviazione dei dati) e disco 2 è il disco dati connesso alla macchina virtuale. Si noti che il disco dati ha una capacità di 5 GB come specificato in precedenza. Fare clic con il pulsante destro del mouse sul disco 2 e quindi scegliere **Inizializza**.

	![Start initialization](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. Fare clic su **Sì** per avviare il processo di inizializzazione.

	![Continue initialization](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. Fare di nuovo clic con il pulsante destro del mouse sul disco 2 e scegliere **Nuovo volume**. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. Completare la procedura guidata usando i valori predefiniti specificati. Al termine della procedura guidata, nella sezione **Volumi** viene visualizzato un nuovo volume. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	Il disco sarà ora online e pronto per l'uso con una nuova lettera di unità. 
	
##Passaggi successivi 

Per altre informazioni sulla configurazione di macchine virtuali Windows in Azure, vedere gli articoli seguenti:

[Come connettere macchine virtuali in un servizio cloud](http://www.windowsazure.com/it-it/documentation/articles/cloud-services-connect-virtual-machine/)

[Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server](http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Connettere un disco dati a una macchina virtuale](http://www.windowsazure.com/it-it/documentation/articles/storage-windows-attach-disk/)

[Gestire la disponibilità delle macchine virtuali](http://www.windowsazure.com/it-it/documentation/articles/manage-availability-virtual-machines/)

[Informazioni sulle macchine virtuali in Azure]: #virtualmachine
[Come creare la macchina virtuale]: #custommachine
[Come accedere alla macchina virtuale dopo averla creata]: #logon
[Come collegare un disco dati alla nuova macchina virtuale]: #attachdisk
[Come configurare la comunicazione con la macchina virtuale]: #endpoints


