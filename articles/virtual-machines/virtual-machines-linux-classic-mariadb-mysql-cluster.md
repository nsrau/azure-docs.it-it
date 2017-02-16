---
title: Esecuzione di un cluster MariaDB (MySQL) in Azure | Microsoft Docs
description: Creare un cluster MariaDB + Galera MySQL in macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: 
author: sabbour
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
translationtype: Human Translation
ms.sourcegitcommit: 91090ad383d6a03e6d978376d8dd45dc05eac7e8
ms.openlocfilehash: 504e810146d6787eac9be661a93c7a0ec2ff183f


---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>Cluster MariaDB (MySQL) - Esercitazione su Azure
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) e la distribuzione classica. In questo articolo viene illustrato il modello di distribuzione classica. Microsoft consiglia di usare il modello di Azure Resource Manager per le distribuzioni più recenti.

> [!NOTE]
> Il cluster MariaDB Enterprise è ora disponibile in Azure Marketplace. La nuova offerta distribuirà automaticamente un cluster MariaDB Galera su Azure Resource Manager. È consigliabile usare la nuova offerta da [Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/).
>
>

Questo articolo illustra come creare un cluster multi-master [Galera](http://galeracluster.com/products/) di database [MariaDBs](https://mariadb.org/en/about/), un sostituto pronto all'uso per MySQL, per lavorare in un ambiente a disponibilità elevata in macchine virtuali di Azure.

## <a name="architecture-overview"></a>Panoramica dell'architettura
In questo articolo viene descritto come completare i passaggi seguenti:

- Creare un cluster a tre nodi.
- Separare i dischi dati dal disco del sistema operativo.
- Creare i dischi dati in configurazione RAID&0;/striping per aumentare il numero di IOPS.
- Usare Azure Load Balancer per bilanciare il carico dei&3; nodi.
- Per ridurre le operazioni ripetitive, creare un'immagine di macchina virtuale contenente MariaDB e Galera e usarla per creare le altre macchine virtuali del cluster.

![Architettura di sistema](media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> In questo argomento vengono usati gli strumenti dell'[interfaccia della riga di comando di Azure](../xplat-cli-install.md); accertarsi quindi di scaricarli e connetterli alla sottoscrizione di Azure in base alle istruzioni. Per informazioni sui comandi disponibili nell’interfaccia della riga di comando di Azure, vedere il [riferimento all'interfaccia della riga di comando di Azure](../virtual-machines-command-line-tools.md). Sarà inoltre necessario [creare una chiave SSH per l'autenticazione] e prendere nota del percorso del file .pem.
>
>

## <a name="create-the-template"></a>Creare il modello
### <a name="infrastructure"></a>Infrastruttura
1. Creare un gruppo di affinità per contenere le risorse.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Creare una rete virtuale.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Creare un account di archiviazione per ospitare tutti i dischi. Non è consigliabile inserire più di 40 dischi molto usati nello stesso account di archiviazione, per evitare di raggiungere il limite di 20.000 IOPS per account. Nel caso di questo esempio si è molto lontani da tale valore, dunque per motivi di semplicità verrà archiviato tutto nello stesso account.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Individuare il nome dell'immagine di macchina virtuale CentOS 7.

        azure vm image list | findstr CentOS
   Il risultato sarà simile a `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Usare il nome nel passaggio successivo.
5. Creare il modello di macchina virtuale sostituendo /path/to/key.pem con il percorso in cui è stata archiviata la chiave SSH .pem generata.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Collegare 4 dischi dati da 500 GB alla macchina virtuale da usare in configurazione RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Usare SSH per accedere al modello di macchina virtuale creato all'indirizzo mariadbhatemplate.cloudapp.net:22 e connettersi usando la chiave privata.

### <a name="software"></a>Software
1. Ottenere la radice.

        sudo su

2. Installare il supporto RAID:

    a. Installare mdadm.

              yum install mdadm

    b. Creare la configurazione RAID&0;/striping con un file system EXT4.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Creare la directory del punto di montaggio.

              mkdir /mnt/data
    d. Recuperare l'UUID del dispositivo RAID appena creato.

              blkid | grep /dev/md0
    e. Modificare /etc/fstab.

              vi /etc/fstab
    f. Aggiungere il dispositivo per abilitare il montaggio automatico al riavvio sostituendo l'UUID con il valore ottenuto dal comando **blkid** precedente.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Montare la nuova partizione.

              mount /mnt/data

3. Installare MariaDB.

    a. Creare il file MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Inserire nel file repo il contenuto seguente:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Rimuovere postfix e mariadb-libs per evitare conflitti.

           yum remove postfix mariadb-libs-*
    d. Installare MariaDB con Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Spostare la directory dati MySQL nel dispositivo a blocchi RAID.

    a. Copiare la directory MySQL nel nuovo percorso e rimuovere la vecchia directory.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Impostare di conseguenza le autorizzazioni per la nuova directory.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Creare un collegamento simbolico che punti la vecchia directory al nuovo percorso nella partizione RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Poiché [SELinux interferisce con le operazioni cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), è necessario disabilitarlo per la sessione corrente. Modificare `/etc/selinux/config` per disabilitarlo per i riavvii successivi.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Convalidare le esecuzioni di MySQL.

   a. Avviare MySQL.

           service mysql start
   b. Proteggere l'installazione di MySQL impostando la password radice, rimuovendo gli utenti anonimi per disabilitare la connessione da remoto come utente radice e rimuovendo il database di test.

           mysql_secure_installation
   c. Creare un utente nel database per le operazioni cluster e, facoltativamente, le applicazioni.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Arrestare MySQL.

            service mysql stop
7. Creare un segnaposto di configurazione.

   a. Modificare la configurazione di MySQL per creare un segnaposto per le impostazioni del cluster. Non sostituire **`<Variables>`** né rimuovere simboli di commento in questa fase. L'operazione va eseguita dopo la creazione di una macchina virtuale dal modello.

            vi /etc/my.cnf.d/server.cnf
   b. Modificare la sezione **[galera]** e cancellarla.

   c. Modificare la sezione **[mariadb]**.

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
8. Aprire le porte necessarie sul firewall usando FirewallD su CentOS 7.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Ricaricare il firewall: `firewall-cmd --reload`

9. Ottimizzare le prestazioni del sistema. Per altre informazioni, vedere la [strategia di ottimizzazione delle prestazioni](virtual-machines-linux-classic-optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

   a. Modificare di nuovo il file di configurazione di MySQL.

            vi /etc/my.cnf.d/server.cnf
   b. Modificare la sezione **[mariadb]** e aggiungere quanto segue:

   > [!NOTE]
   > innodb\_buffer\_pool_size dovrebbe essere il 70% della memoria della macchina virtuale. In questo esempio il valore è stato impostato su 2,45 GB per una macchina virtuale di Azure media con 3,5 GB di RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Arrestare MySQL, disabilitare l'esecuzione all'avvio del servizio MySQL per evitare di rovinare il cluster quando si aggiunge un nodo ed eseguire il deprovisioning della macchina.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Acquisire la macchina virtuale tramite il portale. (Attualmente il [problema noto n. 1268 nell’interfaccia della riga di comando di Azure](https://github.com/Azure/azure-xplat-cli/issues/1268) descrive il fatto che le immagini acquisite dagli strumenti di tale interfaccia non acquisiscono i dischi dati collegati.)

    a. Arrestare la macchina tramite il portale.

    b. Fare clic su **Acquisisci** e specificare il nome dell'immagine come **mariadb-galera-image**. Fornire una descrizione e selezionare "Il comando waagent è stato eseguito."
      
      ![Acquisizione della macchina virtuale](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Creare il cluster
Creare tre macchine virtuali sulla base del modello creato e quindi configurare e avviare il cluster.

1. Creare la prima macchina virtuale di CentOS 7 dall'immagine mariadb-galera-image creata, fornendo le informazioni seguenti:

 - Nome rete virtuale: mariadbvnet
 - Subnet: mariadb
 - Dimensioni della macchina: medie
 - Nome del servizio cloud: mariadbha (o un nome a cui si desidera accedere tramite mariadbha.cloudapp.net)
 - Nome del computer: mariadb1
 - Nome utente: azureuser
 - Accesso SSH: abilitato
 - Trasferimento del file .pem con certificato SSH e sostituzione di /path/to/key.pem con il percorso in cui è stata archiviata la chiave SSH .pem generata.

   > [!NOTE]
   > I comandi riportati di seguito sono suddivisi su più righe per maggiore chiarezza, ma è necessario immetterli in un'unica riga.
   >
   >
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
2. Creare altre due macchine virtuali collegandole al servizio cloud mariadbha. Modificare il nome della macchina virtuale e la porta SSH in una porta univoca non in conflitto con altre macchine virtuali nello stesso servizio cloud.

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
  Per MariaDB3:

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
3. Per il passaggio successivo è necessario ottenere l'indirizzo IP interno di ognuna delle tre macchine virtuali:

    ![Recupero dell'indirizzo IP virtuale](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)
4. Usare SSH per accedere alle tre macchine virtuali e modificare il file di configurazione in ognuna di esse.

        sudo vi /etc/my.cnf.d/server.cnf

    Rimuovere simboli di commento in **`wsrep_cluster_name`** e **`wsrep_cluster_address`** rimuovendo **#** a inizio riga.
    Inoltre, sostituire **`<ServerIP>`** in **`wsrep_node_address`** e **`<NodeName>`** in **`wsrep_node_name`** rispettivamente con l'indirizzo IP e il nome della macchina virtuale, quindi rimuovere i simboli di commento dalle righe di codice.
5. Avviare il cluster in MariaDB1 e lasciare che venga eseguito all'avvio.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Avviare MySQL in MariaDB2 e MariaDB3 e lasciare che venga eseguito all'avvio.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Bilanciare il carico del cluster
Durante la creazione, le macchine virtuali in cluster sono state aggiunte a un set di disponibilità denominato clusteravset, per garantire che vengano inserite in domini di errore e di aggiornamento diversi e che Azure non esegua mai contemporaneamente la manutenzione su tutte le macchine. Questa configurazione soddisfa i requisiti di idoneità per il contratto di servizio di Azure.

Ora è possibile usare Azure Load balancer per bilanciare le richieste tra i tre nodi.

Eseguire i comandi seguenti nel computer tramite l'interfaccia della riga di comando di Azure.

La struttura dei parametri di comando è: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

L'interfaccia della riga di comando imposta l'intervallo dei probe del servizio di bilanciamento carico su 15 secondi, un tempo che potrebbe rivelarsi eccessivamente lungo. Modificare il valore nel portale in **Endpoint** per tutte le macchine virtuali.

![Modificare l'endpoint](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

Selezionare **Riconfigura il set con carico bilanciato**.

![Riconfigurare il set con carico bilanciato](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

Modificare **Intervallo probe** a 5 secondi e salvare le modifiche.

![Modificare l'intervallo di probe](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Convalidare il cluster
Il più è fatto. Il cluster dovrebbe essere accessibile all'indirizzo `mariadbha.cloudapp.net:3306` che raggiunge il servizio di bilanciamento del carico e instrada le richieste tra le tre macchine virtuali in modo semplice ed efficiente.

Usare il client MySQL di propria scelta per connettersi oppure connettersi tramite una delle macchine virtuali per verificare il funzionamento corretto di questo cluster.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Creare quindi un nuovo database e popolarlo con una serie di dati.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Il database creato restituisce la tabella seguente:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato creato un cluster a disponibilità elevata MariaDB + Galera a tre nodi in macchine virtuali di Azure con CentOS 7. Il bilanciamento del carico delle macchine virtuali viene eseguito mediante Azure Load Balancer.

Può essere utile consultare gli articoli sul [metodo alternativo per creare un cluster MySQL su Linux](virtual-machines-linux-classic-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) e sui metodi per [ottimizzare e testare le prestazioni di MySQL in macchine virtuali Linux di Azure](virtual-machines-linux-classic-optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[creare una chiave SSH per l'autenticazione]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268



<!--HONumber=Jan17_HO2-->


