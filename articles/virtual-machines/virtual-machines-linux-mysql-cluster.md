<properties
	pageTitle="Creare un cluster MySQL con set con carico bilanciato | Microsoft Azure"
	description="Installazione di un cluster MySQL con un bilanciamento del carico, disponibilità elevata Linux creato con il modello di distribuzione classica in Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="bureado"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2015"
	ms.author="jparrel"/>

# Uso di set con bilanciamento del carico per creare un cluster MySQL su Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione delle risorse con il modello di distribuzione classica.

Lo scopo di questo articolo è esplorare e illustrare i diversi approcci disponibili per distribuire servizi basati su Linux con disponibilità elevata su Microsoft Azure, esaminando principalmente l'elevata disponibilità del server MySQL. Su [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL) è disponibile un video che illustra questo approccio.

Viene descritta una soluzione con disponibilità elevata MySQL a master singolo, con due nodi e nessuna condivisione, basata su DRBD, Corosync e Pacemaker. MySQL viene eseguito solo su un nodo alla volta. Anche la lettura e la scrittura dalla risorsa DRBD sono limitate a un solo nodo alla volta.

Non è necessaria una soluzione IP virtuale come LVS, in quanto si usano i set con carico bilanciato di Microsoft Azure per garantire la funzionalità round robin e il rilevamento dell'endpoint, la rimozione e il ripristino gestito automaticamente dell'indirizzo IP virtuale. L'indirizzo IP virtuale è un indirizzo IPv4 instradabile a livello globale assegnato da Microsoft Azure durante la creazione del servizio cloud.

È possibile usare altre architetture per MySQL inclusi NBD Cluster, Percona e Galera, nonché diverse soluzioni middleware, di cui almeno una disponibile come macchina virtuale su [VM Depot](http://vmdepot.msopentech.com). Se le soluzioni possono eseguire la replica su unicast piuttosto che su multicast o trasmissione e non si basano su un archivio condiviso o su più interfacce di rete, gli scenari dovrebbero risultare facili da distribuire su Microsoft Azure.

Naturalmente queste architetture di clustering possono essere estese ad altri prodotti come PostgreSQL e OpenLDAP in modo simile. Questa procedura di bilanciamento del carico senza condivisione è stata ad esempio testata con OpenLDAP multimaster ed è possibile vederne i risultati sul blog di Channel 9.

## Preparazione

È necessario un account Microsoft Azure con una sottoscrizione valida in grado di creare almeno due (2) macchine virtuali (in questo esempio è stato usato XS), una rete e una subnet, un gruppo di affinità e un set di disponibilità, nonché la possibilità di creare nuovi VHD nella stessa area del servizio cloud e di collegarli alle macchine virtuali Linux.

### Ambiente testato

- Ubuntu 13.10
  - DRBD
  - Server MySQL
  - Corosync e Pacemaker

### Gruppo di affinità

Un gruppo di affinità per la soluzione viene creato accedendo al portale di Azure, passando a Impostazioni e creando un nuovo gruppo di affinità. Le risorse allocate create in seguito verranno assegnate a questo gruppo di affinità.

### Reti

Vengono create una nuova rete e una subnet all'interno della rete. È stata scelta una rete 10.10.10.0/24 contenente una sola subnet /24.

### Macchine virtuali

La prima macchina virtuale Ubuntu 13.10 viene creata con un'immagine della raccolta Ubuntu approvata e denominata `hadb01`. Nel processo viene creato un nuovo servizio cloud, denominato hadb. Questo nome consente di illustrare la natura condivisa e con bilanciamento del carico che il servizio avrà aggiungendo altre risorse. La creazione di `hadb01` è molto semplice e viene completata tramite il portale. Viene creato automaticamente un endpoint per SSH e viene selezionata la rete creata. Si è scelto anche di creare un nuovo set di disponibilità per le macchine virtuali.

Dopo aver creato la prima macchina virtuale (tecnicamente, quando viene creato il servizio cloud), sarà possibile creare la seconda macchina virtuale, `hadb02`. Anche per la seconda macchina virtuale si userà una macchina virtuale Ubuntu 13.10 della raccolta tramite il portale, ma si sceglierà di usare un servizio cloud esistente, `hadb.cloudapp.net`, anziché crearne uno nuovo. La rete e il set di disponibilità dovrebbero essere selezionati automaticamente. Verrà anche creato un endpoint SSH.

Dopo aver creato entrambe le macchine virtuali, si prenderà nota della porta SSH per `hadb01` (TCP 22) e `hadb02` (assegnata automaticamente da Azure)

### Archivio collegato

Si collegherà un nuovo disco a entrambe le macchine virtuali e si creeranno nuovi dischi da 5 GB durante il processo. I dischi saranno ospitati nel contenitore VHD in uso per i dischi del sistema operativo principale. Dopo la creazione e il collegamento dei dischi non è necessario riavviare Linux, in quanto il kernel rileva il nuovo dispositivo (in genere `/dev/sdc`, è possibile controllare l'output in `dmesg`).

In ogni macchina virtuale si procede creando una nuova partizione utilizzando `cfdisk` (partizione primaria, Linux) e scrivendo la nuova tabella di partizione. **Non creare un file system in questa partizione** .

## Configurazione del cluster

In entrambe le macchine virtuali Ubuntu è necessario usare APT per installare Corosync, Pacemaker e DRBD. Utilizzo di `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**Non installare MySQL a questo punto** . Gli script di installazione Debian e Ubuntu inizializzeranno una directory di dati MySQL in `/var/lib/mysql`, ma poiché la directory verrà sostituita da un file system DRBD, è necessario eseguire questa operazione in un secondo momento.

A questo punto, è necessario anche verificare (con `/sbin/ifconfig`) che entrambe le macchine virtuali usino indirizzi della subnet 10.10.10.0/24 e che siano in grado di eseguire il ping reciprocamente in base al nome. Se lo si desidera, è anche possibile usare `ssh-keygen` e `ssh-copy-id` per verificare che entrambe le macchine virtuali siano in grado di comunicare tramite SSH senza che sia necessaria una password.

### Configurazione di DRBD

Si creerà una risorsa DRBD che usa la partizione sottostante `/dev/sdc1` per produrre una risorsa `/dev/drbd1` che possa essere formattata con ext3 e usata in nodi primari e secondari. Per eseguire questa operazione, aprire `/etc/drbd.d/r0.res` e copiare la definizione di risorsa seguente. Eseguire l'operazione in entrambe le macchine virtuali:

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Al termine, inizializzare la risorsa tramite `drbdadm` in entrambe le macchine virtuali:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

Infine, sul nodo primario (`hadb01`) forzare la proprietà (primaria) della risorsa DRBD:

    sudo drbdadm primary --force r0

A questo punto, nel contenuto di /proc/drbd (`sudo cat /proc/drbd`) su entrambe le macchine virtuali, dovrebbe essere presente `Primary/Secondary` su `hadb01` e `Secondary/Primary` su `hadb02`, coerentemente con la soluzione. Il disco da 5 GB verrà sincronizzato sulla rete 10.10.10.0/24 senza costi per i clienti.

Quando il disco è sincronizzato, è possibile creare il file system su `hadb01`. Per le operazioni di test è stato usato ext2, ma l'istruzione seguente crea un file system ext3:

    mkfs.ext3 /dev/drbd1

### Montaggio della risorsa DRBD

In `hadb01` è ora possibile montare le risorse DRBD. Debian e i derivati usano `/var/lib/mysql` come directory dei dati di MySQL. Poiché MySQL non è installato, verrà creata la directory e montata la risorsa DRBD. In `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## Configurazione di MySQL

A questo punto si può installare MySQL su `hadb01`:

    sudo apt-get install mysql-server

Per `hadb02` sono disponibili due opzioni. È possibile installare mysql-server ora, creando /var/lib/mysql e inserendovi una nuova directory dati, quindi procedere alla rimozione dei contenuti. In `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La seconda opzione prevede il failover su `hadb02` e quindi l'installazione di mysql-server in questa posizione (gli script di installazione rileveranno l'installazione esistente e non la toccheranno)

In `hadb01`:

    sudo drbdadm secondary –force r0

In `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Se non si prevede di eseguire il failover di DRBD a questo punto, la prima opzione è più semplice, anche se meno elegante. Al termine della configurazione, è possibile iniziare a lavorare sul database MySQL. In `hadb02` o sul server attivo, in base a DRBD:

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.* TO root;

**Avviso**: quest'ultima istruzione disabilita in modo efficace l'autenticazione per l'utente ROOT in questa tabella. È consigliabile sostituirla con istruzioni GRANT di livello di produzione ed è inclusa solo per scopi esplicativi.

Occorre anche abilitare le connessioni di rete per MySQL se si vuole eseguire query dall'esterno delle macchine virtuali, ovvero lo scopo di questa guida. Su entrambe le macchine virtuali, aprire `/etc/mysql/my.cnf` e passare a `bind-address`, modificandolo da 127.0.0.1 a 0.0.0.0. Dopo aver salvato il file, inviare un comando `sudo service mysql restart` al nodo primario corrente.

### Creazione del set con bilanciamento del carico di MySQL

Ora si tornerà al portale di Azure e si passerà alla macchina virtuale `hadb01` e quindi agli endpoint. Verrà creato un nuovo endpoint, si sceglierà MySQL (TCP 3306) dall'elenco a discesa e si selezionerà la casella *.* L'endpoint con bilanciamento del carico verrà denominato `lb-mysql`. La maggior parte delle opzioni non verrà modificata, ad eccezione del tempo, che verrà ridotto a 5 secondi (impostazione minima)

Dopo la creazione dell'endpoint si passa a `hadb02`, Endpoint, e si crea un nuovo endpoint, ma si sceglie `lb-mysql` e si seleziona MySQL dal menu a discesa. È anche possibile usare CLI di Azure per questo passaggio.

A questo punto, si ha tutto l'occorrente per il funzionamento manuale del cluster.

### Test del set con bilanciamento del carico

I test possono essere eseguiti da un computer esterno, tramite qualsiasi client MySQL oppure con applicazioni come phpMyAdmin in esecuzione su un sito Web di Azure. In questo caso è stato usato lo strumento da riga di comando MySQL su un'altra casella di Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### Failover manuale

È ora possibile simulare il failover chiudendo MySQL, passando al nodo primario di DRBD e riavviando MySQL.

In hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Quindi, in hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Dopo aver eseguito il failover manuale, è possibile ripetere la query remota, che dovrebbe funzionare perfettamente.

## Configurazione di Corosync

Corosync è l'infrastruttura di cluster sottostante necessaria per il funzionamento di Pacemaker. Per gli utenti delle versioni 1 e 2 di Heartbeat (e altre metodologie come Ultramonkey), Corosync è una parte delle funzionalità CRM, mentre Pacemaker rimane più simile ad Hearbeat per funzionalità.

Il vincolo principale per Corosync su Azure sta nel fatto che Corosync preferisce il multicast alla trasmissione su comunicazioni unicast, mentre le reti Microsoft Azure supportano solo l'unicast.

Fortunatamente, Corosync include una modalità di lavoro unicast e l'unico vero vincolo è che, poiché tutti i nodi non comunicano tra loro *automaticamente*, è necessario definire i nodi nei file di configurazione, incluso l'indirizzo IP. È possibile usare i file di esempio di Corosync per Unicast, cambiare solo l'indirizzo di associazione, l'elenco dei nodi e la directory di registrazione (Ubuntu usa `/var/log/corosync` mentre i file di esempio usano `/var/log/cluster`) e abilitare gli strumenti del quorum.

**Si noti la direttiva `transport: udpu` sottostante e gli indirizzi IP definiti in modo manuale per i nodi**.

In `/etc/corosync/corosync.conf` per entrambi i nodi:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Copiare il file di configurazione in entrambe le macchine virtuali e avviare Corosync in entrambi i nodi:

    sudo service start corosync

Poco dopo aver avviato il servizio, dovrebbe venire stabilito il cluster nell'anello corrente e il quorum dovrebbe essere costituito. Per verificare questa funzionalità, controllare i log oppure:

    sudo corosync-quorumtool –l

Dovrebbe essere visualizzato un output simile all'immagine sottostante:

![output di esempio di corosync-quorumtool -l](media/virtual-machines-linux-mysql-cluster/image001.png)

## Configurazione di Pacemaker

Pacemaker usa il cluster per monitorare le risorse, definire quando i nodi primari diventano inattivi e passare le risorse ai nodi secondari. Le risorse possono essere definite da un set di script disponibili o da script LSB (simili all'inizializzazione), tra le altre possibilità.

Si vuole che Pacemaker "possegga" la risorsa DRBD, il punto di montaggio e il servizio MySQL. Se Pacemaker è in grado di attivare e disattivare DRBD, montarlo e smontarlo, avviare e interrompere MySQL nell'ordine corretto in caso di errori del nodo primario, la configurazione è completa.

Quando si installa Pacemaker per la prima volta, la configurazione dovrebbe essere molto semplice, come:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Verificare il programma eseguendo `sudo crm configure show`. Creare ora un file (ad esempio `/tmp/cluster.conf`) con le risorse seguenti:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

Caricarlo nella configurazione (è necessario eseguire questa operazione su un solo nodo):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Controllare anche che Pacemaker si avvii al momento giusto in entrambi i nodi:

    sudo update-rc.d pacemaker defaults

Dopo alcuni secondi, utilizzando `sudo crm_mon –L` verificare che uno dei nodi sia diventato il master del cluster, in cui sono eseguite tutte le risorse. È possibile usare mount e ps per verificare che le risorse siano in esecuzione.

La schermata seguente mostra `crm_mon` con un nodo arrestato (uscire con CTRL+C)

![Nodo crm\_mon arrestato](media/virtual-machines-linux-mysql-cluster/image002.png)

Questa schermata illustra invece entrambi i nodi, con un master e uno slave:

![Master/slave operativo crm\_mon](media/virtual-machines-linux-mysql-cluster/image003.png)

## Test

A questo punto, è possibile eseguire una simulazione di failover automatico. È possibile procedere in due modi: con un soft mount e con un hard mount. Il metodo soft mount prevede l'uso della funzione di arresto del cluster: ``crm_standby -U `uname -n` -v on`` Se si usa questa funzione sul master, lo slave prenderà il suo posto. Ricordare di riportarlo su Off (in caso contrario, crm\_mon segnalerà che un nodo è in standby)

Il metodo hard mount prevede l'arresto della macchina virtuale primaria (hadb01) tramite il portale o la modifica del livello di esecuzione sulla macchina virtuale (ad esempio con chiusura, arresto). Si aiutano quindi Corosync e Pacemaker segnalando che il master è inattivo. È possibile testare questa funzionalità (utile per le finestre di manutenzione) ma anche forzare lo scenario bloccando semplicemente la macchina virtuale.

## STONITH

Dovrebbe risultare possibile causare la chiusura di una macchina virtuale tramite l'interfaccia della riga di comando di Azure per Linux anziché eseguire uno script STONITH che controlla un dispositivo fisico. È possibile usare `/usr/lib/stonith/plugins/external/ssh` come base e abilitare STONITH nella configurazione del cluster. CLI di Azure deve essere installato ovunque e l'impostazione o il profilo di pubblicazione deve essere caricato per l'utente del cluster.

Codice di esempio per la risorsa disponibile in [GitHub](https://github.com/bureado/aztonith). È necessario modificare la configurazione del cluster aggiungendo il codice seguente a `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Nota**: lo script non esegue controlli up/down. La risorsa SSH originale aveva 15 verifiche ping ma il tempo di ripristino per una macchina virtuale di Azure può variare maggiormente.

## Limitazioni

Si applicano le limitazioni seguenti:

- Lo script della risorsa DRBD linbit che gestisce DRBD come risorsa in Pacemaker usa `drbdadm down` per chiudere un nodo, anche se il nodo sta solo passando in modalità standby. Questa situazione non è ideale perché lo slave non sincronizza la risorsa DRBD mentre il master riceve le scritture. Se l'arresto del master non è normale, lo slave può sostituire uno stato del file system meno recente. Per questo problema sono possibili due soluzioni:
  - Imporre `drbdadm up r0` in tutti i nodi del cluster tramite un watchdog locale (non appartenente al cluster) oppure
  - Modificare lo script DRBD linbit per fare in modo che `down` non venga chiamato in `/usr/lib/ocf/resource.d/linbit/drbd`.
- Il servizio di bilanciamento del carico richiede almeno 5 secondi per rispondere, quindi le applicazioni devono supportare i cluster ed essere più tolleranti del timeout. Anche altre architetture possono risultare utili, ad esempio le code in-app, middleware di query e così via
- È necessaria l'ottimizzazione di MySQL per assicurare che la scrittura venga effettuata con la velocità corretta e che le cache siano scaricate nel disco il più frequentemente possibile per ridurre al minimo le perdite di memoria
- Le prestazioni delle operazioni di scrittura dipenderanno dall'interconnessione delle macchine virtuali nel commutatore virtuale, in quanto questo è il meccanismo usato da DRBD per replicare il dispositivo

<!---HONumber=Sept15_HO4-->