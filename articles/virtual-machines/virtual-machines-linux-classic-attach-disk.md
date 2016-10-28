<properties
	pageTitle="Collegare un disco a una macchina virtuale Linux | Microsoft Azure"
	description="Informazioni su come collegare un disco dati a una macchina virtuale Azure e inizializzarlo in modo che sia pronto per l'uso."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="iainfou"/>

# Come collegare un disco dati a una macchina virtuale Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Vedere come [aggiungere un disco dati tramite il modello di distribuzione Resource Manager](virtual-machines-linux-add-disk.md).

È possibile collegare sia dischi vuoti sia dischi contenenti dati alle VM di Azure. In entrambi i casi i dischi sono file con estensione vhd che risiedono in un account di archiviazione di Azure. Come con l'aggiunta di un disco a un computer Linux, dopo aver collegato il disco, è necessario inizializzarlo e formattarlo affinché sia pronto per l'uso. Questo articolo illustra in dettaglio come collegare sia i dischi vuoti sia i dischi contenenti dati alle VM e come inizializzare e formattare successivamente un nuovo disco.

> [AZURE.NOTE] È consigliabile usare uno o più dischi separati per archiviare i dati di una macchina virtuale. Al momento della creazione, una macchina virtuale di Azure dispone di un disco del sistema operativo e di un disco temporaneo. **Non usare il disco temporaneo per archiviare i dati persistenti.** Come si può dedurre dal nome, fornisce solo archiviazione temporanea. Non offre funzionalità di ridondanza o backup perché non risiede nel servizio di archiviazione di Azure. Il disco temporaneo è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu). D'altra parte, il kernel potrebbe assegnare a un disco dati il nome `/dev/sdc`; in tal caso è necessario suddividere in partizioni, formattare e montare tale risorsa. Per dettagli, vedere [Guida dell'utente dell'agente Linux di Azure][Agent].

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## Inizializzare un nuovo disco dati in Linux

1. Stabilire una connessione SSH alla VM. Per altre informazioni, vedere [Come accedere a una macchina virtuale che esegue Linux][Logon].

2. In seguito è necessario trovare l'identificatore del dispositivo per inizializzare il disco dati. A questo scopo è possibile procedere in due modi:

	a) Grep per dispositivi SCSI nei log, come nel comando seguente:

			$sudo grep SCSI /var/log/messages

	Per le distribuzioni recenti di Ubuntu, può essere necessario usare `sudo grep SCSI /var/log/syslog` perché la registrazione in `/var/log/messages` potrebbe essere disattivata per impostazione predefinita.

	L'identificatore dell'ultimo disco dati aggiunto verrà indicato nei messaggi visualizzati.

	![Visualizzare i messaggi relativi al disco](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

	OPPURE

	b) Usare il comando `lsscsi` per trovare l'ID dispositivo. `lsscsi` può essere installato da `yum install lsscsi` (su distribuzioni basate su Red Hat) o `apt-get install lsscsi` (su distribuzioni basate su Debian). È possibile trovare il disco che si sta cercando in base al relativo _LUN_ o **numero di unità logica**. Ad esempio, il _LUN_ per i dischi collegati può essere facilmente trovato in `azure vm disk list <virtual-machine>` come:

			~$ azure vm disk list TestVM
			info:    Executing command vm disk list
			+ Fetching disk images
			+ Getting virtual machines
			+ Getting VM disks
			data:    Lun  Size(GB)  Blob-Name                         OS
			data:    ---  --------  --------------------------------  -----
			data:         30        TestVM-2645b8030676c8f8.vhd  Linux
			data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
			info:    vm disk list command OK

	Confrontare questi dati con l'output di `lsscsi` per la stessa macchina virtuale di esempio:

			ops@TestVM:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

	L'ultimo numero della tupla in ogni riga è il _lun_. Per altre informazioni, vedere `man lsscsi`.

3. Al prompt dei comandi, digitare il comando seguente per creare un dispositivo:

		$sudo fdisk /dev/sdc


4. Quando richiesto, digitare **n** per creare una nuova partizione.


	![Creare un dispositivo](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. Quando richiesto, digitare **p** per impostare la partizione come partizione primaria. Digitare **1** per impostarla come prima partizione, quindi premere INVIO per accettare il valore predefinito per il cilindro. In alcuni sistemi, è possibile visualizzare i valori predefiniti del primo e dell'ultimo settore e non del cilindro. È possibile scegliere di accettare questi valori predefiniti.


	![Creare la partizione](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. Digitare **p** per visualizzare i dettagli relativi al disco da suddividere in partizioni.


	![Visualizzare le informazioni sul disco](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. Digitare **w** per scrivere le impostazioni per il disco.


	![Scrivere le modifiche sul disco](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. È ora possibile creare il file system nella nuova partizione. Aggiungere il numero della partizione all'ID del dispositivo (nell'esempio seguente `/dev/sdc1`). Nell'esempio seguente viene creata una partizione ext4 in /dev/sdc1:

		# sudo mkfs -t ext4 /dev/sdc1

	![Creare il file system](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

	>[AZURE.NOTE] Che i sistemi SUSE Linux Enterprise 11 supportano solo l'accesso di sola lettura ai file system ext4. Per questi sistemi è consigliabile formattare il nuovo file system come ext3 anziché ext4.


9. Creare una directory per il montaggio del nuovo file system nel modo seguente:

		# sudo mkdir /datadrive


10. È infine possibile montare l'unità, come indicato di seguito:

		# sudo mount /dev/sdc1 /datadrive

	Il disco dati è ora pronto per l'uso come **/datadrive**.
	
	![Creare la directory e montare il disco](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. Aggiungere la nuova unità a /etc/fstab:

	Per assicurarsi che l'unità venga rimontata automaticamente dopo un riavvio, è necessario aggiungerla al file /etc/fstab. È inoltre consigliabile che l'UUID (Universally Unique IDentifier) usato in /etc/fstab faccia riferimento all'unità anziché al solo nome del dispositivo, ad esempio /dev/sdc1. L'utilizzo dell'UUID evita che venga montato il disco non corretto in una posizione specificata, se il sistema operativo rileva un errore del disco durante l'avvio, e l'assegnazione di tali ID dispositivo agli eventuali dischi dati rimanenti. Per individuare l'UUID della nuova unità, è possibile usare l'utilità **blkid**:

		# sudo -i blkid

	L'output è simile al seguente:

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE] Se il file **/etc/fstab** non viene modificato in modo corretto, il sistema potrebbe diventare non avviabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

	Successivamente, aprire il file **/etc/fstab** in un editor di testo:

		# sudo vi /etc/fstab

	In questo esempio vengono usati il valore UUID del nuovo dispositivo **/dev/sdc1** creato nei passaggi precedenti, nonché il punto di montaggio **/datadrive**. Alla fine del file **/etc/fstab** aggiungere la riga di codice seguente:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	In alternativa, su sistemi basati su SUSE Linux, potrebbe essere necessario usare un formato leggermente diverso:

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults   1   2

	Per verificare che il file system venga montato correttamente, è possibile smontarlo e rimontarlo, usando ad esempio il punto di montaggio `/datadrive` creato nei passaggi precedenti:

		# sudo umount /datadrive
		# sudo mount /datadrive

	Se il comando `mount` restituisce un errore, verificare che la sintassi del file /etc/fstab sia corretta. Anche le eventuali altre unità o partizioni dati create devono essere inserite separatamente in /etc/fstab.

	Rendere scrivibile l'unità tramite questo comando:

		# sudo chmod go+w /datadrive

>[AZURE.NOTE] Se si rimuove successivamente un disco dati senza modificare fstab, è possibile che si verifichi un errore di avvio della macchina virtuale. Se si tratta di un errore ricorrente, nella maggior parte delle distribuzioni sono disponibili le opzioni fstab `nofail` e/o `nobootwait`, che consentono l'avvio di un sistema anche in caso di errore di montaggio in fase di avvio. Per altre informazioni su questi parametri, consultare la documentazione della distribuzione.

### Supporto delle funzioni TRIM/UNMAP per Linux in Azure
Alcuni kernel di Linux supportano operazioni TRIM/UNMAP allo scopo di rimuovere i blocchi inutilizzati sul disco. Nel servizio di archiviazione standard, queste operazioni sono particolarmente utili per informare Azure che le pagine eliminate non sono più valide e possono essere rimosse. L'eliminazione delle pagine consente di risparmiare sui costi quando si creano file di grandi dimensioni per poi eliminarli.

Esistono due modi per abilitare la funzione TRIM in una VM Linux. Come di consueto, consultare la documentazione della distribuzione per stabilire l'approccio consigliato:

- Usare l'opzione di montaggio `discard` in `/etc/fstab`, ad esempio:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- In alternativa, è possibile eseguire il comando `fstrim` manualmente dalla riga di comando oppure aggiungerlo a crontab per eseguirlo a intervalli regolari:

	**Ubuntu**

		# sudo apt-get install util-linux
		# sudo fstrim /datadrive

	**RHEL/CentOS**

		# sudo yum install util-linux
		# sudo fstrim /datadrive

## Risoluzione dei problemi
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]


## Passaggi successivi
Per altre informazioni sull'uso delle VM Linux, vedere gli articoli seguenti:

- [Come accedere a una macchina virtuale che esegue Linux][Logon]

- [Informazioni su come scollegare un disco da una macchina virtuale Linux](virtual-machines-linux-classic-detach-disk.md)

- [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione servizi di Azure (asm)](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md

<!---HONumber=AcomDC_0831_2016-->