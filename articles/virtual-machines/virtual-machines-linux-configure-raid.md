<properties 
	pageTitle="Configurare RAID software in una macchina virtuale che esegue Linux in Azure" 
	description="Informazioni su come usare mdadm per configurare RAID in Linux in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	writer="szark" 
	manager="timlt" 
	editor=""/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>




# Configurare RAID software in Linux
I RAID software vengono spesso usati nelle macchine virtuali Linux in Azure per presentare più dischi dati collegati come se si trattasse di un singolo dispositivo RAID. In genere questa configurazione consente di migliorare le prestazioni e la velocità effettiva rispetto all'utilizzo di un unico disco.


## Collegamento di dischi dati
Per configurare un dispositivo RAID sono in genere necessari due dischi dati. In questo articolo non verrà illustrato in dettaglio come collegare dischi dati a una macchina virtuale Linux. Per istruzioni dettagliate su come collegare un disco dati vuoto a una macchina virtuale Linux in Azure, vedere l'articolo di Azure relativo al [collegamento di dischi](storage-windows-attach-disk.md#attachempty).

>[AZURE.NOTE]Nelle macchine virtuali di dimensioni ExtraSmall è supportato il collegamento di un solo disco dati. Per informazioni dettagliate sulle dimensioni delle macchine virtuali e sul numero di dischi dati supportati, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Microsoft Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).


## Installazione dell'utility mdadm

- **Ubuntu**

		# sudo apt-get update
		# sudo apt-get install mdadm

- **CentOS e Oracle Linux**

		# sudo yum install mdadm

- **SLES e openSUSE**

		# zypper install mdadm


## Creazione delle partizioni del disco
In questo esempio verrà creata una singola partizione del disco in /dev/sdc. La nuova partizione del disco verrà quindi denominata /dev/sdc1.

- Avviare fdisk per iniziare la creazione delle partizioni

		# sudo fdisk /dev/sdc
		Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
		Building a new DOS disklabel with disk identifier 0xa34cb70c.
		Changes will remain in memory only, until you decide to write them.
		After that, of course, the previous content won't be recoverable.

		WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
				 switch off the mode (command 'c') and change display units to
				 sectors (command 'u').

- Premere 'n' al prompt per creare una **n**uova partizione:

		Command (m for help): n

- Successivamente, premere 'p' per creare una partizione **p**rimaria:

		Command action
			e   extended
			p   primary partition (1-4)
		p

- Premere '1' per selezionare la partizione numero 1:

		Partition number (1-4): 1

- Selezionare il punto di inizio della nuova partizione oppure premere semplicemente `<enter>` per accettare le impostazioni predefinite, che prevedono il posizionamento della partizione all'inizio dello spazio libero nell'unità:

		First cylinder (1-1305, default 1):
		Using default value 1

- Selezionare le dimensioni della partizione, ad esempio digitare '+10G' per creare una partizione da 10 gigabyte. In alternativa, premere `<enter>` per creare un'unica partizione che occupa l'intera unità:

		Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
		Using default value 1305

- Successivamente, modificare l'ID e il **t**ipo della partizione dal valore predefinito '83' (Linux) a 'fd' (rilevamento automatico RAID Linux):

		Command (m for help): t
		Selected partition 1
		Hex code (type L to list codes): fd

- Infine, scrivere la tabella delle partizioni sull'unità e chiudere fdisk:

		Command (m for help): w
		The partition table has been altered!


## Creazione dell'array RAID

1. Nell'esempio seguente verrà eseguito lo striping (livello RAID 0) di tre partizioni situate in tre dischi dati separati (sdc1, sdd1, sde1):

		# sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
		  /dev/sdc1 /dev/sdd1 /dev/sde1

In questo esempio, dopo l'esecuzione del comando verrà creato un nuovo dispositivo RAID denominato **/dev/md127**. Si noti inoltre che, se i dischi dati facevano precedentemente parte di una matrice RAID inattiva, può essere necessario aggiungere il parametro `--force` al comando `mdadm`.


2. Creare il file system nel nuovo dispositivo RAID

	**CentOS, Oracle Linux, SLES 12, openSUSE e Ubuntu**

		# sudo mkfs -t ext4 /dev/md127

	**SLES 11**

		# sudo mkfs -t ext3 /dev/md127

3. **SLES 11 & openSUSE** - abilitare boot.md e creare mdadm.conf

		# sudo -i chkconfig --add boot.md
		# sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

	>[AZURE.NOTE]Dopo aver apportato queste modifiche nei sistemi SUSE può essere necessario il riavvio. Questo passaggio *non* è obbligatorio su SLES 12.


## Aggiungere il nuovo file a /etc/fstab

**Attenzione**: se il file /etc/fstab non viene modificato in modo corretto, il sistema potrebbe diventare instabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

1. Creare il punto di montaggio desiderato per il nuovo file system, ad esempio:

		# sudo mkdir /data

2. Quando si modifica /etc/fstab è consigliabile utilizzare l'**UUID** anziché il nome del dispositivo per fare riferimento al file system. Usare l'utilità `blkid` per determinare l'UUID per il nuovo file system:

		# sudo /sbin/blkid
		...........
		/dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3. Aprire /etc/fstab in un editor di testo e aggiungere una voce per il nuovo file system, ad esempio:

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

	O su **SLES 11 & openSUSE**:

		/dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

	Salvare e chiudere /etc/fstab.

4. Verificare che la voce /etc/fstab sia corretta:

		# sudo mount -a

	Se questo comando genera un messaggio di errore, verificare la sintassi nel file /etc/fstab file.

	Eseguire quindi il comando `mount` per assicurarsi che il file system venga montato:

		# mount
		.................
		/dev/md127 on /data type ext4 (rw)

5. (Facoltativo) Parametri di avvio di operatore alternativo

	**Configurazione di fstab**

	Molte distribuzioni includono i parametri di montaggio `nobootwait` o `nofail`, che è possibile aggiungere al file /etc/fstab. Tali parametri consentono di ignorare gli errori durante il montaggio di uno specifico file system. Consentono pertanto di proseguire l'avvio del sistema Linux anche se non è possibile montare correttamente il file system RAID. Per ulteriori informazioni su questi parametri, fare riferimento alla documentazione della distribuzione.

	Esempio (Ubuntu):

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

	**Parametri di avvio di Linux**

	Oltre ai parametri precedenti, il parametro del kernel "`bootdegraded=true`" consente di avviare il sistema anche se il RAID viene percepito come danneggiato o con funzionalità ridotte, ad esempio se un'unità dati viene rimossa accidentalmente dalla macchina virtuale. Per impostazione predefinita, questa situazione può rendere impossibile l'avvio del sistema.

	Per informazioni sulla corretta modifica dei parametri del kernel, fare riferimento alla documentazione della distribuzione. Ad esempio, in molte distribuzioni (CentOS, Oracle Linux, SLES 11) è possibile aggiungere manualmente tali parametri al file "`/boot/grub/menu.lst`". In Ubuntu è possibile aggiungere il parametro `GRUB_CMDLINE_LINUX_DEFAULT` alla variabile in "/etc/default/grub".

 

<!---HONumber=August15_HO6-->