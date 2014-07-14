
# Come collegare un disco dati a una macchina virtuale

* [Concetti](#concepts)
* [Procedura: Collegare un disco esistente](#attachexisting)
* [Procedura: Collegare un disco vuoto](#attachempty)
* [Procedura: Inizializzare un nuovo disco dati in Windows Server 2008
  R2](#initializeinWS)
* [Procedura: Inizializzare un nuovo disco dati in
  Linux](#initializeinlinux)

## <a id="concepts" ></a>Concetti

È possibile collegare un disco dati a una macchina virtuale per archiviare i dati dell'applicazione. Il disco dati è un disco rigido virtuale che è possibile creare in locale con il proprio computer o nel cloud con Azure. I dischi dati in una macchina virtuale vengono gestiti con le stesse modalità con cui lo si farebbe in un server nel proprio ufficio.

È possibile utilizzare il portale di gestione per caricare e collegare alla macchina virtuale un disco contenente dati oppure per aggiungere un disco vuoto dallo stesso account di archiviazione utilizzato per la macchina virtuale. In questo articolo vengono descritti entrambi i processi. Per collegare un disco vuoto ubicato in un account di archiviazione diverso, utilizzare il cmdlet [Add-AzureDataDisk][1], disponibile nel modulo Azure PowerShell. Per scaricare il modulo, vedere la pagina dei [download][2].

La macchina virtuale non viene arrestata per l'aggiunta del disco. Il numero di dischi che è possibile collegare a una macchina virtuale dipende dalle dimensioni della macchina virtuale stessa. Per informazioni sulla macchina virtuale e sulle dimensioni dei dischi, vedere [Dimensioni delle macchine virtuali per Azure][3].

> [WACOM.NOTE] L'archiviazione di Azure supporta oggetti BLOB di
> dimensioni fino a 1 TB, in grado di ospitare un disco rigido virtuale
> con una dimensione virtuale di 999 GB. Se si utilizza Hyper-V per
> creare un nuovo disco rigido virtuale, tuttavia, la dimensione
> specificata rappresenta la dimensione virtuale. Per utilizzare il
> disco rigido virtuale in Azure, specificare una dimensione non
> superiore a 999 GB.

**Disco dati e disco risorse**  
 I dischi dati risiedono nell'archiviazione di Azure e possono essere utilizzati come archivio permanente per file e dati dell'applicazione.

Ogni macchina virtuale dispone inoltre di un *disco risorse* temporaneo locale collegato. Poiché è possibile che i dati su un disco risorse non vengano mantenuti tra un riavvio e l'altro, questo tipo di disco viene spesso utilizzato da applicazioni e processi in esecuzione nella macchina virtuale per l'archiviazione temporanea di dati. Viene inoltre utilizzato per archiviare file di paging o di scambio per il sistema operativo.

In Windows il disco risorse viene etichettato come unità **D:** . In Linux il disco risorse è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu). Per ulteriori informazioni, vedere [Guida dell'utente dell'agente Linux di Azure][4].

Per ulteriori informazioni sull'utilizzo di dischi dati, vedere [Gestione di dischi e immagini][5].

## <a id="attachexisting" ></a>Procedura: Collegare un disco esistente

1.  Accedere al [portale di gestione di Azure][6] se questa operazione non è già stata eseguita.

2.  Fare clic su **Virtual Machines**, quindi selezionare la macchina virtuale a cui collegare il disco.

3.  Sulla barra dei comandi fare clic su **Attach**, quindi selezionare **Attach Disk**.
    
    ![Collegare il disco
    dati](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)
    
    Verrà visualizzata la finestra di dialogo **Attach Disk**.
    
    ![Immettere i dettagli del
    disco](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

4.  Selezionare il disco dati da collegare alla macchina virtuale. 5.  Fare clic sul segno di spunta per collegare il disco dati alla macchina virtuale.
    
    A questo punto il disco dati verrà visualizzato nel dashboard della macchina virtuale.
    
    ![Disco dati correttamente
    collegato](./media/howto-attach-disk-window-linux/AttachSuccess.png)

## <a id="attachempty" ></a>Procedura: Collegare un disco vuoto

Dopo aver creato e caricato un file con estensione vhd da utilizzare come disco vuoto, è possibile collegarlo a una macchina virtuale. Utilizzare il cmdlet [Add-AzureVhd][7] per caricare il file VHD nell'account di archiviazione.

1.  Fare clic su **Virtual Machines**, quindi selezionare la macchina virtuale a cui collegare il disco dati.

2.  Sulla barra dei comandi, fare clic su **Attach**, quindi selezionare **Attach Empty Disk**.
    
    ![Collegare un disco
    vuoto](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)
    
    Verrà visualizzata la finestra di dialogo **Attach Empty Disk**.
    
    ![Collegare un nuovo disco
    vuoto](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

3.  In **File Name** accettare il nome generato automaticamente oppure immettere un nome da utilizzare per il file VHD che verrà archiviato. Il disco dati creato dal file VHD utilizzerà sempre il nome generato automaticamente.

4.  In **Size** immettere la dimensione del disco dati.

5.  Fare clic sul segno di spunta per collegare il disco dati vuoto.
    
    A questo punto il disco dati verrà visualizzato nel dashboard della macchina virtuale.
    
    ![Disco dati vuoto collegato
    correttamente](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

> [WACOM.NOTE] Dopo l'aggiunta di un disco dati, sarà necessario
> accedere alla macchina virtuale e inizializzare il disco in modo che
> la macchina virtuale possa utilizzarlo per l'archiviazione.

## <a id="initializeinWS" ></a>Procedura: Inizializzare un nuovo disco dati in Windows Server

1.  Connettersi alla macchina virtuale utilizzando i passaggi descritti in precedenza in [Come accedere a una macchina virtuale che esegue Windows Server](../virtual-machines-log-on-windows-server/).

2.  Dopo aver effettuato l'accesso, avviare **Server Manager**, nel riquadro sinistro espandere **Storage**, quindi fare clic su **Disk Management**.
    
    ![Avviare Server
    Manager](./media/howto-attach-disk-window-linux/ServerManager.png)

3.  Fare clic con il pulsante destro del mouse su **Disk 2**, quindi su **Initialize Disk**.
    
    ![Inizializzare il
    disco](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4.  Fare clic su **OK** per avviare il processo di inizializzazione.

5.  Fare clic con il pulsante destro del mouse sull'area di allocazione spazio per il disco 2, scegliere **New Simple Volume**, quindi terminare la procedura guidata con i valori predefiniti.
    
    ![Inizializzare il
    volume](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)
    
    Il disco sarà ora online e pronto per l'uso con una nuova lettera di unità.
    
    ![Inizializzazione del volume
    completata](./media/howto-attach-disk-window-linux/InitializeSuccess.png)

## <a id="initializeinlinux" ></a>Procedura: Inizializzare un nuovo disco dati in Linux

1.  Connettersi alla macchina virtuale utilizzando i passaggi descritti in precedenza in [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines-linux-how-to-log-on/).

2.  Nella finestra di SSH digitare il comando seguente, quindi immettere la password per l'account creato per la gestione della macchina virtuale:
    
    `sudo grep SCSI /var/log/messages`
    
    L'identificatore dell'ultimo disco dati aggiunto verrà indicato nei messaggi visualizzati.
    
    ![Visualizzare i messaggi relativi al
    disco](./media/howto-attach-disk-window-linux/DiskMessages.png)

3.  Nella finestra di SSH digitare il comando seguente per creare un nuovo dispositivo, quindi immettere la password per l'account:
    
    `sudo fdisk /dev/sdc`


   
    > [WACOM.NOTE] In questo esempio potrebbe essere necessario
    > utilizzare `sudo -i` in alcune distribuzioni se /sbin o /usr/sbin
    > non sono disponibili in `$PATH`.

4.  Digitare **n** per creare una nuova partizione.
    
    ![Creare un nuovo
    dispositivo](./media/howto-attach-disk-window-linux/DiskPartition.png)

5.  Digitare **p** per impostare la partizione come primaria, digitare **1** per impostarla come prima partizione, quindi premere INVIO per accettare il valore predefinito per il cilindro.
    
    ![Creare la
    partizione](./media/howto-attach-disk-window-linux/DiskCylinder.png)

6.  Digitare **p** per visualizzare i dettagli relativi al disco da suddividere in partizioni.
    
    ![Visualizzare le informazioni sul
    disco](./media/howto-attach-disk-window-linux/DiskInfo.png)

7.  Digitare **w** per scrivere le impostazioni per il disco.
    
    ![Scrivere le modifiche sul
    disco](./media/howto-attach-disk-window-linux/DiskWrite.png)

8.  È necessario creare il file system nella nuova partizione. Ad esempio, digitare il comando seguente per creare il file system, quindi immettere la password per l'account:
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![Creare il file
    system](./media/howto-attach-disk-window-linux/DiskFileSystem.png)


   
    > [WACOM.NOTE] Si noti i sistemi SUSE Linux Enterprise 11
    > forniscono l'accesso in sola lettura ai file system ext4. Per
    > questi sistemi è consigliabile formattare il nuovo file system
    > come ext3 anziché ext4.

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


   
    > [WACOM.NOTE] blkid potrebbe non richiedere l'accesso sudo in
    > tutti i casi ma potrebbe risultare più facile eseguirlo con `sudo
    > -i` su distribuzioni in cui /sbin o /usr/sbin non sono presenti
    > nei percorsi indicati in `$PATH`.
    
    **Attenzione:** se il file /etc/fstab non viene modificato in modo corretto, il sistema potrebbe diventare instabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.
    
    Utilizzare un editor di testo per immettere le informazioni sul nuovo file system alla fine del file /etc/fstab. In questo esempio verranno utilizzati il valore UUID del nuovo dispositivo **/dev/sdc1** creato nella procedura precedente, nonché il punto di montaggio **/datadrive**:
    
        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`
    
    Oppure, su sistemi basati su SUSE Linux potrebbe essere necessario utilizzare un formato leggermente diverso:
    
        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`
    
    Anche le eventuali altre unità o partizioni dati create dovranno essere inserite separatamente in /etc/fstab.
    
    Per verificare che il file system venga montato correttamente, è sufficiente smontarlo e rimontarlo, utilizzando ad esempio il punto di montaggio `/datadrive` creato nei passaggi precedenti:
    
        `sudo umount /datadrive`
        `sudo mount /datadrive`
    
    Se il secondo comando restituisce un errore, verificare il file /etc/fstab per ottenere la sintassi corretta.


   
    > [WACOM.NOTE] Se si rimuove successivamente un disco dati senza
    > modificare fstab, è possibile che si verifichi un errore di avvio
    > della macchina virtuale. Se si tratta di un errore ricorrente,
    > nella maggior parte delle distribuzioni sono disponibili le
    > opzioni `nofail` e/o `nobootwait`, che consentono l'avvio di un
    > sistema anche in caso di assenza del disco. Per ulteriori
    > informazioni su tali parametri, fare riferimento alla
    > documentazione della distribuzione.



[1]: http://go.microsoft.com/fwlink/p/?LinkId=391661
[2]: http://www.windowsazure.com/en-us/downloads/
[3]: http://go.microsoft.com/FWLink/p/?LinkID=294683
[4]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/jj672979.aspx
[6]: http://manage.windowsazure.com
[7]: http://go.microsoft.com/FWLink/p/?LinkID=391684