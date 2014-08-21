1.  Nel [portale di gestione Azure (Anteprima)][AzurePreviewPortal], fare clic su **Virtual Machines** e selezionare la macchina virtuale appena creata (**testlinuxvm**).

2.  Sulla barra dei comandi fare clic su **Attach** e quindi su **Attach Empty Disk**.
    
    Verrà visualizzata la finestra di dialogo **Attach Empty Disk**.

3.  Le impostazioni **Virtual Machine Name**, **Storage Location** e **File Name** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Digitare **5** nel campo **Size**.
    
	![Attach Empty Disk](./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png)

    
    **Nota:** tutti i dischi vengono creati da un file VHD in  Archiviazione di Azure. È possibile specificare un nome per il file VHD aggiunto alla risorsa di archiviazione, tuttavia il nome del disco viene generato automaticamente in Azure.

4.  Fare clic sul segno di spunta per collegare il disco dati alla macchina virtuale.

5.  Fare clic sul nome della macchina virtuale da visualizzare nel dashboard. In questo modo è possibile verificare che il disco dati sia stato collegato correttamente alla macchina virtuale.
    
    A questo punto la macchina virtuale è collegata a due dischi. Il disco collegato verrà visualizzato nella tabella **Disks**.
    
	![Attach Empty Disk](./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM3.png)

    
    Quando viene collegato alla macchina virtuale, il disco dati è offline e non inizializzato. Prima di poter archiviare i dati, è necessario accedere alla macchina virtuale e inizializzare il disco.

## Connettersi alla macchina virtuale tramite SSH o PuTTY e completare la configurazione

Il disco dati appena collegato alla macchina virtuale è offline e non è inizializzato dopo l'aggiunta. Per archiviare i dati, è necessario accedere alla macchina e inizializzare il disco da utilizzare.

1.  Dopo avere effettuato il provisioning della macchina virtuale, connettersi tramite SSH o PuTTY ed eseguire l'accesso come **newuser** (come descritto nei passaggi precedenti).

2.  Nella finestra di SSH o PuTTY digitare il comando seguente e quindi immettere la password dell'account:
    
    `$ sudo grep SCSI /var/log/messages`
    
    L'identificatore dell'ultimo disco dati aggiunto sarà disponibile nei messaggi visualizzati (in questo esempio, **sdc**).
    
    ![GREP](./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png)

3.  Nella finestra di SSH o PuTTY immettere il comando seguente per effettuare la partizione del disco **/dev/sdc**:
    
    `$ sudo fdisk /dev/sdc`

4.  Immettere **n** per creare una nuova partizione.
    
    ![FDISK](./media/attach-data-disk-centos-vm-in-portal/fdisk1.png)

5.  Premere **p** per impostare la partizione come primaria, digitare **1** per impostarla come prima partizione, quindi premere INVIO per accettare il valore predefinito (1) per il cilindro.
    
    ![FDISK](./media/attach-data-disk-centos-vm-in-portal/fdisk2.png)

6.  Digitare **p** per visualizzare i dettagli relativi al disco da suddividere in partizioni.
    
    ![FDISK](./media/attach-data-disk-centos-vm-in-portal/fdisk3.png)

7.  Digitare **w** per scrivere le impostazioni per il disco.
    
    ![FDISK](./media/attach-data-disk-centos-vm-in-portal/fdisk4.png)

8.  Formattare il nuovo disco mediante il comando **mkfs**:
    
    `$ sudo mkfs -t ext4 /dev/sdc1`
    

	![Creare il file system](./media/howto-attach-disk-window-linux/DiskFileSystem.png)

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