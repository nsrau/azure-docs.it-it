# Creazione di una macchina virtuale che esegue Windows Server

Creare una macchina virtuale che esegue il sistema operativo Windows
Server è facile quando si utilizza la raccolta immagini presente nel
portale di gestione di Azure. In questa esercitazione viene illustrato
come creare una macchina virtuale che esegue Windows Server nel cloud e
quindi accedervi per personalizzarla. Per svolgere l'esercitazione, non
è necessario avere esperienze precedenti nell'utilizzo di Azure.

Si acquisiranno le nozioni seguenti:

* [Informazioni sulle macchine virtuali in Azure](#virtualmachine)
* [Come creare la macchina virtuale](#custommachine)
* [Come accedere alla macchina virtuale dopo averla creata](#logon)
* [Come collegare un disco dati alla nuova macchina
  virtuale](#attachdisk)

**Nota:** in questa esercitazione viene creata una macchina virtuale non
connessa a una rete virtuale. Se si desidera che una macchina virtuale
utilizzi una rete virtuale, è necessario specificare la rete quando si
crea la macchina. Per ulteriori informazioni sulle reti virtuali, vedere
[Informazioni generali su Rete virtuale di Azure][1].
## <a  id="virtualmachine"> </a>Informazioni sulle macchine virtuali in Azure

In Azure una macchina virtuale è un server disponibile nel cloud che è
possibile controllare e gestire. Dopo aver creato una macchina virtuale
in Azure, è possibile eliminarla e ricrearla ogni volta che è necessario
ed è possibile accedervi con le stesse modalità con cui si accederebbe a
qualsiasi altro server. Per ulteriori informazioni, vedere [Macchine
virtuali][2].
## <a  id="custommachine"> </a>Come creare la macchina virtuale

In questa esercitazione viene illustrato come utilizzare il metodo
**From Gallery** nel portale di gestione per la creazione di una
macchina virtuale. Rispetto al metodo **Quick Create**, questo metodo
fornisce un numero maggiore di opzioni per la configurazione della
macchina virtuale nella fase di creazione.

1.  Accedere alla propria sottoscrizione per utilizzare il [portale di
    gestione][3] di Azure. Se non si dispone di una sottoscrizione, è
    possibile iscriversi per provare la [versione di valutazione
    gratuita][4].

2.  Sulla barra dei comandi fare clic su **New**.

3.  Fare clic su **Virtual Machine**, quindi su **From Gallery**.

4.  In **Choose an Image** selezionare **Windows Server 2012 R2
    Datacenter**. Le immagini visualizzate possono variare in base alla
    sottoscrizione in uso. Fare clic sulla freccia per continuare.

5.  Se sono disponibili più versioni, selezionare quella da utilizzare
    in **Version Release Date**.

6.  In **Virtual Machine Name** digitare il nome da utilizzare per la
    macchina virtuale. Per questa macchina virtuale digitare
    **MyTestVM1**.

7.  In **Size** selezionare la dimensione della macchina virtuale. Il
    valore da selezionare dipende dal numero di core necessari per
    eseguire l'applicazione. Per questa macchina virtuale selezionare
    la dimensione minima disponibile.

8.  In **New User Name** digitare un nome per l'account amministrativo
    da utilizzare per la gestione del server. Per questa macchina
    virtuale digitare **MyTestVM1Admin**.

9.  In **New Password** digitare una password complessa per l'account
    amministrativo della macchina virtuale. In **Confirm Password**
    ridigitare la password. Fare clic sulla freccia per continuare.

10. È possibile collocare macchine virtuali insieme in un servizio cloud
    per creare applicazioni affidabili, tuttavia, ai fini della presente
    esercitazione, verrà creata una sola macchina virtuale. A tale
    scopo, selezionare **Create a new cloud service**.

11. In **Cloud Service DNS Name** digitare un nome composto da un minimo
    di 3 a un massimo di 24 caratteri, tra lettere in minuscolo e
    numeri. Tale nome sarà incluso nell'URI utilizzato per contattare
    la macchina virtuale tramite il servizio cloud. Per questa macchina
    virtuale digitare **MyService1**.

12. In **Region/Affinity Group/Virtual Network** selezionare l'area in
    cui situare la macchina virtuale.

13. Per l'archiviazione del disco rigido virtuale che verrà utilizzato
    dalla macchina virtuale, accettare l'impostazione predefinita **Use
    an Automatically Generated Storage Account**.

14. Per le finalità di questa esercitazione, in **Availability Set**
    utilizzare l'impostazione predefinita **None**. Fare clic sulla
    freccia per continuare.

15. In **VM Agent** scegliere se installare l'agente VM. Tale agente
    offre l'ambiente per installare le estensioni che consentono di
    interagire con la macchina virtuale. Per informazioni dettagliate,
    vedere l'articolo sull'[utilizzo delle estensioni][5].

16. In **Endpoints** verificare i nuovi endpoint che verranno creati per
    consentire connessioni alla macchina virtuale utilizzando la
    comunicazione remota, ad esempio tramite Desktop remoto e Windows
    PowerShell. È inoltre possibile aggiungere subito gli endpoint o
    crearli in un secondo momento. Per istruzioni su come crearli in un
    secondo momento, vedere [Come configurare le comunicazioni con una
    macchina virtuale][6].

17. Fare clic sul segno di spunta per creare la macchina virtuale.
    
    Dopo aver creato la macchina virtuale e il servizio cloud, questi
    vengono elencati nel portale di gestione rispettivamente in
    **Virtual Machines** e **Cloud Services**. Sia la macchina virtuale
    che il servizio cloud vengono avviati automaticamente.
## <a  id="logon"> </a>Come accedere alla macchina virtuale dopo averla creata

È possibile accedere alla macchina virtuale creata per gestirne le
impostazioni e le applicazioni eseguite.

1.  Accedere al [portale di gestione di Azure][3].

2.  Fare clic su **Virtual Machines**, quindi selezionare la macchina
    virtuale **MyTestVM1**.

3.  Nella barra dei comandi fare clic su **Connect**.

4.  Fare clic su **Open** per utilizzare il file di protocollo di
    Desktop remoto creato automaticamente per la macchina virtuale.

5.  Fare clic su **Connect**.
    
    ![Procedere alla
    connessione](./media/CreateVirtualMachineWindowsTutorial/connectpublisher.png)

6.  Nella casella per l'immissione della password digitare il nome
    utente e la password specificati al momento della creazione della
    macchina virtuale, quindi fare clic su **OK**.

7.  Fare clic su **Yes** per verificare l'identità della macchina
    virtuale.
    
    ![Verificare l'identità della macchina
    virtuale](./media/CreateVirtualMachineWindowsTutorial/connectverify.png)
    
    È ora possibile lavorare con la macchina virtuale nello stesso modo
    in cui lo si farebbe con un altro server dell'ufficio.
## <a  id="attachdisk"> </a>Come collegare un disco dati alla nuova macchina virtuale

L'applicazione in uso potrebbe richiedere l'archiviazione di dati. Per
impostare questa funzionalità, collegare un disco dati alla macchina
virtuale. Il modo più semplice per procedere consiste nel collegare un
disco dati vuoto alla macchina virtuale.

1.  Accedere al [portale di gestione di Azure][3].

2.  Fare clic su **Virtual Machines**, quindi selezionare la macchina
    virtuale **MyTestVM1**.

3.  Sulla barra dei comandi fare clic su **Attach**, quindi su **Attach
    Empty Disk**.
    
    Verrà visualizzata la finestra di dialogo **Attach Empty Disk**.
    
    > [WACOM.NOTE] Potrebbe essere visualizzata la pagina Quick Start
    > anziché il dashboard e la barra dei comandi. In questo caso, fare
    > clic su **Dashboard** nella parte superiore.

4.  Le impostazioni **Virtual Machine Name**, **Storage Location**,
    **File Name** e **Host Cache Preference** sono definite
    automaticamente. È sufficiente immettere la dimensione desiderata
    per il disco. Digitare **5** nel campo **Size**.
    
    **Nota:** tutti i dischi vengono creati da un file VHD in
    Archiviazione di Azure. È possibile specificare un nome per il file
    VHD aggiunto alla risorsa di archiviazione, tuttavia il nome del
    disco viene generato automaticamente in Azure.

5.  Fare clic sul segno di spunta per collegare il disco dati alla
    macchina virtuale.

6.  Fare clic sul nome della macchina virtuale. Viene visualizzato il
    dashboard, il che consente di verificare che il disco dati sia stato
    collegato correttamente alla macchina virtuale.
    
    La macchina virtuale dispone ora di 2 dischi. Il disco collegato
    verrà visualizzato nella tabella **Disks**.
    
    ![Attach Empty
    Disk](./media/CreateVirtualMachineWindowsTutorial/attachemptysuccess.png)
    
    Quando viene collegato alla macchina virtuale, il disco dati è
    offline e non inizializzato. Prima di poterlo utilizzare per
    l'archiviazione dei dati, sarà necessario accedere alla macchina
    virtuale e inizializzarlo.

7.  Connettersi alla macchina virtuale utilizzando i passaggi descritti
    nella sezione precedente, **Come accedere alla macchina virtuale
    dopo averla creata**.

8.  Dopo aver eseguito l'accesso alla macchina virtuale, aprire
    **Server Manager**. Nel riquadro sinistro espandere **Storage**,
    quindi fare clic su **Disk Management**.
    
    ![Inizializzare il disco in Server
    Manager](./media/CreateVirtualMachineWindowsTutorial/servermanager.png)

9.  Fare clic con il pulsante destro del mouse su **Disk 2**, quindi su
    **Initialize Disk**.
    
    ![Avviare
    l'inizializzazione](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

10. Fare clic su **OK** per avviare il processo di inizializzazione.

11. Fare clic con il pulsante destro del mouse sull'area di allocazione
    spazio per il disco 2, scegliere **New Simple Volume**, quindi
    terminare la procedura guidata con i valori predefiniti.
    
    ![Creare il
    volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)
    
    Il disco sarà ora online e pronto per l'uso con una nuova lettera
    di unità.
    
    ![Inizializzazione
    completata](./media/CreateVirtualMachineWindowsTutorial/initializesuccess.png)
## Passaggi successivi

Per ulteriori informazioni sulla configurazione di macchine virtuali
Windows in Azure, vedere gli articoli seguenti:

-[Come connettere macchine virtuali in un servizio cloud][7]

-[Gestione della disponibilità delle macchine virtuali][8]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkID=271224
[3]: http://manage.windowsazure.com
[4]: http://go.microsoft.com/fwlink/p/?LinkID=23435
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/
[7]: http://www.windowsazure.com/en-us/documentation/articles/cloud-services-connect-virtual-machine/
[8]: http://www.windowsazure.com/en-us/documentation/articles/manage-availability-virtual-machines/
