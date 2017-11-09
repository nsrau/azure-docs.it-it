---
title: Creare un cluster MySQL con set con carico bilanciato | Microsoft Docs
description: "Configurare un cluster MySQL di Linux con bilanciamento del carico e disponibilità elevata creato con il modello di distribuzione classica in Azure"
services: virtual-machines-linux
documentationcenter: 
author: bureado
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: be178a59f7c3558cc0fef89e5529893f160c649b
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Usare set con bilanciamento del carico per creare un cluster MySQL su Linux
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager](../../../resource-manager-deployment-model.md) e la distribuzione classica. Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. È disponibile un [modello di Resource Manager](https://azure.microsoft.com/documentation/templates/mysql-replication/) per la distribuzione di un cluster MySQL.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Questo articolo esplora e illustra i diversi approcci disponibili per distribuire servizi basati su Linux con disponibilità elevata su Microsoft Azure, esaminando principalmente l'elevata disponibilità del server MySQL. Su [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)è disponibile un video che illustra questo approccio.

Sarà descritta una soluzione con disponibilità elevata MySQL a master singolo, con due nodi e nessuna condivisione, basata su DRBD, Corosync e Pacemaker. MySQL viene eseguito solo su un nodo alla volta. Anche la lettura e la scrittura dalla risorsa DRBD sono limitate a un solo nodo alla volta.

Non è necessaria una soluzione IP virtuale come LVS, in quanto si useranno i set con carico bilanciato in Microsoft Azure per garantire la funzionalità round robin e il rilevamento dell'endpoint, la rimozione e il ripristino gestito automaticamente dell'indirizzo VIP. L'indirizzo VIP è un indirizzo IPv4 instradabile a livello globale assegnato da Microsoft Azure durante la creazione del servizio cloud.

È possibile usare altre architetture per MySQL inclusi NBD Cluster, Percona, Galera e varie soluzioni middleware, di cui almeno una disponibile come VM su [VM Depot](http://vmdepot.msopentech.com). Se le soluzioni possono eseguire la replica su unicast piuttosto che su multicast o trasmissione e non si basano su un archivio condiviso o su più interfacce di rete, gli scenari dovrebbero risultare facili da distribuire su Microsoft Azure.

Queste architetture di clustering possono essere estese ad altri prodotti come PostgreSQL e OpenLDAP in modo simile. Questa procedura di bilanciamento del carico senza condivisione è stata ad esempio testata con OpenLDAP multimaster ed è possibile vederne i risultati sul blog di Channel 9.

## <a name="get-ready"></a>Preparazione
Sono necessarie le risorse e funzionalità seguenti:

  - Un account di Microsoft Azure con una sottoscrizione valida, in grado di creare almeno due VM (in questo esempio è stato usato XS)
  - Una rete e una subnet
  - Un gruppo di affinità
  - Un set di disponibilità
  - La possibilità di creare dischi rigidi virtuali nella stessa area del servizio cloud e collegarli alle VM Linux

### <a name="tested-environment"></a>Ambiente testato
* Ubuntu 13.10
  * DRBD
  * Server MySQL
  * Corosync e Pacemaker

### <a name="affinity-group"></a>Gruppo di affinità
Creare un gruppo di affinità per la soluzione accedendo al portale di Azure classico, selezionando **Impostazioni** e creando un gruppo di affinità. Le risorse allocate create in seguito verranno assegnate a questo gruppo di affinità.

### <a name="networks"></a>Reti
Vengono create una nuova rete e una subnet all'interno della rete. Questo esempio usa una rete 10.10.10.0/24 contenente una sola subnet /24.

### <a name="virtual-machines"></a>Macchine virtuali
La prima VM Ubuntu 13.10 viene creata con un'immagine della raccolta Ubuntu approvata ed è denominata `hadb01`. Nel processo viene creato un nuovo servizio cloud, denominato hadb. Questo nome indica la natura condivisa e con bilanciamento del carico che il servizio avrà al momento dell'aggiunta di altre risorse. La creazione di `hadb01` è molto semplice e la si esegue mediante il portale. Viene creato automaticamente un endpoint per SSH e viene selezionata la nuova rete. Ora è possibile creare un set di disponibilità per le VM.

Dopo aver creato la prima VM (tecnicamente quando viene creato il servizio cloud), creare la seconda VM, `hadb02`. Anche per la seconda VM si userà una VM Ubuntu 13.10 della raccolta tramite il portale, ma si userà un servizio cloud esistente, `hadb.cloudapp.net`, anziché crearne uno nuovo. La rete e il set di disponibilità dovrebbero essere selezionati automaticamente. Verrà anche creato un endpoint SSH.

Dopo aver creato entrambe le macchine virtuali, prendere nota della porta SSH per `hadb01` (TCP 22) e `hadb02` (assegnata automaticamente da Azure).

### <a name="attached-storage"></a>Archivio collegato
Collegare un nuovo disco a entrambe le VM e creare nuovi dischi da 5 GB durante il processo. I dischi sono ospitati nel contenitore del disco rigido virtuale in uso per i dischi del sistema operativo principale. Dopo la creazione e il collegamento dei dischi non è necessario riavviare Linux, in quanto il kernel rileva il nuovo dispositivo. Questo dispositivo è in genere `/dev/sdc`. Controllare l'output di `dmesg`.

In ogni VM creare una nuova partizione usando `cfdisk` (partizione primaria Linux) e scrivendo la nuova tabella di partizione. Non creare un file system in questa partizione.

## <a name="set-up-the-cluster"></a>Configurazione del cluster
Usare APT per installare Corosync, Pacemaker e DRBD su entrambe le VM Ubuntu. Per farlo con `apt-get`, eseguire il codice seguente:

    sudo apt-get install corosync pacemaker drbd8-utils.

Non installare MySQL a questo punto. Gli script di installazione Debian e Ubuntu inizializzeranno una directory di dati MySQL in `/var/lib/mysql`, ma poiché la directory verrà sostituita da un file system DRBD, è necessario installare MySQL.

Verificare (con `/sbin/ifconfig`) che entrambe le macchine virtuali usino indirizzi della subnet 10.10.10.0/24 e che siano in grado di eseguire il ping reciprocamente in base al nome. È anche possibile usare `ssh-keygen` e `ssh-copy-id` per verificare che entrambe le macchine virtuali siano in grado di comunicare tramite SSH senza che sia necessaria una password.

### <a name="set-up-drbd"></a>Configurare DRBD
Creare una risorsa DRBD che usa la partizione `/dev/sdc1` sottostante per produrre una risorsa `/dev/drbd1` che possa essere formattata con ext3 e usata in nodi primari e secondari.

1. Aprire `/etc/drbd.d/r0.res` e copiare la definizione di risorsa seguente in entrambe le VM:

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

2. Inizializzare la risorsa utilizzando `drbdadm` in entrambe le VM:

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. Sulla VM primaria (`hadb01`) forzare la proprietà (primaria) della risorsa DRBD:

        sudo drbdadm primary --force r0

A questo punto, nel contenuto di /proc/drbd (`sudo cat /proc/drbd`) su entrambe le macchine virtuali, dovrebbe essere presente `Primary/Secondary` su `hadb01` e `Secondary/Primary` su `hadb02`, coerentemente con la soluzione. Il disco da 5 GB viene sincronizzato sulla rete 10.10.10.0/24 senza costi per i clienti.

Quando il disco è sincronizzato, è possibile creare il file system su `hadb01`. Per le operazioni di test è stato usato ext2, ma il codice seguente crea un file system ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>Montare la risorsa DRBD
Ora è possibile montare le risorse DRBD in `hadb01`. Debian e i derivati usano `/var/lib/mysql` come directory dei dati di MySQL. Poiché MySQL non è installato, creare la directory e montare la risorsa DRBD. Per eseguire questa opzione, eseguire il codice seguente su `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>Configurare MySQL
A questo punto si può installare MySQL su `hadb01`:

    sudo apt-get install mysql-server

Per `hadb02`sono disponibili due opzioni. È possibile installare mysql-server creando in questo modo /var/lib/mysql, inserirvi una nuova directory dati e poi rimuovere i contenuti. Per eseguire questa opzione, eseguire il codice seguente su `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La seconda opzione è eseguire il failover su `hadb02` e quindi installare mysql-server là. Gli script di installazione rileveranno l'installazione esistente e non la toccheranno.

Eseguire il codice seguente in `hadb01`:

    sudo drbdadm secondary –force r0

Eseguire il codice seguente in `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Se non si prevede di eseguire il failover di DRBD a questo punto, la prima opzione è più semplice, anche se meno elegante. Al termine della configurazione, è possibile iniziare a lavorare sul database MySQL. Eseguire il codice seguente in `hadb02` (o su qualsiasi server attivo, in base a DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> Quest'ultima istruzione disabilita in modo efficace l'autenticazione per l'utente ROOT in questa tabella. È consigliabile sostituirla con istruzioni GRANT di livello di produzione ed è inclusa solo per scopi esplicativi.

Se si vuole eseguire query dall'esterno delle VM, che è lo scopo di questa guida, occorre anche abilitare le connessioni di rete per MySQL. In entrambe le VM aprire `/etc/mysql/my.cnf` e passare a `bind-address`. Cambiare l'indirizzo da 127.0.0.1 in 0.0.0.0. Dopo aver salvato il file, inviare un comando `sudo service mysql restart` al nodo primario corrente.

### <a name="create-the-mysql-load-balanced-set"></a>Creare il set con carico bilanciato di MySQL
Tornare al portale, passare a `hadb01` e scegliere **Endpoint**. Per creare un endpoint, scegliere MySQL (TCP 3306) dall'elenco a discesa e selezionare **Crea nuovo con carico bilanciato**. Assegnare un nome all'endpoint con carico bilanciato `lb-mysql`. Impostare **Tempo** su 5 secondi, come valore minimo.

Dopo aver creato l'endpoint, passare a `hadb02`, selezionare **Endpoint** e creare un endpoint. Scegliere `lb-mysql`, quindi selezionare MySQL dall'elenco a discesa. È anche possibile usare CLI di Azure per questo passaggio.

Ora si ha tutto l'occorrente per il funzionamento manuale del cluster.

### <a name="test-the-load-balanced-set"></a>Eseguire test sul set con carico bilanciato
I test possono essere eseguiti da un computer esterno usando qualsiasi client MySQL oppure con alcune applicazioni come phpMyAdmin in esecuzione come sito Web di Azure. In questo caso è stato usato lo strumento da riga di comando di MySQL su un'altra casella di Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Failover manuale
È possibile simulare il failover chiudendo MySQL, passando al nodo primario di DRBD e riavviando MySQL.

Per eseguire questa attività, eseguire il codice seguente in hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Quindi, in hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Dopo aver eseguito il failover manuale, è possibile ripetere la query remota, che dovrebbe funzionare perfettamente.

## <a name="set-up-corosync"></a>Configurare Corosync
Corosync è l'infrastruttura di cluster sottostante necessaria per il funzionamento di Pacemaker. Per Heartbeat (e altre metodologie come Ultramonkey), Corosync è una parte delle funzionalità CRM, mentre Pacemaker rimane più simile ad Heartbeat per funzionalità.

Il vincolo principale per Corosync su Azure sta nel fatto che Corosync preferisce il multicast alla trasmissione su comunicazioni unicast, mentre le reti Microsoft Azure supportano solo l'unicast.

Fortunatamente Corosync include una modalità di lavoro unicast. L'unico vero vincolo è che, poiché i nodi non comunicano tutti tra loro, è necessario definire i nodi nei file di configurazione, incluso l'indirizzo IP. È possibile usare i file di esempio di Corosync per Unicast, cambiare l'indirizzo di associazione, l'elenco dei nodi e le directory di registrazione (Ubuntu usa `/var/log/corosync` mentre i file di esempio usano `/var/log/cluster`) e abilitare gli strumenti del quorum.

> [!NOTE]
> Usare la seguente direttiva `transport: udpu` e gli indirizzi IP definiti manualmente per entrambi i nodi.

Eseguire il codice seguente in `/etc/corosync/corosync.conf` per entrambi i nodi:

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

Copiare il file di configurazione in entrambe le VM e avviare Corosync in entrambi i nodi:

    sudo service start corosync

Poco dopo aver avviato il servizio, dovrebbe venire stabilito il cluster nell'anello corrente e il quorum dovrebbe essere costituito. Per verificare questa funzionalità, controllare i log oppure eseguire il codice seguente:

    sudo corosync-quorumtool –l

L'output sarà simile all'immagine seguente:

![output di esempio di corosync-quorumtool -l](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Configurare Pacemaker
Pacemaker usa il cluster per monitorare le risorse, definire quando i nodi primari diventano inattivi e passare le risorse ai nodi secondari. Le risorse possono essere definite da un set di script disponibili o da script LSB (simili all'inizializzazione), tra le altre possibilità.

Si vuole che Pacemaker "possegga" la risorsa DRBD, il punto di montaggio e il servizio MySQL. Se Pacemaker è in grado di attivare e disattivare DRBD, montarlo e smontarlo, quindi avviare e interrompere MySQL nell'ordine corretto in caso di errori del nodo primario, e la configurazione è completa.

Quando si installa Pacemaker per la prima volta, la configurazione dovrebbe essere molto semplice, come:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Controllare la configurazione eseguendo `sudo crm configure show`.
2. Quindi creare un file (ad esempio `/tmp/cluster.conf`) con le risorse seguenti:

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

3. Caricare il file nella configurazione. È sufficiente eseguire questa operazione in un solo nodo.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Controllare che Pacemaker si avvii al momento giusto in entrambi i nodi:

        sudo update-rc.d pacemaker defaults

5. Mediante `sudo crm_mon –L` verificare che uno dei nodi sia diventato il master del cluster, in cui sono eseguite tutte le risorse. È possibile usare mount e ps per verificare che le risorse siano in esecuzione.

La schermata seguente mostra `crm_mon` con un nodo arrestato (uscire con CTRL+C):

![Nodo crm_mon arrestato](./media/mysql-cluster/image002.png)

Questa schermata mostra entrambi i nodi, uno master e uno slave:

![Master/slave operativo crm_mon](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Test
Ora è possibile eseguire una simulazione del failover automatico. È possibile procedere in due modi: con un soft mount e con un hard mount.

Il metodo soft mount prevede l'uso della funzione di arresto del cluster: ``crm_standby -U `uname -n` -v on``. Se la si usa sul master, subentra lo slave. Ricordare di disattivarla nuovamente. In caso contrario, crm_mon mostrerà un solo nodo in standby.

Il metodo "forte" è arrestare la VM primaria (hadb01) mediante il portalr o cambiare il runlevel sulla VM (ovvero halt, shutdown). Questo aiuta Corosync e Pacemaker segnalando che il master si sta arrestando. È possibile testare questa funzionalità (utile per le finestre di manutenzione) ma anche forzare lo scenario bloccando la VM.

## <a name="stonith"></a>STONITH
Dovrebbe risultare possibile causare la chiusura di una macchina virtuale tramite l'interfaccia della riga di comando di Azure per Linux anziché eseguire uno script STONITH che controlla un dispositivo fisico. È possibile usare `/usr/lib/stonith/plugins/external/ssh` come base e abilitare STONITH nella configurazione del cluster. L'interfaccia della riga di comando di Azure deve essere installata a livello globale e le impostazioni e il profilo di pubblicazione devono essere caricati per l'utente del cluster.

Esempi di codice per la risorsa sono disponibili in [GitHub](https://github.com/bureado/aztonith). Modificare la configurazione del cluster aggiungendo il codice seguente a `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> Lo script non esegue controlli up/down. La risorsa SSH originale aveva 15 verifiche ping ma il tempo di ripristino per una macchina virtuale di Azure può variare maggiormente.

## <a name="limitations"></a>Limitazioni
Si applicano le limitazioni seguenti:

* Lo script della risorsa DRBD linbit che gestisce DRBD come risorsa in Pacemaker usa `drbdadm down` per chiudere un nodo, anche se il nodo sta passando in modalità standby. Questa situazione non è ideale perché lo slave non sincronizza la risorsa DRBD mentre il master riceve le scritture. Se l'arresto del master non avviene con grazia, lo slave può sostituire uno stato del file system meno recente. Per questo problema sono possibili due soluzioni:
  * Applicare un `drbdadm up r0` in tutti i nodi del cluster tramite un watchdog locale (non appartenente al cluster) oppure
  * Modificare lo script DRBD linbit per fare in modo che `down` non venga chiamato in `/usr/lib/ocf/resource.d/linbit/drbd`
* Il bilanciamento del carico richiede almeno cinque secondi per rispondere, quindi le applicazioni devono essere compatibili con i cluster ed essere più tolleranti del timeout. Possono essere utili anche altre architetture, quali le code in-app e i middleware di query.
* L'ottimizzazione di MySQL è necessaria per assicurare che la scrittura venga effettuata con la velocità adeguata e che le cache siano scaricate nel disco il più frequentemente possibile per ridurre al minimo le perdite di memoria.
* Le prestazioni delle operazioni di scrittura dipendono dall'interconnessione delle VM nel commutatore virtuale, in quanto questo è il meccanismo utilizzato da DRBD per replicare il dispositivo.
