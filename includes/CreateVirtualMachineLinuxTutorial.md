<!---
Don't use this file. It's deprecated and will be removed. Instead use, virtual-machines-Linux-tutorial-log-on-attach-disk.md
-->

## <a id="logon"> </a>Come accedere alla macchina virtuale dopo averla creata ##

Per gestire le impostazioni della macchina virtuale e le applicazioni da eseguirvi all'interno, è possibile usare un client SSH. A tale scopo, è necessario installare un client SSH nel computer che si intende usare per accedere alla macchina virtuale. È disponibile un'ampia scelta di programmi client SSH. Di seguito vengono indicate alcune tra le scelte possibili:

- Se si usa un computer che esegue un sistema operativo Windows, è possibile usare un client SSH come PuTTY. Per altre informazioni, vedere la pagina [PuTTY Download](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Se si usa un computer che esegue un sistema operativo Linux, è possibile usare un client SSH come OpenSSH. Per altre informazioni, vedere la pagina [OpenSSH](http://www.openssh.org/).

In questa esercitazione viene illustrato l'utilizzo del programma PuTTY per accedere alla macchina virtuale.

1. Individuare le informazioni **Host Name** e **Port information** nel portale di gestione. È possibile rilevare le informazioni necessarie dal dashboard della macchina virtuale. Fare clic sul nome della macchina virtuale e individuare **SSH Details** nella sezione **Quick Glance** del dashboard.

	![Individuare i dettagli SSH](./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png)

2. Aprire il programma PuTTY.

3. Immettere in **Host Name** e **Port information** i dati raccolti dal dashboard, quindi fare clic su **Open**.

	![Immettere il nome host e le informazioni sulla porta](./media/CreateVirtualMachineLinuxTutorial/putty.png)

4. Accedere alla macchina virtuale usando l'account NewUser1 specificato durante la creazione della macchina.

	![Accesso alla nuova macchina virtuale](./media/CreateVirtualMachineLinuxTutorial/sshlogin.png)

	È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.


## <a id="attachdisk"> </a>Come collegare un disco dati alla nuova macchina virtuale ##

L'applicazione in uso potrebbe richiedere l'archiviazione di dati. Per impostare questa funzionalità è necessario collegare un disco dati alla macchina virtuale creata in precedenza. Il modo più semplice per procedere consiste nel collegare un disco dati vuoto alla macchina virtuale.

**Nota: disco dati e disco risorse** I dischi dati risiedono nell'archiviazione di Azure e possono essere usati come archivio permanente per file e dati dell'applicazione.

Ogni macchina virtuale creata dispone inoltre di un *disco risorse* temporaneo locale collegato. Poiché è possibile che i dati su un disco risorse non vengano mantenuti tra un riavvio e l'altro, questo tipo di disco viene spesso usato da applicazioni e processi in esecuzione nella macchina virtuale per l'archiviazione temporanea di dati. Viene inoltre usato per archiviare file di paging o di scambio per il sistema operativo.

In Linux il disco risorse è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu). Si noti che il disco risorse è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. D'altra parte, in Linux il kernel potrebbe assegnare al disco dati il nome `/dev/sdc`; in tal caso gli utenti dovranno suddividere in partizioni, formattare e montare tale risorsa. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](http://www.windowsazure.com/manage/linux/how-to-guides/linux-agent-guide/).



1. Accedere al portale di gestione di Azure se questa operazione non è già stata eseguita.

2. Fare clic su **Virtual Machines**, quindi selezionare la macchina virtuale **MyTestVM1** creata in precedenza.

3. Sulla barra dei comandi fare clic su **Connetti**, quindi su **Connetti disco vuoto**.
	
	Verrà visualizzata la finestra di dialogo **Connetti disco vuoto**.

	![Definizione dei dettagli del disco](./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png)

4. Le impostazioni **Virtual Machine Name**, **Storage Location** e **File Name** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Digitare **5** nel campo **Size**.

	**Nota**: tutti i dischi vengono creati da un file VHD in Archiviazione di Azure. Il nome del disco viene generato automaticamente ma è tuttavia possibile specificare un nome per il file VHD aggiunto alla risorsa di archiviazione.

5. Fare clic sul segno di spunta per collegare il disco dati alla macchina virtuale.

6. È possibile verificare che il disco dati sia stato collegato correttamente alla macchina virtuale visualizzando il dashboard. Per visualizzare il dashboard, fare clic sul nome della macchina virtuale.

	Il numero di dischi nella macchina virtuale è ora pari a 2 e il disco collegato è elencato nella tabella **Disks**.

	![Collegamento disco completato](./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png)


Il disco dati appena collegato alla macchina virtuale è offline e non è inizializzato dopo l'aggiunta. Per archiviare i dati, è necessario accedere alla macchina e inizializzare il disco da usare.

1. Connettersi alla macchina virtuale usando i passaggi descritti in precedenza in **Come accedere alla macchina virtuale dopo averla creata**.


2. Nella finestra di SSH digitare il comando seguente e quindi immettere la password dell'account:

	`sudo grep SCSI /var/log/messages`

	L'identificatore dell'ultimo disco dati aggiunto verrà indicato nei messaggi visualizzati.

	![Identificazione disco](./media/CreateVirtualMachineLinuxTutorial/diskmessages.png)


3. Nella finestra di SSH digitare il comando seguente per creare un nuovo dispositivo, quindi immettere la password per l'account:

	`sudo fdisk /dev/sdc`

	>[AZURE.NOTE]In questo esempio potrebbe essere necessario usare `sudo -i` in alcune distribuzioni se /sbin o /usr/sbin non sono disponibili in `$PATH`.


4. Digitare **n** per creare una nuova partizione.

	![Creare un nuovo dispositivo](./media/CreateVirtualMachineLinuxTutorial/diskpartition.png)


5. Digitare **p** per impostare la partizione come primaria, digitare **1** per impostarla come prima partizione, quindi premere INVIO per accettare il valore predefinito per il cilindro.

	![Creare la partizione](./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png)


6. Digitare **p** per visualizzare i dettagli relativi al disco da suddividere in partizioni.

	![Visualizzare le informazioni sul disco](./media/CreateVirtualMachineLinuxTutorial/diskinfo.png)


7. Digitare **w** per scrivere le impostazioni per il disco.

	![Scrivere le modifiche sul disco](./media/CreateVirtualMachineLinuxTutorial/diskwrite.png)


8. È necessario creare il file system nella nuova partizione. Ad esempio, digitare il comando seguente per creare il file system, quindi immettere la password per l'account:

	`sudo mkfs -t ext4 /dev/sdc1`

	![Creare il file system](./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png)

	>[AZURE.NOTE]Si noti che i sistemi SUSE Linux Enterprise 11 forniscono l'accesso in sola lettura ai file system ext4. Per questi sistemi è consigliabile formattare il nuovo file system come ext3 anziché ext4.


9. È quindi necessario disporre di una directory per il montaggio del nuovo file system. Digitare, ad esempio, il comando seguente per creare una nuova directory per il montaggio dell'unità, quindi immettere la password per l'account:

	`sudo mkdir /datadrive`


10. Digitare il comando seguente per montare l'unità:

	`sudo mount /dev/sdc1 /datadrive`

	Il disco dati è ora pronto per l'uso come **/datadrive**.


11. Aggiungere la nuova unità a /etc/fstab:

	Per assicurarsi che l'unità venga rimontata automaticamente dopo un riavvio, è necessario aggiungerla al file /etc/fstab. È inoltre consigliabile che l'UUID (Universally Unique IDentifier) usato in /etc/fstab faccia riferimento all'unità anziché al solo nome del dispositivo, ad esempio /dev/sdc1. Per individuare l'UUID della nuova unità, è possibile usare l'utilità **blkid**:
	
		`sudo -i blkid`

	L'output sarà simile al seguente:

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[AZURE.NOTE]blkidw potrebbe non richiedere l'accesso sudo in tutti i casi ma potrebbe risultare più facile eseguirlo con `sudo -i` su distribuzioni in cui /sbin o /usr/sbin non sono presenti nei percorsi indicati in `$PATH`.

	**Attenzione**: se il file /etc/fstab non viene modificato in modo corretto, il sistema potrebbe diventare instabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

	Usare un editor di testo per immettere le informazioni sul nuovo file system alla fine del file /etc/fstab. In questo esempio verranno usati il valore UUID del nuovo dispositivo **/dev/sdc1** creato nella procedura precedente, nonché il punto di montaggio **/datadrive**:

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	Oppure, su sistemi basati su SUSE Linux potrebbe essere necessario usare un formato leggermente diverso:

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	Anche le eventuali altre unità o partizioni dati create dovranno essere inserite separatamente in /etc/fstab.

	Per verificare che il file system venga montato correttamente, è sufficiente smontarlo e rimontarlo, usando ad esempio il punto di montaggio `/datadrive` creato nei passaggi precedenti:

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Se il secondo comando restituisce un errore, verificare il file /etc/fstab per ottenere la sintassi corretta.


	>[AZURE.NOTE]Se si rimuove successivamente un disco dati senza modificare fstab, è possibile che si verifichi un errore di avvio della macchina virtuale. Se si tratta di un errore ricorrente, nella maggior parte delle distribuzioni sono disponibili le opzioni `nofail` e/o `nobootwait` fstab, che consentono l'avvio di un sistema anche in caso di assenza del disco. Per altre informazioni su tali parametri, fare riferimento alla documentazione della distribuzione.

<!---HONumber=July15_HO4-->