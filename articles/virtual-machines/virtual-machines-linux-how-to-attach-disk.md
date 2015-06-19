<properties
	pageTitle="Collegare un disco a una macchina virtuale che esegue Linux in Azure"
	description="Informazioni su come collegare un disco dati a una macchina virtuale di Azure e inizializzarlo in modo che sia pronto per l'uso."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2015"
	ms.author="kathydav"/>

#Come collegare un disco dati a una macchina virtuale Linux

È possibile collegare sia dischi vuoti sia dischi contenenti dati. In entrambi i casi, i dischi sono effettivamente file con estensione vhd che risiedono in un account di archiviazione di Azure. Inoltre, dopo avere collegato il disco, sarà necessario inizializzarlo affinché sia pronto per l'uso.

> [AZURE.NOTE]È consigliabile usare uno o più dischi separati per archiviare i dati di una macchina virtuale. Al momento della creazione, una macchina virtuale di Azure dispone di un disco del sistema operativo e di un disco temporaneo. **Non usare il disco temporaneo per archiviare i dati.** Come si può dedurre dal nome, fornisce solo archiviazione temporanea. Non offre funzionalità di ridondanza o backup perché non risiede nel servizio di archiviazione di Azure. Il disco temporaneo è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu). In Linux, tuttavia, il kernel può assegnare al disco dati il nome `/dev/sdc`. In questo caso sarà necessario eseguire la partizione, la formattazione e il montaggio della risorsa. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](http://azure.microsoft.com/manage/linux/how-to-guides/linux-agent-guide/).

- [Procedura: Collegare un disco vuoto](#attachempty)
- [Procedura: Collegare un disco esistente](#attachexisting)
- [Procedura: Inizializzare un nuovo disco dati in Linux](#initializeinlinux)

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinlinux"></a>Procedura: Inizializzare di un nuovo disco dati in Linux



1. Connettersi alla macchina virtuale utilizzando i passaggi descritti in precedenza in [Come accedere a una macchina virtuale che esegue Linux](logonlinux).



2. Nella finestra di SSH digitare il comando seguente, quindi immettere la password per l'account creato per la gestione della macchina virtuale:

		# sudo grep SCSI /var/log/messages

	>[AZURE.NOTE]Per le distribuzioni recenti di Ubuntu, può essere necessario usare `sudo grep SCSI /var/log/syslog` perché la registrazione in `/var/log/messages` potrebbe essere disattivata per impostazione predefinita.

	L'identificatore dell'ultimo disco dati aggiunto verrà indicato nei messaggi visualizzati.



	![Visualizzare i messaggi relativi al disco](./media/virtual-machines-linux-how-to-attach-disk/DiskMessages.png)



3. Nella finestra di SSH digitare il comando seguente per creare un nuovo dispositivo, quindi immettere la password per l'account:

		# sudo fdisk /dev/sdc

	>[AZURE.NOTE]In questo esempio potrebbe essere necessario usare `sudo -i` in alcune distribuzioni se /sbin o /usr/sbin non sono disponibili in `$PATH`.


4. Digitare **n** per creare una nuova partizione.


	![Creare un nuovo dispositivo](./media/virtual-machines-linux-how-to-attach-disk/DiskPartition.png)

5. Digitare **p** per impostare la partizione come primaria, digitare **1** per impostarla come prima partizione, quindi premere INVIO per accettare il valore predefinito per il cilindro.


	![Creare la partizione](./media/virtual-machines-linux-how-to-attach-disk/DiskCylinder.png)



6. Digitare **p** per visualizzare i dettagli relativi al disco da suddividere in partizioni.


	![Visualizzare le informazioni sul disco](./media/virtual-machines-linux-how-to-attach-disk/DiskInfo.png)



7. Digitare **w** per scrivere le impostazioni per il disco.


	![Scrivere le modifiche sul disco](./media/virtual-machines-linux-how-to-attach-disk/DiskWrite.png)

8. Creare il file system nella nuova partizione. Ad esempio, digitare il comando seguente e quindi immettere la password dell'account:

		# sudo mkfs -t ext4 /dev/sdc1

	![Creare il file system](./media/virtual-machines-linux-how-to-attach-disk/DiskFileSystem.png)

	>[AZURE.NOTE]Si noti che i sistemi SUSE Linux Enterprise 11 supportano solo l'accesso di sola lettura ai file system ext4. Per questi sistemi è consigliabile formattare il nuovo file system come ext3 anziché ext4.


9. Creare una directory per il montaggio del nuovo file system. Ad esempio, digitare il comando seguente e quindi immettere la password dell'account:

		# sudo mkdir /datadrive


10. Digitare il comando seguente per montare l'unità:

		# sudo mount /dev/sdc1 /datadrive

	Il disco dati è ora pronto per l'uso come **/datadrive**.


11. Aggiungere la nuova unità a /etc/fstab:

	Per assicurarsi che l'unità venga rimontata automaticamente dopo un riavvio, è necessario aggiungerla al file /etc/fstab. È inoltre consigliabile che l'UUID (Universally Unique IDentifier) usato in /etc/fstab faccia riferimento all'unità anziché al solo nome del dispositivo, ad esempio /dev/sdc1. Per individuare l'UUID della nuova unità, è possibile usare l'utilità **blkid**:

		# sudo -i blkid

	L'output sarà simile al seguente:

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE]Se il file **/etc/fstab** non viene modificato in modo corretto, il sistema potrebbe diventare non avviabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

	Successivamente, aprire il file **/etc/fstab** in un editor di testo. Si noti che /etc/fstab è un file system, quindi per apportare modifiche a questo file è necessario usare `sudo`, ad esempio:

		# sudo vi /etc/fstab

	In questo esempio verranno usati il valore UUID del nuovo dispositivo **/dev/sdc1** creato nei passaggi precedenti, nonché il punto di montaggio **/datadrive**. Alla fine del file **/etc/fstab** aggiungere la riga di codice seguente:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	Oppure, su sistemi basati su SUSE Linux potrebbe essere necessario usare un formato leggermente diverso:

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2

	Per verificare che il file system venga montato correttamente, è sufficiente smontarlo e rimontarlo, usando ad esempio il punto di montaggio `/datadrive` creato nei passaggi precedenti:

		# sudo umount /datadrive
		# sudo mount /datadrive

	Se il comando `mount` restituisce un errore, verificare che la sintassi del file /etc/fstab sia corretta. Anche le eventuali altre unità o partizioni dati create dovranno essere inserite separatamente in /etc/fstab.


	>[AZURE.NOTE]Se si rimuove successivamente un disco dati senza modificare fstab, è possibile che si verifichi un errore di avvio della macchina virtuale. Se si tratta di un errore ricorrente, nella maggior parte delle distribuzioni sono disponibili le opzioni fstab `nofail` e/o `nobootwait`, che consentono l'avvio di un sistema anche in caso di errore di montaggio in fase di avvio. Per altre informazioni su tali parametri, fare riferimento alla documentazione della distribuzione.

[logonlinux]: virtual-machines-linux-how-to-log-on.md

<!---HONumber=58--> 