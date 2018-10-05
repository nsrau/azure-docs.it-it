---
title: Risoluzione dei problemi di scale-out SAP HANA 2.0 con impostazione HSR-Pacemaker su SLES 12 SPE3 in macchine virtuali Azure | Microsoft Docs
description: Guida per controllare e risolvere i problemi relativi all'elevata disponibilità per SAP HANA in una complessa configurazione di tipo scale-out basata su SAP HANA System Replication (HSR) e Pacemaker su SLES 12 SP3 in esecuzione su macchine virtuali Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 6c0d6397246e8b8db1d59c26229e37a722d49f48
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184658"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Verificare e risolvere i problemi di impostazione di tipo scale-out a disponibilità elevata per SAP HANA su SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Questo articolo è stato scritto per aiutare a verificare la configurazione del cluster Pacemaker per scale-out di SAP HANA in esecuzione su macchine virtuali Azure. È stata eseguita l'installazione del cluster in combinazione con SAP HANA System Replication (HSR) e il pacchetto RPM SUSE SAPHanaSR-scale-out. Tutti i test sono stati eseguiti esclusivamente su SUSE SLES 12 SP3. Sono presenti diverse sezioni che coprono diverse aree e includono comandi di esempio ed estratti dal file di configurazione. Questi esempi sono consigliati come metodo per verificare e controllare l'impostazione dell'intero cluster.



## <a name="important-notes"></a>Note importanti

Tutti i test per scale-out di SAP HANA in combinazione con SAP HANA System Replication e Pacemaker sono stati eseguiti solo con SAP HANA 2.0. La versione del sistema operativo era SUSE Linux Enterprise Server 12 SP3 per applicazioni SAP. È stato inoltre usato il pacchetto RPM più recente SAPHanaSR-scale-out di SUSE per configurare il cluster pacemaker.
SUSE ha pubblicato una descrizione dettagliata di questa impostazione ottimizzata per le prestazioni che può essere trovata [qui][sles-hana-scale-out-ha-paper]

Per i tipi di macchina virtuale supportati per lo scale-out di SAP HANA, consultare la [directory IaaS certificata di SAP HANA][sap-hana-iaas-list]

Si è verificato un problema tecnico con lo scale-out di SAP HANA in combinazione con più subnet e vNIC e la configurazione di HSR. È obbligatorio usare le patch più recenti di SAP HANA 2.0 in cui è stato risolto il problema. Sono supportate le versioni seguenti di SAP HANA: 

**rev2.00.024.04 o successive e rev2.00.032 o successive.**

Nel caso in cui si presentasse una situazione in cui è necessario il supporto di SUSE, seguire questa [guida][suse-pacemaker-support-log-files]. Raccogliere tutte le informazioni relative al cluster a disponibilità elevata di SAP HANA come illustrato nell'articolo. Il supporto SUSE necessita di queste informazioni per un'ulteriore analisi.

Durante i test interni, l'installazione del cluster è rimasta confusa da un arresto normale della macchina virtuale tramite il portale di Azure. È quindi consigliabile testare un failover del cluster tramite altri metodi. Usare metodi come forzare un kernel panic, arrestare le reti o eseguire la migrazione della risorsa **msl** (vedere dettagli nelle sezioni seguenti). Il presupposto è che un arresto standard si verifichi intenzionalmente. Il miglior esempio di un arresto intenzionale è quello per manutenzione (vedere i dettagli nella sezione relativa alla manutenzione pianificata).

Durante i test interni, l'installazione del cluster è rimasta confusa dopo un takeover manuale di SAP HANA mentre il cluster era in modalità di manutenzione. Si consiglia di reimpostare manualmente prima di terminare la modalità di manutenzione del cluster. Un'altra opzione è quella di attivare un failover prima di mettere il cluster in modalità di manutenzione (vedere i dettagli nella sezione relativa alla manutenzione pianificata). La documentazione di SUSE illustra come è possibile reimpostare il cluster a questo proposito usando il comando crm. Tuttavia, l'approccio indicato in precedenza è sembrato essere efficace durante i test interni e non ha mai mostrato effetti collaterali imprevisti.

Quando si usa il comando di migrazione crm, non dimenticare di pulire la configurazione del cluster. Aggiunge vincoli di posizione dei quali si potrebbe non essere consapevoli. Questi vincoli influiscono sul comportamento del cluster (consultare la sezione sulla manutenzione pianificata per ulteriori dettagli).



## <a name="test-system-description"></a>Descrizione del sistema di test

Per la verifica e la certificazione della disponibilità elevata dello scale-out di SAP HANA è stato usato un setup costituito da due sistemi con tre nodi di SAP HANA ciascuno, uno master e due nodi di lavoro. Ecco la lista dei nomi di macchina virtuale e degli indirizzi IP interni. Tutti i campioni di verifica più in avanti sono stati eseguiti su tali macchine virtuali. Usare questi nomi di macchina virtuale e indirizzi IP negli esempi di comando può aiutare a comprendere meglio i comandi e i loro output.


| Tipo di nodo | Nome della VM. | Indirizzo IP |
| --- | --- | --- |
| Nodo master nel sito 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Nodo di lavoro 1 nel sito 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Nodo di lavoro 2 nel sito 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Nodo master nel sito 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Nodo di lavoro 1 nel sito 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Nodo di lavoro 2 nel sito 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Nodo di maggioranza | hso-hana-dm | 10.0.0.13 |
| Server dispositivo SBD | hso-hana-sbd | 10.0.0.19 |
| | | |
| Server NFS 1 | hso-nfs-vm-0 | 10.0.0.15 |
| Server NFS 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Più subnet e vNIC

In seguito alle raccomandazioni di rete di SAP HANA, sono state create tre subnet all'interno di una rete virtuale di Azure. Lo scale-out di SAP HANA deve essere installato su Azure in modalità non condiviso, il che significa che ogni nodo usa volumi del disco locale per **/hana/data** e **hana/log**. Poiché si usano solo volumi del disco locale, non è necessario definire una subnet separata per la memorizzazione:

- 10.0.2.0/24   per comunicazione SAP HANA tra nodi
- 10.0.1.0/24   per SAP HANA System Replication HSR (replica di sistema SAP HANA)
- 10.0.0.0/24   per tutto il resto

Per informazioni sulla configurazione di SAP HANA relativa all'utilizzo di più reti, vedere la sezione **global.ini** più avanti.

In corrispondenza al numero di subnet, tutte le macchine virtuali del cluster dispongono di tre vNIC. [Questo][azure-linux-multiple-nics] articolo descrive un potenziale problema di routing in Azure quando si implementa una macchina virtuale Linux. Questo argomento di routing specifico vale solo per l'utilizzo di più vNIC. Il problema viene risolto da SUSE per impostazione predefinita in SLES 12 SP3. L'articolo di SUSE su questo argomento è reperibile [qui][suse-cloud-netconfig].


Come un controllo di base per verificare se SAP HANA sia configurato correttamente per l'uso di più reti, è sufficiente eseguire i comandi seguenti. Il primo passaggio consiste semplicemente nel controllare a livello di sistema operativo che tutti e tre gli indirizzi IP interni per tutte e tre le subnet siano attivi. Nel caso di subnet definite con intervalli di indirizzo IP diversi, è necessario adattare i comandi:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Di seguito è riportato un output di esempio dal secondo nodo del ruolo di lavoro nel sito 2. È possibile vedere tre indirizzi IP diversi da eth0, eth1 e eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Il secondo passaggio prevede la verifica delle porte di SAP HANA per il server dei nomi e HSR. SAP HANA deve essere in ascolto sulle subnet corrispondenti. È necessario adattare i comandi a seconda del numero di istanza di SAP HANA. Per il sistema di test, il numero di istanza era **00**. Esistono diversi modi per determinare quali porte vengono usate. 

Qui di seguito si trova una dichiarazione SQL, che tra le altre informazioni restituisce l'ID e il numero di istanza:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Per trovare i numeri di porta corretti è possibile guardare, per esempio, in HANA Studio sotto "**Configurazione**" o attraverso una dichiarazione SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Per trovare tutte le porte usate nello stack di software SAP incluso SAP HANA, cercare [qui][sap-list-port-numbers].

Dato il numero di istanza **00** nel sistema di test di SAP HANA 2.0, il numero di porta per il server dei nomi **30001**. Il numero di porta per la comunicazione dei metadati HSR è **40002**. Un'opzione è accedere a un nodo di lavoro, quindi controllare i servizi del nodo master. In questo caso, il controllo è stato eseguito sul nodo di lavoro 2 nel sito 2 provando a connettersi al nodo master nel sito 2.

Verificare la porta del server dei nomi:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Il risultato dovrebbe essere come nell'esempio di output di seguito per dimostrare che la comunicazione tra i nodi usi la subnet **10.0.2.0/24**.
Solo la connessione tramite subnet **10.0.2.0/24** dovrebbe avere esito positivo:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Eseguire ora una verifica per la porta HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Il risultato dovrebbe essere come nell'esempio di output di seguito per dimostrare che la comunicazione HRS usa la subnet **10.0.1.0/24**.
Solo la connessione tramite subnet **10.0.1.0/24** dovrebbe avere esito positivo:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


Il file di configurazione corosync deve essere corretto in ogni nodo del cluster, compreso il nodo di maggioranza. Nel caso in cui l'aggiunta nel cluster di un nodo non abbia funzionato come previsto, creare e/o copiare **/etc/corosync/corosync.conf** manualmente su/in tutti i nodi e riavviare il servizio.

Ecco il contenuto di **corosync.conf** dal sistema di test come esempio.

La prima sezione è **totem**, come descritto in questa [documentazione][sles-pacemaker-ha-guide] (sezione installazione cluster, passaggio 11). È possibile ignorare il valore per **mcastaddr**. È sufficiente mantenere la voce esistente. Le voci relative a **token** e **consenso** devono essere impostate in base alla documentazione di Microsoft Azure SAP HANA, che è possibile trovare [qui][sles-pacemaker-ha-guide]

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

La seconda sezione, **logging**, non è stata modificata rispetto alle impostazioni predefinite:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

La terza sezione mostra la **nodelist**. Tutti i nodi del cluster devono apparire con il loro id di nodo:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

Nell'ultima sezione, **quorum**, è importante impostare il valore per **expected_votes** correttamente. Deve essere il numero dei nodi incluso il nodo di maggioranza. Inoltre, il valore per **two_node** deve essere **0**. Non rimuovere completamente la voce. È sufficiente impostare il valore su **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Riavviare il servizio tramite **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Dispositivo SBD

La documentazione su come configurare un dispositivo SBD su una macchina virtuale di Azure è disponibile [qui][sles-pacemaker-ha-guide] (sezione sbd-fencing).

La prima cosa da verificare è se sulla macchina virtuale del server SBD sono presenti voci ACL per ogni nodo del cluster. Eseguire il seguente comando sulla macchina virtuale del server SBD:


<pre><code>
targetcli ls
</code></pre>


Nel sistema di test, l'output del comando era simile all'esempio seguente: I nomi come **iqn.2006-04.hso-db-0.local:hso-db-0** devono essere immessi come il nome dell'iniziatore corrispondente nelle macchine virtuali. Tutte le macchine virtuali devono averne uno diverso.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Successivamente, verificare che i nomi dell'iniziatore in tutte le macchine virtuali siano diversi e corrispondano alle voci illustrate in precedenza. Ecco un esempio dal nodo di lavoro 1 nel sito 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

L'output era simile all'esempio seguente:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Successivamente, verificare se l'**individuazione** funziona correttamente ed eseguire il comando seguente in ogni nodo del cluster usando l'indirizzo IP della macchina virtuale del server SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

L'output dovrebbe essere simile all'esempio seguente:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Il successivo punto di prova consiste nel verificare che il nodo rilevi il dispositivo SDB. Verificare su ogni nodo, compreso il nodo di maggioranza:

<pre><code>
lsscsi | grep dbhso
</code></pre>

L'output dovrebbe essere simile all'esempio seguente. Tenere presente che i nomi potrebbero essere diversi (anche il nome del dispositivo potrebbe cambiare dopo il riavvio della macchina virtuale):

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

A seconda dello stato del sistema, può talvolta essere utile riavviare i servizi iscsi per risolvere i problemi. Eseguire quindi i comandi seguenti:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Da qualsiasi nodo, è possibile verificare se tutti i nodi sono **clear**. Fare solo attenzione a usare il nome del dispositivo corretto in un nodo specifico:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

L'output dovrebbe mostrare **clear** per ogni nodo del cluster:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Un altro controllo SBD è l'opzione **dump** del comando sbd. Ecco un comando e un output di esempio dal nodo di maggioranza in cui il nome del dispositivo non era **sdm** ma **sdd**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

L'output (a parte il nome del dispositivo) dovrebbe essere lo stesso in tutti i nodi:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Un ulteriore controllo per SBD è la possibilità di inviare un messaggio a un altro nodo. Eseguire il comando seguente nel nodo di lavoro 1 nel sito 2 per inviare un messaggio al nodo di lavoro 2 nel sito 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Dalla parte della macchina virtuale di destinazione, che in questo esempio era **hso-hana-vm-s2-2**, è possibile trovare la voce seguente in **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Verificare se le voci in **/etc/sysconfig/sbd** corrispondono alla descrizione nella nostra [documentazione][sles-pacemaker-ha-guide] (sezione sbd-fencing). Verificare che l'impostazione di avvio in **/etc/iscsi/iscsid.conf** sia impostata su automatico.

Voci importanti in **/etc/sysconfig/sbd** (adatta il valore id, se necessario):

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Un altro elemento da controllare è l'impostazione di avvio in **/etc/iscsi/iscsid.conf**. L'impostazione obbligatoria deve essersi realizzata nel comando **iscsiadm** mostrato di seguito, descritto nella documentazione. È opportuno verificare e forse adattare manualmente con **vi** nel caso in cui sia diverso.

Comando per impostare il comportamento di avvio:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Voce in **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

In alcuni casi, durante i test e le verifiche, dopo il riavvio di una macchina virtuale il dispositivo SBD non era più visibile. In quei casi, si era verificata una discrepanza tra l'impostazione di avvio e quanto mostrato da yast2. Per verificare le impostazioni, eseguire i passaggi seguenti:

1. Avviare yast2
2. Selezionare **Servizi di rete** sul lato sinistro
3. Scorrere verso il basso sul lato destro fino a **Iniziatore iSCSI** e selezionarlo
4. Nella schermata successiva, sotto la scheda **Servizio**, si dovrebbe visualizzare il nome univoco dell'iniziatore per il nodo
5. Sopra il nome dell'iniziatore, assicurarsi che il valore **Avviare il servizio** sia impostato su **Durante l'avvio**
6. Se non è così, impostarlo su **Durante l'avvio** invece che su **Manualmente**
7. Passare quindi alla scheda in alto **Destinazioni connesse**
8. Nella schermata delle Destinazioni connesse si vedrà una voce per il dispositivo SBD come nell'esempio: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**
9. Controllare se il valore di avvio è impostato su "**onboot**"
10. In caso contrario, scegliere **Modifica** e modificarlo
11. Salvare le modifiche e uscire da yast2



## <a name="pacemaker"></a>Pacemaker

Dopo che tutto è configurato correttamente, è possibile eseguire il comando seguente in ogni nodo per controllare lo stato del servizio pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

La parte superiore dell'output dovrebbe essere simile all'esempio seguente. È importante che lo stato, dopo **Attivo**, sia visualizzato come **caricato** e **attivo (in esecuzione)**. Lo stato dopo "Caricato" deve essere visualizzato come **abilitato**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Nel caso in cui l'impostazione sia ancora su **disabilitato**, eseguire il comando seguente:

<pre><code>
systemctl enable pacemaker
</code></pre>

Per visualizzare tutte le risorse configurate nel pacemaker, eseguire il comando seguente:

<pre><code>
crm status
</code></pre>

L'output dovrebbe essere simile all'esempio seguente. È accettabile che le risorse cln e msl siano visualizzate come arrestate sulla macchina virtuale di maggioranza (**hso-hana-dm**). Non vi è alcuna installazione di SAP HANA sul nodo di maggioranza. Per questo, le risorse **cln** e **msl** sono visualizzate come arrestate. È importante che sia mostrato il corretto numero totale di macchine virtuali (**7**). Tutte le macchine virtuali che fanno parte del cluster devono essere elencate con stato **Online**. Il nodo master primario corrente deve essere riconosciuto correttamente (in questo esempio si tratta di **hso-hana-vm-s1-0**).

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Una caratteristica importante del pacemaker è la modalità di manutenzione. Questa modalità consente di apportare modifiche (ad esempio un riavvio della macchina virtuale) senza provocare un'azione immediata del cluster. Un caso d'uso tipico può essere la manutenzione pianificata dell'infrastruttura del sistema operativo o di Azure (vedere anche la sezione separata sulla manutenzione pianificata). Usare il comando seguente per inserire il pacemaker in modalità manutenzione:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Durante il controllo con **lo stato di crm**, è possibile osservare nell'output come tutte le risorse vengano contrassegnate come **non gestita**. In questo stato, il cluster non reagisce alle modifiche apportate, come ad esempio l'avvio/arresto di SAP HANA.
Di seguito è riportato l'output del comando **stato di crm** mentre il cluster è in modalità manutenzione:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Nell'esempio di comando riportato di seguito, invece, viene illustrato come terminare la modalità di manutenzione del cluster:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Un altro comando crm consente di trasformare la configurazione completa del cluster in un editor con la possibilità di modifica. Dopo aver salvato le modifiche, il cluster avvia le azioni appropriate:

<pre><code>
crm configure edit
</code></pre>

Se si desidera solo guardare la configurazione completa del cluster, usare l'opzione **mostra** di crm:

<pre><code>
crm configure show
</code></pre>



Dopo gli errori delle risorse del cluster, può succedere che il comando **stato di crm** visualizzi un elenco di **Azioni non riuscite**. Ecco un esempio con l'output seguente:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

È necessario eseguire una pulizia del cluster dopo gli errori. È sufficiente usare nuovamente il comando crm e usare l'opzione di comando **pulizia** per liberarsi di queste voci di azione sbagliate, denominando la risorsa del cluster corrispondente come illustrato di seguito:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Il comando dovrebbe restituire un output analogo a quello nell'esempio seguente:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover--takeover"></a>Failover / Takeover

Come già menzionato nella prima sezione con note importanti, non si deve usare un arresto normale standard per testare il failover del cluster o il takeover di SAP HANA HSR. È consigliabile invece attivare, ad esempio, un panic kernel oppure forzare una migrazione di risorse, oppure ancora arrestare tutte le reti a livello di sistema operativo di una macchina virtuale. Un altro metodo può essere il comando **crm \<node\> standby**. Vedere anche il documento SUSE, disponibile [qui][sles-12-ha-paper]. Di seguito è possibile trovare tre comandi di esempio per forzare un failover del cluster:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Come anche descritto nella sezione sulla manutenzione pianificata, un buon metodo per monitorare le attività del cluster consiste nell'eseguire **SAPHanaSR-showAttr** con il comando **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Inoltre, è utile per controllare lo stato del panorama SAP HANA proveniente da uno script di python SAP. Questo valore di stato è quello che l'impostazione del cluster sta cercando. È evidente quando si pensa a un errore del nodo di lavoro. Se un nodo di lavoro diventa inattivo, SAP HANA non restituisce immediatamente un errore per l'integrità dell'intero sistema di scale-out. Esistono alcuni tentativi per evitare failover non necessari. Solo se lo stato passa da Ok (valore restituito 4) a errore (valore restituito 1) il cluster reagisce. Pertanto, è corretto se l'output di **SAPHanaSR-showAttr** mostra una macchina virtuale con stato **offline** ma non esiste ancora nessuna attività da passare da primaria a secondaria. Nessuna attività del cluster viene attivata, purché SAP HANA non restituisca un errore.

È possibile monitorare lo stato di integrità del panorama SAP HANA come utente adm \<SID HANA\> chiamando lo script python SAP nel modo seguente (potrebbe essere necessario adattare il percorso):

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

L'output di questo comando dovrebbe essere simile al seguente: È importante anche la colonna **Stato dell'host**, nonché lo **stato complessivo dell'host**. L'output effettivo è infatti più ampio con colonne aggiuntive.
Per rendere più leggibile la tabella di output nel documento, la maggior parte delle colonne sul lato destro è stata rimossa:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Esiste un altro comando per controllare le attività correnti del cluster. Vedere il comando di seguito e la parte finale dell'output dopo che il nodo master del sito primario è stato terminato. È possibile visualizzare l'elenco delle azioni di transizione come **alzare di livello** il vecchio nodo master secondario (**hso-hana-vm-s2-0**) come il nuovo master primario. Se non ci sono problemi e tutte le attività sono terminate, l'elenco di **Riepilogo transizioni** deve essere vuoto.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Manutenzione pianificata 

Esistono diversi casi d'uso della manutenzione pianificata. Una domanda può essere, ad esempio, se si tratta semplicemente di manutenzione dell'infrastruttura, ad esempio modifiche a livello di sistema operativo e configurazione del disco o un aggiornamento HANA.
È possibile trovare ulteriori informazioni nei documenti di SUSE come [questo][sles-zero-downtime-paper] o [quest'altro][sles-12-for-sap]. Questi documenti includono anche esempi di come eseguire manualmente la migrazione di un oggetto primario.

Sono stati eseguiti intensi test interni per verificare il caso d'uso di manutenzione dell'infrastruttura. Per evitare qualsiasi tipo di problema correlato alla migrazione del primario, la decisione è stata di eseguire sempre la migrazione di un primario prima di inserire un cluster in modalità di manutenzione. In questo modo, non è necessario fare dimenticare al cluster come era la situazione precedente (quale parte era primaria e quale secondaria).

A tale proposito, si possono verificare due diverse situazioni:

1. Manutenzione pianificata sul secondario attuale. 
   In questo caso, è possibile solo inserire il cluster in modalità di manutenzione e svolgere il lavoro nel database secondario senza influire sul cluster

2. Manutenzione pianificata sul primario attuale. 
   Per consentire agli utenti di continuare a lavorare durante la manutenzione, è necessario forzare un failover. Con questo approccio è necessario attivare il failover del cluster con il pacemaker e non solo a livello di SAP HANA HSR. La configurazione del pacemaker attiva automaticamente il takeover di SAP HANA. Inoltre, è necessario eseguire il failover prima di inserire il cluster in modalità di manutenzione.

La procedura per la manutenzione del sito secondario corrente vuole la procedura seguente:

1. Impostare il cluster in modalità manutenzione
2. Eseguire il lavoro nel sito secondario 
3. Terminare la modalità manutenzione del cluster

La procedura per la manutenzione del sito primario corrente è più complessa:

1. Attivare manualmente un failover/takeover di SAP HANA tramite migrazione delle risorse di Pacemaker (vedere i dettagli sotto)
2. SAP HANA nel sito primario precedente viene arrestato dall'impostazione del cluster
3. Impostare il cluster in modalità manutenzione
4. Al termine del lavoro di manutenzione, registrare l'oggetto primario precedente come nuovo sito secondario
5. Configurazione della pulizia del cluster (vedere i dettagli sotto)
6. Terminare la modalità manutenzione del cluster


La migrazione di una risorsa, ad esempio forzare un failover, aggiunge una voce per la configurazione del cluster. È necessario eliminare queste voci prima di terminare la modalità di manutenzione. Ecco un esempio:

Il primo passaggio consiste nel forzare un failover di un cluster eseguendo la migrazione della risorsa msl al nodo master secondario attuale. Il comando seguente visualizza un avviso che è stato creato un "vincolo di spostamento".

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Controllare il processo di failover tramite il comando **SAPHanaSR-showAttr**. Ciò che consente di monitorare lo stato del cluster consiste nell'aprire una finestra della shell dedicata e avviare il comando con **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

L'output deve riflettere il failover manuale. Il primo nodo master secondario è stato **alzato di livello** (in questo esempio **hso-hana-vm-s2-0**) e il sito primario precedente è stato arrestato (**lss** valore **1** per il primo nodo master primario **hso-hana-vm-s1-0**): 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Dopo il failover del cluster e il takeover di SAP HANA, impostare il cluster in modalità di manutenzione, come descritto nella sezione del pacemaker.

I comandi **SAPHanaSR-showAttr** oppure **crm status** non indicano alcun dettaglio relativo ai vincoli creati dalla migrazione delle risorse. Un'opzione per rendere visibili tali vincoli consiste nel visualizzare la configurazione completa delle risorse del cluster con il comando seguente:

<pre><code>
crm configure show
</code></pre>

All'interno della configurazione del cluster, si trova un nuovo vincolo di posizione causato dalla precedente migrazione manuale della risorsa. Di seguito è riportato un esempio (voce che inizia con **location cli-**):

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Sfortunatamente, tali vincoli potrebbero influire sul comportamento del cluster complessivo. È pertanto obbligatorio rimuoverli nuovamente prima di rieseguire l'intero sistema. Con il comando **unmigrate** è possibile pulire i vincoli di posizione creati in precedenza. La denominazione potrebbe generare confusione. Questo non significa che tenterà di eseguire la migrazione della risorsa a ritroso, verso la macchina virtuale di origine da cui è stata eseguita la migrazione. Il comando si limiterà a rimuovere i vincoli di posizione e a restituire le informazioni corrispondenti quando viene eseguito:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Al termine delle operazioni di manutenzione, si arresta la modalità di manutenzione del cluster come illustrato nella sezione del pacemaker.



## <a name="hbreport-to-collect-log-files"></a>oggetto hb_report per raccogliere i file di log

Per analizzare i problemi del cluster pacemaker è utile, nonché richiesto dal supporto tecnico SUSE, eseguire l'utilità **hb_report**. Questa raccoglie tutti i file di log importanti, il che consente l'analisi delle operazioni eseguite. Di seguito si trova una chiamata di esempio con un'ora di inizio e una di fine in cui si è verificato un evento imprevisto specifico (vedere anche la prima la sezione sulle note importanti):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Il comando indica dove ha inserito i file di log compressi:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

È possibile quindi estrarre i file individuali tramite il comando standard **tar**:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Esaminando i file estratti, è possibile trovare tutti i file di log. La maggior parte è stata inserita in directory separate per ogni nodo del cluster:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


All'interno dell'intervallo di tempo specificato, il nodo master corrente **hso-hana-vm-s1-0** è stato terminato. Nel **journal.log** è possibile trovare le voci correlate a questo evento:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Un altro esempio è il file di log pacemaker nel master secondario che è diventato il nuovo master primario. Di seguito è riportato un estratto che mostra che lo stato del nodo master primario terminato è stato impostato su **offline**.

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>global.ini di SAP HANA


Di seguito sono visualizzati estratti dal file global.ini di SAP HANA nel sito di cluster 2 come esempio per visualizzare le voci di risoluzione nome host per l'uso di reti diverse per la comunicazione tra nodi di SAP HANA e HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>HAWK

La soluzione cluster fornisce anche un'interfaccia del browser, che offre un'interfaccia utente grafica interessante per gli utenti che preferiscono i menu e la grafica rispetto a tutti i comandi a livello di shell.
Per usare l'interfaccia del browser, prendere l'URL seguente e sostituire **\<nodo\>**  con un nodo reale di SAP HANA, quindi immettere le credenziali del cluster (utente **hacluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Lo screenshot seguente mostra la dashboard del cluster.


![Dashboard del cluster HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Nella seconda schermata è possibile vedere un esempio dei vincoli di posizione causato da una migrazione di risorse del cluster, come illustrato nella sezione manutenzione pianificata:


![Vincoli dell'elenco HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Un'altra funzionalità utile è la possibilità di caricare un output **hb_report** (vedere sezione **hb_report**) in **HAWK** sotto **Cronologia** come mostrato nello screenshot successivo:

![Caricamento di output hb_report in HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

L'**Explorer cronologia** consente quindi di scorrere tutte le transizioni del cluster incluse nell'output di **hb_report**:

![Visualizzazione in HAWK di transizioni nell'output hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Nell'ultimo screenshot si può vedere la sezione dei dettagli di una singola transizione, che mostra come il cluster abbia reagito a un arresto anomalo del nodo master primario (nodo **hso-hana-vm-s1-0**) alzando di livello il nodo secondario come nuovo master (**hso-hana-vm-s2-0**):

![Visualizzazione in HAWK di una singola transizione](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Passaggi successivi

Questa guida alla risoluzione dei problemi riguarda l'elevata disponibilità per SAP HANA in una configurazione di tipo scale-out. Un altro componente importante all'interno di un panorama applicativo SAP, oltre ai database, è lo stack SAP NetWeaver. Successivamente, è necessario leggere l'articolo sulla disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure usando SUSE Enterprise Linux Server. L'articolo è disponibile [qui][sap-nw-ha-guide-sles].

