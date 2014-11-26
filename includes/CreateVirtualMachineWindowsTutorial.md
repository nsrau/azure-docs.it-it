<properties title="Create a Virtual Machine Running Windows Server" pageTitle="How to create a Virtual Machine Running Windows Server" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Creazione di una macchina virtuale che esegue Windows Server

Questa esercitazione illustra come creare una macchina virtuale di Azure che esegue Windows Server usando la Raccolta immagini presente nel portale di gestione di Windows Azure. Nella Raccolta immagini è disponibile un'ampia gamma di immagini, ad esempio immagini di sistemi operativi Windows, sistemi operativi basati su Linux e di applicazioni.

> [WACOM.NOTE] Per completare questa esercitazione, non è necessario essere esperti di macchine virtuali di Azure. È invece richiesto un account di Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere [Creazione di un account Azure][Creazione di un account Azure].

In questa esercitazione si apprenderà come:

-   [Come creare la macchina virtuale][Come creare la macchina virtuale]
-   [Come accedere alla macchina virtuale dopo averla creata][Come accedere alla macchina virtuale dopo averla creata]
-   [Come collegare un disco dati alla nuova macchina virtuale][Come collegare un disco dati alla nuova macchina virtuale]

Per altre informazioni, vedere [Macchine virtuali][Macchine virtuali].

## <span id="createvirtualmachine"></span> </a>Come creare la macchina virtuale

Questa sezione illustra come usare l'opzione **Da raccolta** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida**. Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta**.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <span id="logon"></span> </a>Come accedere alla macchina virtuale dopo averla creata

Questa sezione descrive come accedere alla macchina virtuale per gestirne le impostazioni e le applicazioni che verranno eseguite al suo interno.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale **MyTestVM**.

    ![Select MyTestVM][Select MyTestVM]

3.  Nella barra dei comandi fare clic su **Connect**.

    ![Connessione a MyTestVM][Connessione a MyTestVM]

4.  Fare clic su **Apri** per usare il file di Remote Desktop Protocol creato automaticamente per la macchina virtuale.

    ![Apertura del file rdp][Apertura del file rdp]

5.  Fare clic su **Connect**.

    ![Procedere alla connessione][Procedere alla connessione]

6.  Nella casella per l'immissione della password digitare il nome utente e la password specificati al momento della creazione della macchina virtuale, quindi fare clic su **OK**.

7.  Fare clic su **Sì** per verificare l'identità della macchina virtuale.

    ![Verificare l'identità della macchina virtuale][Verificare l'identità della macchina virtuale]

    È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con un altro server dell'ufficio.

## <span id="attachdisk"></span> </a>Come collegare un disco dati alla nuova macchina virtuale

Questa sezione descrive come collegare un disco dati vuoto alla macchina virtuale. Per altre informazioni su come collegare dischi vuoti e dischi esistenti, vedere l'[esercitazione relativa al collegamento di un disco dati] (<http://www.windowsazure.com/it-it/documentation/articles/storage-windows-attach-disk/>).

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale **MyTestVM**.

    ![Select MyTestVM][Select MyTestVM]

3.  L'utente potrebbe essere indirizzato alla pagina Avvio rapido. In questo caso, fare clic su **Dashboard** nella parte superiore.

    ![Selezione del Dashboard][Selezione del Dashboard]

4.  Sella barra dei comandi fare clic su **Connetti** e quindi su **Connetti disco vuoto**.

    ![Selezione di Connetti sulla barra dei comandi][Selezione di Connetti sulla barra dei comandi]

5.  Le impostazioni **Virtual Machine Name**, **Storage Location**, **File Name** e **Host Cache Preference** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Digitare **5** nel campo **Size**. Fare quindi clic sul segno di spunta per connettere il disco vuoto alla macchina virtuale.

    ![Definizione delle dimensioni del disco vuoto][Definizione delle dimensioni del disco vuoto]

    > [WACOM.NOTE] Tutti i dischi vengono creati da un file VHD in Archiviazione di Azure. In **Nome file** è possibile specificare un nome per il file VHD aggiunto alla risorsa di archiviazione, tuttavia il nome del disco viene generato automaticamente in Azure.

6.  Tornare al dashboard per verificare che il disco dati sia stato connesso correttamente alla macchina virtuale. Verrà visualizzato nell'elenco **Dischi** come secondo disco, insieme al disco del sistema operativo.

    ![Attach Empty Disk][Attach Empty Disk]

    Quando viene collegato alla macchina virtuale, il disco dati è offline e non inizializzato. Prima di poterlo utilizzare per l'archiviazione dei dati, sarà necessario accedere alla macchina virtuale e inizializzarlo.

7.  Per connettersi alla macchina virtuale, attenersi ai passaggi descritti nella sezione precedente, [Come accedere alla macchina virtuale dopo averla creata][Come accedere alla macchina virtuale dopo averla creata] (\#logon).

8.  Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro fare clic su **Servizi file e archiviazione**.

    ![Espandere Servizi file e archiviazione in Server Manager][Espandere Servizi file e archiviazione in Server Manager]

9.  Selezionare **Dischi** nel menu espanso.

    ![Espandere Servizi file e archiviazione in Server Manager][1]

10. Nella sezione **Dischi** è visualizzato un elenco con tre dischi: disco 0, disco 1 e disco 2. Disco 0 è il disco del sistema operativo, disco 1 è un disco di risorse temporaneo (che non deve essere usato per l'archiviazione dei dati) e disco 2 è il disco dati connesso alla macchina virtuale. Si noti che il disco dati ha una capacità di 5 GB come specificato in precedenza. Fare clic con il pulsante destro del mouse sul disco 2 e quindi scegliere **Inizializza**.

    ![Avviare l'inizializzazione][Avviare l'inizializzazione]

11. Fare clic su **Sì** per avviare il processo di inizializzazione.

    ![Continuare l'inizializzazione][Continuare l'inizializzazione]

12. Fare di nuovo clic con il pulsante destro del mouse sul disco 2 e scegliere **Nuovo volume**.

    ![Creare il volume][Creare il volume]

13. Completare la procedura guidata usando i valori predefiniti specificati. Al termine della procedura guidata, nella sezione **Volumi** viene visualizzato un nuovo volume.

    ![Creare il volume][2]

    Il disco sarà ora online e pronto per l'uso con una nuova lettera di unità.

## Passaggi successivi

Per ulteriori informazioni sulla configurazione di macchine virtuali Windows in Azure, vedere gli articoli seguenti:

[Come connettere macchine virtuali in un servizio cloud][Come connettere macchine virtuali in un servizio cloud]

[Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server][Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server]

[Connettere un disco dati a una macchina virtuale][Connettere un disco dati a una macchina virtuale]

[Gestione della disponibilità delle macchine virtuali][Gestione della disponibilità delle macchine virtuali]

  [Creazione di un account Azure]: http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/
  [Come creare la macchina virtuale]: #createvirtualmachine
  [Come accedere alla macchina virtuale dopo averla creata]: #logon
  [Come collegare un disco dati alla nuova macchina virtuale]: #attachdisk
  [Macchine virtuali]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Select MyTestVM]: ./media/CreateVirtualMachineWindowsTutorial/selectvm.png
  [Connessione a MyTestVM]: ./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png
  [Apertura del file rdp]: ./media/CreateVirtualMachineWindowsTutorial/openrdp.png
  [Procedere alla connessione]: ./media/CreateVirtualMachineWindowsTutorial/connectrdc.png
  [Verificare l'identità della macchina virtuale]: ./media/CreateVirtualMachineWindowsTutorial/certificate.png
  [Selezione del Dashboard]: ./media/CreateVirtualMachineWindowsTutorial/dashboard.png
  [Selezione di Connetti sulla barra dei comandi]: ./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png
  [Definizione delle dimensioni del disco vuoto]: ./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png
  [Attach Empty Disk]: ./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png
  [Espandere Servizi file e archiviazione in Server Manager]: ./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png
  [1]: ./media/CreateVirtualMachineWindowsTutorial/selectdisks.png
  [Avviare l'inizializzazione]: ./media/CreateVirtualMachineWindowsTutorial/initializedisk.png
  [Continuare l'inizializzazione]: ./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png
  [Creare il volume]: ./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png
  [2]: ./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png
  [Come connettere macchine virtuali in un servizio cloud]: http://www.windowsazure.com/it-it/documentation/articles/cloud-services-connect-virtual-machine/
  [Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server]: http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Connettere un disco dati a una macchina virtuale]: http://www.windowsazure.com/it-it/documentation/articles/storage-windows-attach-disk/
  [Gestione della disponibilità delle macchine virtuali]: http://www.windowsazure.com/it-it/documentation/articles/manage-availability-virtual-machines/
