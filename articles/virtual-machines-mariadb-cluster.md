<properties 
	pageTitle="Esecuzione di un cluster MariaDB (MySQL) in Azure" 
	description="Creare un cluster MariaDB + Galera MySQL in Macchine virtuali di Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="sabbour" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="12/3/2014" 
	ms.author="v-ahsab" 
	ms.prod="azure"/>

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Cluster MariaDB (MySQL) - Esercitazione su Azure

<p>In questa esercitazione verrà creato un cluster multi-master [Galera](http://galeracluster.com/products/) di database [MariaDB](https://mariadb.org/en/about/), un sostituto robusto, scalabile e pronto all'uso di MySQL, da usare in un ambiente a disponibilità elevata in Macchine virtuali di Azure.</p>

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

+ [Panoramica dell'architettura] 
+ [Creazione del modello]
+ [Creazione del cluster]
+ [Bilanciamento del carico del cluster]
+ [Convalida del cluster]


## Panoramica dell'architettura

Questo argomento illustra la procedura per:

1. Creare un cluster a 3 nodi
2. Separare i dischi dati dal disco del sistema operativo
3. Creare i dischi dati in configurazione RAID 0/striping per aumentare il numero di IOPS
4. Usare il servizio di bilanciamento del carico di Azure per bilanciare il carico dei 3 nodi
5. Per ridurre le operazioni ripetitive, creare un'immagine di macchina virtuale contenente MariaDB e Galera e usarla per creare le altre macchine virtuali del cluster.

![Architecture](./media/virtual-machines-mariadb-cluster/Setup.png)

> [AZURE.NOTE]  In questo argomento vengono usati gli strumenti dell'[interfaccia della riga di comando (CLI) di Azure], accertarsi quindi di scaricarli e connetterli alla sottoscrizione di Azure in base alle istruzioni. Per informazioni sui comandi disponibili, vedere il [riferimento agli strumenti da riga di comando di Azure]. Sarà inoltre necessario [creare una chiave SSH per l'autenticazione] e prendere nota del **percorso del file PEM**.


## Creazione del modello

### Infrastruttura

1. Creare un gruppo di affinità per contenere le risorse

		azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
        
2. Creare una rete virtuale

		azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Creare un account di archiviazione per ospitare tutti i dischi. Si noti che non è consigliabile inserire più di 40 dischi molto usati nello stesso account di archiviazione, per evitare di raggiungere il limite di 20.000 IOPS per account. Nel caso di questo esempio si è molto lontani da tale valore, dunque per motivi di semplicità verrà archiviato tutto nello stesso account.

		azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Individuare il nome dell'immagine di macchina virtuale CentOS 7

		azure vm image list | findstr CentOS        
verrà restituito un risultato simile a `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Usare il nome nel passaggio successivo.

4. Creare il modello di macchina virtuale sostituendo **/path/to/key.pem** con il percorso in cui è stata archiviata la chiave SSH PEM generata

		azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
        
5. Collegare 4 dischi dati da 500 GB alla macchina virtuale da usare in configurazione RAID

		FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd        

6. Connettersi tramite SSH al modello di macchina virtuale creato all'indirizzo **mariadbhatemplate.cloudapp.net:22** e connettersi usando la propria chiave privata.

### Software

1. Ottenere l'accesso radice

        sudo su
        
2. Installare il supporto RAID:

     - Installare mdadm
        
        		yum install mdadm
                
     - Creare la configurazione RAID 0/striping con un file system EXT4
        
				mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
				mdadm --detail --scan >> /etc/mdadm.conf
				mkfs -t ext4 /dev/md0
         
     - Creare la directory del punto di montaggio
         
				mkdir /mnt/data
                
     - Recuperare l'UUID del dispositivo RAID appena creato
        
				blkid | grep /dev/md0
    
     - Modificare /etc/fstab
        
        		vi /etc/fstab
        
     - Aggiungere il dispositivo per abilitare il montaggio automatico al riavvio sostituendo l'UUID con il valore ottenuto dal comando **blkid** precedente
        
        		UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
        	
     - Montare la nuova partizione
        
        		mount /mnt/data
            
3. Installare MariaDB:    
		
     - Creare il file MariaDB.repo 
        
              	vi /etc/yum.repos.d/MariaDB.repo
        
     - Riempirlo con il contenuto seguente      
        
				[mariadb]
				name = MariaDB
				baseurl = http://yum.mariadb.org/10.0/centos7-amd64
				gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
				gpgcheck=1
    
     - Rimuovere postfix e mariadb-libs per evitare conflitti
    
    		yum remove postfix mariadb-libs-*
            
     - Installare MariaDB con Galera
    
    		yum install MariaDB-Galera-server MariaDB-client galera
   
4. Spostare la directory dati MySQL nel dispositivo a blocchi RAID

     - Copiare la directory MySQL nel nuovo percorso e rimuovere la vecchia directory
    
    		cp -avr /var/lib/mysql /mnt/data  
    		rm -rf /var/lib/mysql
      
     - Impostare le autorizzazioni nella nuova directory di conseguenza
        
        	chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  
            
     - Creare un collegamento simbolico che punti la vecchia directory al nuovo percorso nella partizione RAID
    
    		ln -s /mnt/data/mysql /var/lib/mysql   

5. Poiché [SELinux interferisce con le operazioni cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), è necessario disabilitarlo per la sessione corrente (finché non sarà disponibile una versione compatibile). Modificare  `/etc/selinux/config` per disabilitarlo per i riavvii successivi:
    	
	        setenforce 0
    
       quindi modificare `/etc/selinux/config` per impostare `SELINUX=permissive`
       
6. Convalidare le esecuzioni di MySQL

    - Avviare MySQL
    
    		service mysql start
            
    - Proteggere l'installazione di MySQL impostando la password radice, rimuovendo gli utenti anonimi, disabilitando la connessione da remoto come utente radice e rimuovendo il database di test
    		
            mysql_secure_installation
            
    - Creare un utente nel database per le operazioni cluster e, facoltativamente, le applicazioni
   
			mysql -u root -p
			GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit
   
   - Arrestare MySQL
   
			service mysql stop
            
7. Creare il segnaposto di configurazione

	- Modificare la configurazione di MySQL per creare un segnaposto per le impostazioni del cluster. Non sostituire **`<Vairables>`** né rimuovere simboli di commento in questa fase. L'operazione va eseguita dopo la creazione di una macchina virtuale dal modello.
	
			vi /etc/my.cnf.d/server.cnf
			
	- Modificare la sezione **[galera]** e cancellarla
	
	- Modificare la sezione **[mariadb]**
	
			wsrep_provider=/usr/lib64/galera/libgalera_smm.so 
            binlog_format=ROW
            wsrep_sst_method=rsync
			bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
			default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2
			
            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
			#wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Aprire le porte necessarie sul firewall (usando FirewallD su CentOS 7)
	
	- MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Ricaricare il firewall: `firewall-cmd --reload`
	
9.  Ottimizzare le prestazioni del sistema. Per altre informazioni, fare riferimento a questo articolo sulla [strategia di ottimizzazione delle prestazioni]

	- Modificare di nuovo il file di configurazione di MySQL
	
			vi /etc/my.cnf.d/server.cnf
		
	- Modificare la sezione **[mariadb]** e aggiungere quanto segue
	
	> [AZURE.NOTE] È consigliabile che **innodb\_buffer\_pool_size** equivalga al 70% della memoria della macchina virtuale. Qui è stato impostato su 2,45 GB per la macchina virtuale di Azure di dimensione Media con 3,5 GB di RAM.
	
	        innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
			query_cache_size = 0
			
10. Arrestare MySQL, disabilitare l'esecuzione all'avvio del servizio MySQL per evitare di rovinare il cluster quando si aggiunge un nuovo nodo ed eseguire il deprovisioning della macchina.

		service mysql stop
        chkconfig mysql off
		waagent -deprovision	
		
11. Acquisire la macchina virtuale tramite il portale. (Attualmente il [problema noto #1268 relativo agli strumenti dell'interfaccia della riga di comando di Azure] descrive il fatto che le immagini acquisite dagli strumenti di xplat-cli non acquisiscono i dischi dati collegati.)

	- Arrestare la macchina tramite il portale
    - Fare clic su Acquisisci e specificare il nome dell'immagine come **mariadb-galera-image**, fornire una descrizione e selezionare "Il comando "waagent -deprovision" è stato eseguito nella macchina virtuale".
	![Capture the Virtual Machine](./media/virtual-machines-mariadb-cluster/Capture.png)
	![Capture the Virtual Machine](./media/virtual-machines-mariadb-cluster/Capture2.PNG)
	
## Creazione del cluster
 
Creare 3 macchine virtuali sulla base del modello appena creato e quindi configurare e avviare il cluster.

1. Creare la prima macchina virtuale CentOS 7 dall'immagine **mariadb-galera-image**. Fornire il nome della rete virtuale **mariadbvnet**, la subnet **mariadb** e le dimensioni della macchina **Medium**, passare il nome del servizio cloud **mariadbha** (o qualsiasi altro nome a cui si desidera accedere tramite mariadbha.cloudapp.net), impostare il nome del computer **mariadb1** e il nome utente **azureuser**, abilitare l'accesso SSH, passare il file PEM del certificato SSH e sostituire **/path/to/key.pem** con il percorso in cui è stato archiviata la chiave SSH PEM generata.

	> [AZURE.NOTE] I comandi riportati di seguito sono suddivisi su più righe per maggiore chiarezza, ma è necessario immetterli in un'unica riga.

		azure vm create 
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium 
		--ssh-cert "/path/to/key.pem" 
		--no-ssh-password 
		--ssh 22 
		--vm-name mariadb1 
		mariadbha mariadb-galera-image azureuser 

2. Creare altre 2 macchine virtuali _collegandole_ al servizio cloud **mariadbha** attualmente creato, modificando il **nome della macchina virtuale** e la **porta SSH**. Inserire il numero di una porta univoca non in conflitto con altre macchine virtuali nello stesso servizio cloud.

		azure vm create		
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 23
		--vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
Per MariaDB3

		azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 24
		--vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
		
3. Per il passaggio successivo è necessario ottenere l'indirizzo IP interno di ognuna delle 3 macchine virtuali:

	![Getting IP address](./media/virtual-machines-mariadb-cluster/IP.png)

4. Connettersi tramite SSH alle 3 macchine virtuali e quindi modificare il file di configurazione in ognuna di esse

		sudo vi /etc/my.cnf.d/server.cnf
		
	rimuovendo i simboli di commento da **`wsrep_cluster_name`** e **`wsrep_cluster_address`** cancellando il simbolo **#** all'inizio e verificando che il valore sia quello desiderato.
    Inoltre, sostituire **`<ServerIP>`** in **`wsrep_node_address`** e **`<NodeName>`** in **`wsrep_node_name`** rispettivamente con l'indirizzo IP e il nome della macchina virtuale, quindi rimuovere i simboli di commento dalle righe di codice.
	
5. Avviare il cluster in MariaDB1 e lasciare che venga eseguito all'avvio

		sudo service mysql bootstrap
        chkconfig mysql on
	
6. Avviare MySQL in MariaDB2 e MariaDB3 e lasciare che venga eseguito all'avvio

		sudo service mysql start
        chkconfig mysql on
		
## Bilanciamento del carico del cluster
Durante la creazione, le macchine virtuali in cluster sono state aggiunte a un set di disponibilità denominato **clusteravset**, per garantire che vengano inserite in domini di errore e di aggiornamento diversi e che Azure non esegua mai contemporaneamente la manutenzione su tutte le macchine. Questa configurazione soddisfa i requisiti di idoneità per il contratto di servizio di Azure.

Ora è possibile usare il servizio di bilanciamento del carico di Azure per bilanciare le richieste tra i 3 nodi.

Eseguire i comandi seguenti nel computer tramite l'interfaccia della riga di comando di Azure.
La struttura dei parametri di comando è: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

	azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306
	
Infine, poiché l'interfaccia della riga di comando imposta l'intervallo dei probe del servizio di bilanciamento del carico su 15 secondi, che può essere un valore troppo elevato, modificare il valore nel portale in **Endpoint** per tutte le macchine virtuali

![Edit endpoint](./media/virtual-machines-mariadb-cluster/Endpoint.PNG)

fare clic su Riconfigura il set con carico bilanciato e andare avanti

![Reconfigure Load Balanced Set](./media/virtual-machines-mariadb-cluster/Endpoint2.PNG)

quindi modificare Intervallo di probe in 5 secondi e salvare

![Change Probe Interval](./media/virtual-machines-mariadb-cluster/Endpoint3.PNG)

## Convalida del cluster

Il più è fatto. Il cluster dovrebbe essere accessibile all'indirizzo `mariadbha.cloudapp.net:3306`, che raggiungerà il servizio di bilanciamento del carico e instraderà le richieste tra le 3 macchine virtuali in modo semplice ed efficiente.

Usare il client MySQL di propria scelta per connettersi oppure connettersi semplicemente tramite una delle macchine virtuali per verificare il funzionamento corretto di questo cluster.

	 mysql -u cluster -h mariadbha.cloudapp.net -p
	 
Creare quindi un nuovo database e popolarlo con una serie di dati

	CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
	INSERT INTO TestTable (value)  VALUES ('Value1');
	INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;
	
Verrà generata la tabella seguente

	+----+--------+
	| id | value  |
	+----+--------+
	|  1 | Value1 |
	|  4 | Value2 |
	+----+--------+
	2 rows in set (0.00 sec)	
	
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

In questo articolo è stato creato un cluster a disponibilità elevata MariaDB + Galera a 3 nodi in Macchine virtuali di Azure con CentOS 7. Il bilanciamento del carico delle macchine virtuali viene eseguito mediante il servizio di bilanciamento del carico di Azure.

Può essere utile consultare gli articoli sul [metodo alternativo per creare un cluster MySQL su Linux] e sui metodi per [ottimizzare e testare le prestazioni di MySQL in macchine virtuali Linux di Azure].

<!--Anchors-->
[Panoramica dell'architettura]: #architecture-overview
[Creazione del modello]: #creating-the-template
[Creazione del cluster]: #creating-the-cluster
[Bilanciamento del carico del cluster]: #load-balancing-the-cluster
[Convalida del cluster]: #validating-the-cluster
[Passaggi successivi]: #next-steps

<!--Image references-->

<!--Link references-->
[Azure CLI]: http://azure.microsoft.com/documentation/articles/xplat-cli/
[riferimento agli strumenti da riga di comando di Azure]: http://azure.microsoft.com/documentation/articles/command-line-tools/
[creare una chiave SSH per l'autenticazione]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[strategia di ottimizzazione delle prestazioni]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[ottimizzare e testare le prestazioni di MySQL in macchine virtuali Linux di Azure]:http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[problema noto #1268 relativo agli strumenti dell'interfaccia della riga di comando di Azure]:https://github.com/Azure/azure-xplat-cli/issues/1268
[metodo alternativo per creare un cluster MySQL su Linux]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/



<!--HONumber=42-->
