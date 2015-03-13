<properties 
	pageTitle="Ottimizzazione delle prestazioni di MySQL in macchine virtuali Linux di Azure" 
	description="Informazioni su come ottimizzare MySQL in esecuzione su una macchina virtuale di Azure che esegue Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="NingKuang" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="ningk"/>

#Ottimizzazione delle prestazioni di MySQL in macchine virtuali Linux di Azure 

Esistono molti fattori che influiscono sulle prestazioni di MySQL in Azure, sia nella selezione dell'hardware virtuale sia nella configurazione software. Questo articolo è incentrato sull'ottimizzazione delle prestazioni tramite la configurazione dell'archiviazione, del sistema e del database.

##Usare RAID in una macchina virtuale di Azure 
L'archiviazione è il fattore che influisce maggiormente sulle prestazioni del database negli ambienti cloud.  Rispetto a un singolo disco, RAID può fornire un accesso più rapido tramite la concorrenza.  Per altre informazioni, fare riferimento alla pagina relativa ai [livelli RAID standard](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Tramite RAID è possibile migliorare notevolmente la velocità effettiva di I/O del disco e il tempo di risposta di I/O in Azure. I test di laboratorio indicano che in media la velocità effettiva di I/O del disco può essere raddoppiata e il tempo di risposta di I/O può essere dimezzato raddoppiando il numero di dischi RAID (da 2 a 4, da 4 a 8 e così via). Per informazioni dettagliate, vedere l'[Appendice A](#AppendixA).  

Oltre all'I/O del disco, aumentando il livello RAID migliorano anche le prestazioni di MySQL.  Per informazioni dettagliate, vedere l'[Appendice B](#AppendixB).  

Può inoltre essere utile prendere in considerazione le dimensioni del blocco. In generale, con blocchi di dimensioni maggiori si avrà un sovraccarico ridotto, soprattutto per le scritture di grandi dimensioni. Tuttavia, blocchi di dimensioni troppo grandi potrebbero far aumentare il sovraccarico, rendendo impossibile l'uso del RAID. La dimensione predefinita corrente, dimostratasi ottimale per la maggior parte degli ambienti di produzione, è pari a 512 KB. Per informazioni dettagliate, vedere l'[Appendice C](#AppendixC).   

Si noti che esistono limiti al numero di dischi che è possibile aggiungere per i diversi tipi di macchine virtuali. Tali limiti sono descritti in dettaglio in [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Per l'esempio RAID proposto in questo articolo si dovrà disporre di 4 dischi dati collegati, anche se è possibile scegliere di configurare RAID con un numero di dischi inferiore.  

In questo articolo si presuppone che l'utente abbia già creato una macchina virtuale Linux e che MYSQL sia già stato installato e configurato. Per altre informazioni, fare riferimento alla documentazione sulle modalità di installazione di MySQL in Azure.  
  
###Configurare RAID in Azure
La seguente procedura illustra la creazione di RAID in Azure tramite il portale di gestione di Microsoft Azure. È inoltre possibile configurare RAID usando gli script di Windows PowerShell. 
In questo esempio verrà configurato RAID 0 con 4 dischi.  

####Passaggio 1: Aggiungere un disco dati alla macchina virtuale  

Nella pagina relativa alle macchine virtuali del portale di gestione di Azure selezionare la macchina virtuale a cui si desidera aggiungere un disco dati. In questo esempio la macchina virtuale è mysqlnode1.  

![][1]

Nella pagina della macchina virtuale fare clic su **Dashboard**.  

![][2]
 

Nella barra delle applicazioni fare clic su **Connetti**.
 
![][3]

Quindi fare clic su **Connetti disco vuoto**.  

![][4]
 
Per i dischi dati, l'opzione **Preferenze cache dell'host** deve essere impostata su **Nessuno**.  

Verrà aggiunto un disco vuoto nella macchina virtuale. Ripetere questo passaggio tre volte in modo da disporre di 4 dischi dati per RAID.  

È possibile visualizzare le unità aggiunte nella macchina virtuale esaminando il log dei messaggi del kernel. Ad esempio, per visualizzarlo in Ubuntu, usare il seguente comando:  

	sudo grep SCSI /var/log/dmesg

####Passaggio 2: Creare RAID con i dischi aggiuntivi
Per la procedura dettagliata di installazione di RAID, fare riferimento a questo articolo :  

[http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-RAID/](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-RAID/)

>[AZURE.NOTE] Se si usa il file system XFS, eseguire la seguente procedura dopo aver creato RAID.

Per installare XFS su Debian, Ubuntu o Linux Mint, usare il seguente comando:  

	apt-get -y install xfsprogs  

Per installare XFS su Fedora, CentOS o RHEL, usare il seguente comando:  

	yum -y install xfsprogs  xfsdump 


####Passaggio 3: Impostare un nuovo percorso di archiviazione
Usare il seguente comando:  

	root@mysqlnode1:~# mkdir -p /RAID0/mysql

####Passaggio 4: Copiare i dati originali nel nuovo percorso di archiviazione
Usare il seguente comando:  

	root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####Passaggio 5: Modificare le autorizzazioni in modo che MySQL possa avere accesso in lettura e in scrittura al disco dati
Usare il seguente comando:  

	root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##Modificare l'algoritmo di pianificazione di I/O del disco
Linux implementa quattro tipi di algoritmi di pianificazione di I/O:  

-	Algoritmo NOOP (Nessuna operazione)
-	Algoritmo di scadenza (Scadenza)
-	Algoritmo di accodamento pienamente equo (CFQ, Completely fair queuing)
-	Algoritmo del periodo di budget (Anticipatorio)  

Per ottimizzare le prestazioni è possibile selezionare diverse utilità di pianificazione di I/O in scenari diversi. In un ambiente ad accesso completamente casuale non esiste una differenza significativa tra gli algoritmi CFQ e di scadenza a livello di prestazioni. In genere, è consigliabile impostare l'ambiente del database MySQL sull'algoritmo di scadenza per maggiore stabilità. Se è presente un I/O sequenziale considerevole, l'algoritmo CFQ può ridurre le prestazioni di I/O del disco.   

Per le unità SSD e altri dispositivi, con gli algoritmi NOOP o di scadenza è possibile ottenere prestazioni migliori rispetto all'utilità di pianificazione predefinita.   

Dal kernel 2.5, l'algoritmo di pianificazione di I/O predefinito è quello di scadenza. A partire dal kernel 2.6.18, l'algoritmo CFQ è diventato l'algoritmo di pianificazione di I/O predefinito.  È possibile specificare questa impostazione in fase di avvio del kernel oppure modificare questa impostazione in maniera dinamica mentre il sistema è in esecuzione.  

Il seguente esempio mostra come controllare e impostare l'utilità di pianificazione predefinita per l'algoritmo NOOP.  

Per il gruppo di distribuzione Debian:

###Passaggio 1. Visualizzare l'unità di pianificazione di I/O corrente.
Usare il seguente comando:  

	root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler 

Si visualizzerà il seguente output, che indica l'utilità di pianificazione corrente.  

	noop [deadline] cfq 


###Passaggio 2. Modificare il dispositivo corrente (/dev/sda) dell'algoritmo di pianificazione di I/O
Usare il seguente comando:  

	azureuser@mysqlnode1:~$ sudo su -
	root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Impostare questa opzione solo per /dev/sda è inutile. L'opzione deve essere impostata su tutti i dischi dati in cui si trova il database.  

Si dovrebbe visualizzare il seguente output, che indica che grub.cfg è stato ricreato correttamente e che l'utilità di pianificazione predefinita è stata impostata su NOOP.  

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Per il gruppo di distribuzione Redhat è necessario solo il seguente comando:   

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##Configurare le impostazioni per le operazioni del file system
Una delle procedure consigliate consiste nel disabilitare la funzionalità di registrazione dell'atime nel file system. L'atime è l'ora dell'ultimo accesso al file. Ogni volta che si accede a un file, il file system registra il timestamp nel log. Tuttavia, queste informazioni vengono usate raramente. Se non è necessaria, è possibile disabilitare questa opzione, riducendo così il tempo complessivo di accesso al disco.  
 
Per disattivare la registrazione dell'atime, è necessario modificare il file di configurazione del file system /etc/. fstab e aggiungere l'opzione **noatime**.  

Modificare ad esempio il file vim /etc/fstab aggiungendo l'opzione noatime come illustrato di seguito.  

	# CLOUD_IMG: This file was created/modified by the Cloud Image build process
	UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
	#Add the "noatime" option below to disable atime logging
	UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
	/dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Rimontare il file system con il seguente comando:  

	mount -o remount /RAID0

Verificare il risultato modificato. Si noti che quando si modifica il file di test, il tempo di accesso non viene aggiornato.  

Prima (esempio):		

![][5]
 
Dopo (esempio):

![][6]

##Aumentare il numero massimo di handle di sistema per la concorrenza elevata
MySQL è un database a concorrenza elevata. Il numero predefinito di handle simultanei per Linux è pari a 1024, ma non sempre è sufficiente. **Eseguire la seguente procedura per aumentare il numero massimo di handle simultanei del sistema, in modo da supportare la concorrenza elevata di MySQL**.

###Passaggio 1: Modificare il file limits.conf
Aggiungere le seguenti quattro righe nel file /etc/security/limits.conf per aumentare il numero massimo di handle simultanei consentiti. Si noti che 65536 è il numero massimo di handle che il sistema è in grado di supportare.   

	* soft nofile 65536
	* hard nofile 65536
	* soft nproc 65536
	* hard nproc 65536

###Passaggio 2: Aggiornare il sistema con i nuovi limiti
Attivare i seguenti comandi:  

	ulimit -SHn 65536
	ulimit -SHu 65536 

###Passaggio 3: Assicurarsi che i limiti vengano aggiornati in fase di avvio
Immettere i seguenti comandi di avvio nel file /etc/rc.local in modo che la modifica sia applicata a ogni avvio.  

	echo "ulimit -SHn 65536" >>/etc/rc.local
	echo "ulimit -SHu 65536" >>/etc/rc.local

##Ottimizzare il database MySQL 
Per configurare MySQL in Azure è possibile usare la stessa strategia di ottimizzazione delle prestazioni di un computer locale.  

Le principali regole di ottimizzazione di I/O sono:   

-	Aumentare le dimensioni della cache.
-	Ridurre i tempi di risposta I/O.

Per ottimizzare le impostazioni del server MySQL, è possibile aggiornare il file my.cnf, ovvero il file di configurazione predefinito per i computer server e client.  

I seguenti elementi di configurazione sono i principali fattori che influiscono sulle prestazioni di MySQL:  

-	**innodb_buffer_pool_size**: il pool di buffer contiene i dati memorizzati nel buffer e l'indice. In genere è impostato al 70% della memoria fisica.
-	**innodb_log_file_size**: la dimensione del log di ripristino. È possibile usare i log di ripristino per assicurare che le operazioni di scrittura siano veloci, affidabili e recuperabili dopo un arresto anomalo. È impostato su 512 MB, pertanto sarà disponibile una notevole quantità di spazio per la registrazione delle operazioni di scrittura.
-	**max_connections**: in alcuni casi le applicazioni non chiudono correttamente le connessioni. Un valore più elevato garantirà al server più tempo per riciclare le connessioni inattive. Il numero massimo di connessioni è 10.000, ma quello consigliato è 5000.
-	**Innodb_file_per_table**: questa impostazione consente di abilitare o disabilitare la capacità di InnoDB di archiviare le tabelle in file separati. Una volta attiva, l'opzione assicura che le diverse operazioni avanzate di amministrazione vengano eseguite in modo efficiente. Dal punto di vista delle prestazioni, può velocizzare la trasmissione degli spazi di tabella e ottimizzare le prestazioni della gestione dei detriti. L'impostazione consigliata è quindi ON.</br>
	Da MySQL 5.6, l'impostazione predefinita è ON. Non è pertanto necessaria alcuna azione. Per le versioni precedenti alla 5.6, l'impostazione predefinita è OFF. L'opzione deve essere quindi attivata ed è necessario farlo prima del caricamento dati, in quanto si applica solo alle tabelle appena create.
-	**innodb_flush_log_at_trx_commit**: il valore predefinito è 1, con l'ambito impostato su 0~2. Il valore predefinito è l'opzione più adatta per il database MySQL come pacchetto autonomo. Se impostato su 2, il valore assicura la massima integrità dei dati ed è appropriato per il server Master nel cluster MySQL. Se impostato su 0, il valore tollera la perdita di dati e questo può influire sulla affidabilità, garantendo in alcuni casi prestazioni migliori. Il valore 0 è appropriato per il server Slave nel cluster MySQL.
-	**Innodb_log_buffer_size**: il buffer del log consente l'esecuzione delle transazioni senza scaricare il log sul disco prima del commit delle transazioni. Tuttavia, se è presente un oggetto binario o un campo di testo di grandi dimensioni, la cache verrà utilizzata molto rapidamente e verrà attivato un I/O frequente del disco. Se la variabile di stato Innodb_log_waits è diversa da 0, è consigliabile aumentare le dimensioni del buffer.
-	**query_cache_size**: è consigliabile disabilitare questa opzione fin dall'inizio. Impostare query_cache_size su 0 (in MySQL 5.6 questa è l'impostazione predefinita) e usare altri metodi per velocizzare le query.  
  
Vedere l'[Appendice D](AppendixD) per il confronto delle prestazioni dopo l'ottimizzazione.


##Attivare il log di query lente di MySQL per l'analisi del collo di bottiglia delle prestazioni
Il log di query lente di MySQL consente di identificare le query lente per MySQL. Dopo l'abilitazione del log di query lente di MySQL, è possibile usare strumenti di MySQL come **mysqldumpslow** per identificare il collo di bottiglia delle prestazioni.  

Si noti che il log non è abilitato per impostazione predefinita. L'attivazione del log di query lente può utilizzare alcune risorse della CPU. È pertanto consigliabile abilitarlo temporaneamente per la risoluzione dei colli di bottiglia delle prestazioni.

###Passaggio 1: Modificare il file my.cnf aggiungendo alla fine del file le seguenti righe   

	long_query_time = 2
	slow_query_log = 1
	slow_query_log_file = /RAID0/mysql/mysql-slow.log

###Passaggio 2: Riavviare il server mysql
	service  mysql  restart

###Passaggio 3: Verificare se l'impostazione è stata applicata tramite il comando "show"
 
![][7]   
   
![][8]
 
Come si può vedere, in questo esempio è stata attivata la funzionalità relativa alle query lente. È quindi possibile usare lo strumento **mysqldumpslow** per individuare i colli di bottiglia delle prestazioni e ottimizzare le prestazioni, ad esempio aggiungendo indici.





##Appendice

Di seguito sono riportati i dati di esempio dei test sulle prestazioni prodotti nell'ambiente di destinazione, che forniscono informazioni generali sulla tendenza dei dati delle prestazioni con i diversi approcci di ottimizzazione delle prestazioni. I risultati possono tuttavia variare in ambienti o versioni di prodotto diverse. 

<a name="AppendixA"></a>Appendice A: **Prestazioni del disco (IOPS) con livelli RAID diversi** 


![][9]
 
**Comandi di test:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE.NOTE: Il carico di lavoro di questo test usa 64 thread, al fine di raggiungere il limite massimo di RAID.

<a name="AppendixB"></a>Appendice B:  
**Confronto delle prestazioni (velocità effettiva) di MySQL con livelli RAID diversi**   
(File system XFS)

 
![][10]  
![][11]

**Comandi di test:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write -engine=innodb

**Confronto delle prestazioni (OLTP) di MySQL con livelli RAID diversi**  
![][12]

**Comandi di test:**

	time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Appendice C:   
**Confronto delle prestazioni (IOPS) del disco per dimensioni del blocco diverse**
(File system XFS)

 
![][13]

**Comandi di test:**  

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Si noti che le dimensioni del file usato per il test sono pari rispettivamente a 30 GB e 1 GB, con file system XFS RAID 0(4 dischi).


<a name="AppendixD"></a>Appendice D:  
**Confronto delle prestazioni (velocità effettiva) di MySQL prima e dopo l'ottimizzazione**
(File system XFS)

  
![][14]

**Comandi di test:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write -engine=innodb,misam

**L'impostazione di configurazione per impostazione predefinita e per l'ottimizzazione è la seguente:**

|Parametri	|Impostazione predefinita	|Ottimizzazione
|-----------|-----------|-----------
|**innodb_buffer_pool_size**	|Nessuno	|7G
|**innodb_log_file_size**	|5M	|512M
|**max_connections**	|100	|5000
|**innodb_file_per_table**	|0	|1
|**innodb_flush_log_at_trx_commit**	|1	|2
|**innodb_log_buffer_size**	|8M	|128M
|**query_cache_size**	|16M	|0


Per parametri di configurazione dell'ottimizzazione più dettagliati, fare riferimento alle istruzioni ufficiali di MySQL.  

[http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Ambiente di test**

|Hardware	|Dettagli
|-----------|-------
|Cpu	|AMD Opteron(tm) Processore 4171 HE/4 core
|Memoria	|14G
|disco	|10G/disco
|Sistema operativo	|Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-14.png




<!--HONumber=42-->
