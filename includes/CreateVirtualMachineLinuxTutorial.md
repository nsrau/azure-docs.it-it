# Creazione di una macchina virtuale che esegue Linux

Creare una macchina virtuale che esegue il sistema operativo Linux è facile quando si utilizza la raccolta immagini presente nel portale di gestione di Azure In questa guida si presuppone che l'utente non abbia mai utilizzato Azure. È possibile creare una macchina virtuale che esegue il sistema operativo Linux nel cloud e quindi accedervi per personalizzarla.

> [WACOM.NOTE] Per completare questa esercitazione, non è necessario essere esperti di macchine virtuali di Azure. È invece richiesto un account di Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere [Creazione di un account Azure][Creazione di un account Azure].

Si apprenderà come:

-   [Informazioni sulle macchine virtuali in Azure][Informazioni sulle macchine virtuali in Azure]
-   [Come creare la macchina virtuale][Come creare la macchina virtuale]
-   [Come accedere alla macchina virtuale dopo averla creata][Come accedere alla macchina virtuale dopo averla creata]
-   [Come collegare un disco dati alla nuova macchina virtuale][Come collegare un disco dati alla nuova macchina virtuale]

**Nota**: in questa esercitazione viene creata una macchina virtuale non connessa a una rete virtuale. Se si desidera che una macchina virtuale utilizzi una rete virtuale, è necessario specificare la rete quando si crea la macchina. Per altre informazioni sulle reti virtuali, vedere [Informazioni generali su Rete virtuale di Azure][Informazioni generali su Rete virtuale di Azure].

## <span id="virtualmachine"></span> </a>Informazioni sulle macchine virtuali in Azure

In Azure una macchina virtuale è un server disponibile nel cloud che è possibile controllare e gestire. Dopo aver creato una macchina virtuale in Azure, è possibile eliminarla e ricrearla ogni volta che è necessario ed è possibile accedervi con le stesse modalità con cui si accederebbe a un server nel proprio ufficio. Per creare una macchina virtuale, vengono utilizzati file VHD (Virtual Hard Disk, disco rigido virtuale). Per una macchina virtuale, vengono utilizzati i seguenti tipi di dischi rigidi virtuali:

-   **Immagine** - Un disco rigido virtuale utilizzato come modello per creare una nuova macchina virtuale. Un'immagine è un modello perché non dispone di impostazioni specifiche come nel caso di una macchina virtuale in esecuzione, ad esempio il nome del computer e le impostazioni dell'account utente. Se si crea una macchina virtuale utilizzando un'immagine, viene creato automaticamente un disco del sistema operativo per la nuova macchina.
-   **Disco** - per disco si intende un disco rigido virtuale che è possibile avviare e montare come versione in esecuzione di un sistema operativo. Una volta eseguito il provisioning, un'immagine diventa un disco. Viene sempre creato un disco quando si utilizza un'immagine per creare una macchina virtuale. Qualsiasi disco rigido virtuale collegato all'hardware virtualizzato e in esecuzione come parte di un servizio è un disco.

Per la creazione di una macchina virtuale tramite immagini sono disponibili le opzioni seguenti:

-   Creare una macchina virtuale utilizzando un'immagine fornita nella raccolta immagini del portale di gestione di Azure.
-   Creare e caricare in Azure un file .vhd contenente un'immagine, quindi creare una macchina virtuale utilizzando l'immagine. Per altre informazioni sulla creazione e il caricamento di un'immagine personalizzata, vedere [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux][Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux].

Ogni macchina virtuale risiede in un servizio cloud, da sola oppure insieme ad altre macchine virtuali. È possibile inserire le macchine virtuali nello stesso servizio cloud per consentire loro di comunicare, per bilanciare il carico del traffico di rete tra le macchine virtuali e per garantire una disponibilità elevata delle macchine. Per altre informazioni sui servizi cloud e sulle macchine virtuali, vedere la sezione relativa ai modelli di esecuzione in [Introduzione ad Azure][Introduzione ad Azure].

## <span id="custommachine"></span> </a>Come creare la macchina virtuale

Per creare una macchina virtuale personalizzata nel portale di gestione, viene usato il metodo di creazione **da raccolta**. Tale metodo fornisce un numero maggiore di opzioni per la configurazione della macchina virtuale, ad esempio le risorse connesse, il nome DNS e la connettività di rete, se richiesta.

1.  Accedere al [portale di gestione][portale di gestione] di Azure.
    Sulla barra dei comandi fare clic su **Nuovo**.

2.  Fare clic su **Virtual Machine**, quindi su **From Gallery**.

3.  In **Choose an Image**, selezionare un'immagine da uno degli elenchi. Le immagini visualizzate possono variare in base alla sottoscrizione in uso. Fare clic sulla freccia per continuare.

4.  Se sono disponibili più versioni, selezionare quella da utilizzare in **Version Release Date**.

5.  In **Virtual Machine Name** digitare il nome da utilizzare. Per questa macchina virtuale digitare **MyTestVM1**.

6.  In **Size** selezionare le dimensioni da utilizzare per la macchina virtuale. Il valore da selezionare dipende dal numero di core necessari per l'applicazione. Per questa macchina virtuale selezionare la dimensione minima disponibile.

7.  In **New User Name** digitare il nome dell'account che verrà utilizzato per amministrare la macchina virtuale. Non è possibile utilizzare ROOT per questo nome utente. Per questa macchina virtuale digitare **NewUser1**.

8.  In Autenticazione selezionare **Provide a Password**. Fornire quindi le informazioni necessarie e fare clic sulla freccia per continuare.

9.  È possibile collocare macchine virtuali insieme nel servizio cloud, ma, ai fini della presente esercitazione, verrà creata una sola macchina virtuale. A tale scopo, selezionare **Create a new cloud service**.

10. In **Cloud Service DNS Name** digitare un nome composto da un minimo di 3 a un massimo di 24 caratteri, tra lettere in minuscolo e numeri. Tale nome sarà incluso nell'URI utilizzato per contattare la macchina virtuale tramite il servizio cloud. Per questa macchina virtuale digitare **MyService1**.

11. In **Region/Affinity Group/Virtual Network** selezionare l'area in cui situare la macchina virtuale.

12. È possibile selezionare un account di archiviazione in cui è archiviato il file VHD. Per questa esercitazione accettare l'impostazione predefinita di **Use an Automatically Generated Storage Account**.

13. Per le finalità di questa esercitazione, in **Availability Set** utilizzare l'impostazione predefinita **None**. Fare clic sul segno di spunta per creare la macchina virtuale, quindi fare clic sulla freccia per continuare.

14. In **VM Agent** scegliere se installare l'agente VM. Tale agente offre l'ambiente per installare le estensioni che consentono di interagire con la macchina virtuale. Per informazioni dettagliate, vedere [Gestire le estensioni][Gestire le estensioni].

15. In **Endpoints** esaminare l'endpoint creato automaticamente per consentire connessioni Secure Shell (SSH) alla macchina virtuale. Gli endpoint consentono a risorse in Internet o in altre reti virtuali di comunicare con una macchina virtuale. È possibile creare altri endpoint ora oppure aggiungerne in seguito. Per istruzioni su come crearli in un secondo momento, vedere [Come configurare gli endpoint a una macchina virtuale][Come configurare gli endpoint a una macchina virtuale].

Dopo aver creato la macchina virtuale e il servizio cloud, questi vengono elencati nel portale di gestione rispettivamente in **Virtual Machines** e **Cloud Services**. Sia la macchina virtuale che il servizio cloud vengono avviati automaticamente.

## <span id="logon"></span> </a>Come accedere alla macchina virtuale dopo averla creata

Per gestire le impostazioni della macchina virtuale e le applicazioni da eseguirvi all'interno, è possibile utilizzare un client SSH. A tale scopo, è necessario installare un client SSH nel computer che si intende utilizzare per accedere alla macchina virtuale. È disponibile un'ampia scelta di programmi client SSH. Di seguito vengono indicate alcune tra le scelte possibili:

-   Se si utilizza un computer che esegue un sistema operativo Windows, è possibile utilizzare un client SSH come PuTTY. Per altre informazioni, vedere la pagina [PuTTY Download][PuTTY Download].
-   Se si utilizza un computer che esegue un sistema operativo Linux, è possibile utilizzare un client SSH come OpenSSH. Per altre informazioni, vedere la pagina [OpenSSH][OpenSSH].

In questa esercitazione viene illustrato l'utilizzo del programma PuTTY per accedere alla macchina virtuale.

1.  Individuare le informazioni **Host Name** e **Port information** nel portale di gestione. È possibile rilevare le informazioni necessarie dal dashboard della macchina virtuale. Fare clic sul nome della macchina virtuale e individuare **SSH Details** nella sezione **Quick Glance** del dashboard.

    ![Individuare i dettagli SSH][Individuare i dettagli SSH]

2.  Aprire il programma PuTTY.

3.  Immettere in **Host Name** e **Port information** i dati raccolti dal dashboard, quindi fare clic su **Open**.

    ![Immettere il nome host e le informazioni sulla porta][Immettere il nome host e le informazioni sulla porta]

4.  Accedere alla macchina virtuale utilizzando l'account NewUser1 specificato durante la creazione della macchina.

    ![Accesso alla nuova macchina virtuale][Accesso alla nuova macchina virtuale]

    È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

## <span id="attachdisk"></span> </a>Come collegare un disco dati alla nuova macchina virtuale

L'applicazione in uso potrebbe richiedere l'archiviazione di dati. Per impostare questa funzionalità è necessario collegare un disco dati alla macchina virtuale creata in precedenza. Il modo più semplice per procedere consiste nel collegare un disco dati vuoto alla macchina virtuale.

**Nota: Disco dati e disco risorse**
 I dischi dati risiedono nell'archiviazione di Azure e possono essere utilizzati come archivio permanente per file e dati dell'applicazione.

Ogni macchina virtuale creata dispone inoltre di un *disco risorse* temporaneo locale collegato. Poiché è possibile che i dati su un disco risorse non vengano mantenuti tra un riavvio e l'altro, questo tipo di disco viene spesso utilizzato da applicazioni e processi in esecuzione nella macchina virtuale per l'archiviazione temporanea di dati. Viene inoltre utilizzato per archiviare file di paging o di scambio per il sistema operativo.

In Linux il disco risorse è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu). Si noti che il disco risorse è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. D'altra parte, in Linux il kernel potrebbe assegnare al disco dati il nome `/dev/sdc`; in tal caso gli utenti dovranno suddividere in partizioni, formattare e montare tale risorsa. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure][Guida dell'utente dell'agente Linux di Azure].

1.  Accedere al portale di gestione di Azure se questa operazione non è già stata eseguita.

2.  Fare clic su **Virtual Machines**, quindi selezionare la macchina virtuale **MyTestVM1** creata in precedenza.

3.  Sulla barra dei comandi fare clic su **Connetti**, quindi su **Connetti disco vuoto**.

    Verrà visualizzata la finestra di dialogo **Connetti disco vuoto**.

    ![Definizione dei dettagli del disco][Definizione dei dettagli del disco]

4.  Le impostazioni **Virtual Machine Name**, **Storage Location** e **File Name** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Digitare **5** nel campo **Size**.

    **Nota:** tutti i dischi vengono creati da un file VHD in Archiviazione di Azure. Il nome del disco viene generato automaticamente ma è tuttavia possibile specificare un nome per il file VHD aggiunto alla risorsa di archiviazione.

5.  Fare clic sul segno di spunta per collegare il disco dati alla macchina virtuale.

6.  È possibile verificare che il disco dati sia stato collegato correttamente alla macchina virtuale visualizzando il dashboard. Per visualizzare il dashboard, fare clic sul nome della macchina virtuale.

    Il numero di dischi nella macchina virtuale è ora pari a 2 e il disco collegato è elencato nella tabella **Disks**.

    ![Collegamento disco completato][Collegamento disco completato]

Il disco dati appena collegato alla macchina virtuale è offline e non è inizializzato dopo l'aggiunta. Per archiviare i dati, è necessario accedere alla macchina e inizializzare il disco da utilizzare.

1.  Connettersi alla macchina virtuale utilizzando i passaggi descritti in precedenza in **Come accedere alla macchina virtuale dopo averla creata**.

2.  Nella finestra di SSH digitare il comando seguente e quindi immettere la password dell'account:

    `sudo grep SCSI /var/log/messages`

    L'identificatore dell'ultimo disco dati aggiunto verrà indicato nei messaggi visualizzati.

    ![Identificazione disco][Identificazione disco]

3.  Nella finestra di SSH digitare il comando seguente per creare un nuovo dispositivo, quindi immettere la password per l'account:

    `sudo fdisk /dev/sdc`

    > [WACOM.NOTE] In questo esempio potrebbe essere necessario utilizzare `sudo -i` in alcune distribuzioni se /sbin o /usr/sbin non sono disponibili in `$PATH`.

4.  Digitare **n** per creare una nuova partizione.

    ![Creare un nuovo dispositivo][Creare un nuovo dispositivo]

5.  Digitare **p** per impostare la partizione come primaria, digitare **1** per impostarla come prima partizione, quindi premere INVIO per accettare il valore predefinito per il cilindro.

    ![Creare la partizione][Creare la partizione]

6.  Digitare **p** per visualizzare i dettagli relativi al disco da suddividere in partizioni.

    ![Visualizzare le informazioni sul disco][Visualizzare le informazioni sul disco]

7.  Digitare **w** per scrivere le impostazioni per il disco.

    ![Scrivere le modifiche sul disco][Scrivere le modifiche sul disco]

8.  È necessario creare il file system nella nuova partizione. Ad esempio, digitare il comando seguente per creare il file system, quindi immettere la password per l'account:

    `sudo mkfs -t ext4 /dev/sdc1`

    ![Creare il file system][Creare il file system]

    > [WACOM.NOTE] Si noti i sistemi SUSE Linux Enterprise 11 forniscono l'accesso in sola lettura ai file system ext4. Per questi sistemi è consigliabile formattare il nuovo file system come ext3 anziché ext4.

9.  È quindi necessario disporre di una directory per il montaggio del nuovo file system. Digitare, ad esempio, il comando seguente per creare una nuova directory per il montaggio dell'unità, quindi immettere la password per l'account:

    `sudo mkdir /datadrive`

10. Digitare il comando seguente per montare l'unità:

    `sudo mount /dev/sdc1 /datadrive`

    Il disco dati è ora pronto per l'uso come **/datadrive**.

11. Aggiungere la nuova unità a /etc/fstab:

    Per assicurarsi che l'unità venga rimontata automaticamente dopo un riavvio, è necessario aggiungerla al file /etc/fstab. È inoltre consigliabile che l'UUID (Universally Unique IDentifier) utilizzato in /etc/fstab faccia riferimento all'unità anziché al solo nome del dispositivo, ad esempio /dev/sdc1. Per individuare l'UUID della nuova unità, è possibile utilizzare l'utilità **blkid**:

        `sudo -i blkid`

    L'output sarà simile al seguente:

        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

    > [WACOM.NOTE] blkid potrebbe non richiedere l'accesso sudo in tutti i casi ma potrebbe risultare più facile eseguirlo con `sudo -i` su distribuzioni in cui /sbin o /usr/sbin non sono presenti nei percorsi indicati in `$PATH`.

    **Attenzione:** se il file /etc/fstab non viene modificato in modo corretto, il sistema potrebbe diventare instabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

    Utilizzare un editor di testo per immettere le informazioni sul nuovo file system alla fine del file /etc/fstab. In questo esempio verranno utilizzati il valore UUID del nuovo dispositivo **/dev/sdc1** creato nella procedura precedente, nonché il punto di montaggio **/datadrive**:

        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

    Oppure, su sistemi basati su SUSE Linux potrebbe essere necessario utilizzare un formato leggermente diverso:

        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

    Anche le eventuali altre unità o partizioni dati create dovranno essere inserite separatamente in /etc/fstab.

    Per verificare che il file system venga montato correttamente, è sufficiente smontarlo e rimontarlo, usando ad esempio il punto di montaggio `/datadrive` creato nei passaggi precedenti:

        `sudo umount /datadrive`
        `sudo mount /datadrive`

    Se il secondo comando restituisce un errore, verificare il file /etc/fstab per ottenere la sintassi corretta.

    > [WACOM.NOTE] Se si rimuove successivamente un disco dati senza modificare fstab, è possibile che si verifichi un errore di avvio della macchina virtuale. Se si tratta di un errore ricorrente, nella maggior parte delle distribuzioni sono disponibili le opzioni `nofail` e/o `nobootwait`, che consentono l'avvio di un sistema anche in caso di assenza del disco. Per altre informazioni su tali parametri, fare riferimento alla documentazione della distribuzione.

## Passaggi successivi

Per altre informazioni su Linux in Azure, vedere gli articoli seguenti:

-   [Introduzione a Linux in Azure][Introduzione a Linux in Azure]

-   [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux][Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux]

  [Creazione di un account Azure]: http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/
  [Informazioni sulle macchine virtuali in Azure]: #virtualmachine
  [Come creare la macchina virtuale]: #custommachine
  [Come accedere alla macchina virtuale dopo averla creata]: #logon
  [Come collegare un disco dati alla nuova macchina virtuale]: #attachdisk
  [Informazioni generali su Rete virtuale di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux]: /it-it/manage/linux/common-tasks/upload-a-vhd/
  [Introduzione ad Azure]: http://go.microsoft.com/fwlink/p/?LinkId=311926
  [portale di gestione]: http://manage.windowsazure.com
  [Gestire le estensioni]: http://go.microsoft.com/FWLink/p/?LinkID=390493
  [Come configurare gli endpoint a una macchina virtuale]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-set-up-endpoints/
  [PuTTY Download]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH]: http://www.openssh.org/
  [Individuare i dettagli SSH]: ./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png
  [Immettere il nome host e le informazioni sulla porta]: ./media/CreateVirtualMachineLinuxTutorial/putty.png
  [Accesso alla nuova macchina virtuale]: ./media/CreateVirtualMachineLinuxTutorial/sshlogin.png
  [Guida dell'utente dell'agente Linux di Azure]: http://www.windowsazure.com/it-it/manage/linux/how-to-guides/linux-agent-guide/
  [Definizione dei dettagli del disco]: ./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png
  [Collegamento disco completato]: ./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png
  [Identificazione disco]: ./media/CreateVirtualMachineLinuxTutorial/diskmessages.png
  [Creare un nuovo dispositivo]: ./media/CreateVirtualMachineLinuxTutorial/diskpartition.png
  [Creare la partizione]: ./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png
  [Visualizzare le informazioni sul disco]: ./media/CreateVirtualMachineLinuxTutorial/diskinfo.png
  [Scrivere le modifiche sul disco]: ./media/CreateVirtualMachineLinuxTutorial/diskwrite.png
  [Creare il file system]: ./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png
  [Introduzione a Linux in Azure]: http://www.windowsazure.com/it-it/documentation/articles/introduction-linux/
  [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux]: http://www.windowsazure.com/it-it/documentation/articles/xplat-cli/
