---
title: Risoluzione dei problemi di scale-out SAP HANA 2.0 con impostazione HSR-Pacemaker su SLES 12 SPE3 in macchine virtuali Azure | Microsoft Docs
description: Informazioni per verificare e risolvere i problemi relativi a una complessa configurazione di tipo scale-out a disponibilità elevata per SAP HANA, basata su SAP HANA System Replication (HSR) e Pacemaker, su SLES 12 SP3 in esecuzione su macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: b794b045efa4be20a63e9996425d69f0212ae0d7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707247"
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


Questo articolo presenta informazioni utili per verificare la configurazione del cluster Pacemaker per lo scale-out di SAP HANA in esecuzione su macchine virtuali di Azure. È stata eseguita l'installazione del cluster in combinazione con SAP HANA System Replication (HSR) e il pacchetto RPM SUSE SAPHanaSR-scale-out. Tutti i test sono stati eseguiti esclusivamente su SUSE SLES 12 SP3. Le sezioni dell'articolo trattano diverse aree e includono comandi di esempio ed estratti dai file di configurazione. È consigliabile usare questi esempi come metodo per verificare la configurazione dell'intero cluster.



## <a name="important-notes"></a>Note importanti

Tutti i test per scale-out di SAP HANA in combinazione con SAP HANA System Replication e Pacemaker sono stati eseguiti solo con SAP HANA 2.0. Come versione del sistema operativo è stata usata SUSE Linux Enterprise Server 12 SP3 per applicazioni SAP. Per configurare il cluster Pacemaker è stato usato il pacchetto RPM più recente, SAPHanaSR-ScaleOut di SUSE.
SUSE pubblicato una [descrizione di questa configurazione di ottimizzazione delle prestazioni dettagliata][sles-hana-scale-out-ha-paper].

Per i tipi di macchine virtuali che sono supportati per SAP HANA scalabilità orizzontale, vedere la [SAP HANA certified IaaS directory][sap-hana-iaas-list].

Si è verificato un problema tecnico con lo scale-out di SAP HANA in combinazione con più subnet e vNIC e la configurazione di HSR. È obbligatorio usare le patch più recenti di SAP HANA 2.0 in cui è stato risolto il problema. Sono supportate le versioni seguenti di SAP HANA: 

* rev2.00.024.04 o successiva 
* rev2.00.032 o successiva

Se ti serve supporto da SUSE, seguire questo [Guida][suse-pacemaker-support-log-files]. Raccogliere tutte le informazioni relative al cluster a disponibilità elevata di SAP HANA come illustrato nell'articolo. Il supporto SUSE necessita di queste informazioni per un'ulteriore analisi.

Durante i test interni, la configurazione del cluster è rimasta confusa in seguito a un arresto normale standard di una macchina virtuale tramite il portale di Azure. È quindi consigliabile testare un failover del cluster con altri metodi, ad esempio forzando un kernel panic, arrestando le reti o eseguendo la migrazione della risorsa **msl**. Vedere i dettagli nelle sezioni seguenti. Il presupposto è che un arresto standard si verifichi intenzionalmente. Il miglior esempio di arresto intenzionale è l'arresto per manutenzione. Vedere i dettagli in [Manutenzione pianificata](#planned-maintenance).

Inoltre, durante i test interni, la configurazione del cluster è rimasta confusa dopo un takeover manuale di SAP HANA mentre il cluster era in modalità di manutenzione. È consigliabile ripristinarla manualmente prima di terminare la modalità di manutenzione del cluster. Un'altra opzione è quella di attivare un failover prima di impostare il cluster in modalità di manutenzione. Per altre informazioni, vedere [Manutenzione pianificata](#planned-maintenance). La documentazione di SUSE illustra come reimpostare il cluster in questo modo usando il comando **crm**. L'approccio indicato in precedenza è tuttavia risultato efficace durante i test interni e non ha mai mostrato effetti collaterali imprevisti.

Quando si usa il comando **crm migrate**, assicurarsi di pulire la configurazione del cluster. Aggiunge vincoli di posizione di cui si potrebbe non essere consapevoli e che hanno un impatto sul comportamento del cluster. Per altri dettagli, vedere [Manutenzione pianificata](#planned-maintenance).



## <a name="test-system-description"></a>Descrizione del sistema di test

 Per la verifica e la certificazione della disponibilità elevata del sistema di tipo scale-out di SAP HANA è stata usata una configurazione costituita da due sistemi, ciascuno con tre nodi di SAP HANA, uno master e due di lavoro. La tabella seguente elenca i nomi delle macchine virtuali e gli indirizzi IP interni. Tutti gli esempi di verifica riportati più in avanti sono stati eseguiti su queste macchine virtuali. Usando questi nomi di macchina virtuale e questi indirizzi IP negli esempi di comandi è possibile comprendere più facilmente i comandi e il relativo output:


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

In seguito alle raccomandazioni di rete di SAP HANA, sono state create tre subnet all'interno di una rete virtuale di Azure. Il sistema di tipo scale-out di SAP HANA in Azure deve essere installato in modalità non condivisa. Ciò significa che ogni nodo usa volumi dei dischi locali per **/hana/data** e **hana/log**. Poiché i nodi usano solo volumi dei dischi locali, non è necessario definire una subnet separata per l'archiviazione:

- 10.0.2.0/24 per le comunicazioni tra nodi SAP HANA
- 10.0.1.0/24 per SAP HANA System Replication (HSR)
- 10.0.0.0/24 per tutto il resto

Per informazioni sulla configurazione di SAP HANA correlata all'uso di più reti, vedere [global.ini di SAP HANA](#sap-hana-globalini).

Ogni macchina virtuale del cluster dispone di tre vNIC che corrispondono al numero di subnet. [Come creare una macchina virtuale Linux in Azure con la rete di più schede di interfaccia][azure-linux-multiple-nics] describes a potential routing issue on Azure when deploying a Linux VM. This specific routing article applies only for use of multiple vNICs. The problem is solved by SUSE per default in SLES 12 SP3. For more information, see [Multi-NIC with cloud-netconfig in EC2 and Azure][suse-cloud-netconfig].


Per verificare che SAP HANA sia configurato correttamente per l'uso di più reti, eseguire i comandi seguenti. Per prima cosa, controllare a livello di sistema operativo che tutti e tre gli indirizzi IP interni per tutte e tre le subnet siano attivi. Se sono state definite subnet con intervalli di indirizzi IP diversi, è necessario adattare i comandi:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

L'output di esempio seguente riguarda il secondo nodo di lavoro nel sito 2. È possibile vedere tre indirizzi IP diversi da eth0, eth1 e eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Verificare quindi le porte di SAP HANA per il server dei nomi e HSR. SAP HANA deve essere in ascolto sulle subnet corrispondenti. È necessario adattare i comandi a seconda del numero di istanza di SAP HANA. Per il sistema di test, il numero di istanza era **00**. Esistono diversi modi per determinare quali porte vengono usate. 

L'istruzione SQL seguente restituisce l'ID e il numero di istanza e altre informazioni:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Per trovare i numeri di porta corretti è possibile usare l'area di **configurazione** di HANA Studio oppure un'istruzione SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Per ogni porta usata nello stack di software SAP, tra cui SAP HANA, eseguire ricerche [porte TCP/IP di tutti i prodotti SAP][sap-list-port-numbers].

Dato il numero di istanza **00** nel sistema di test di SAP HANA 2.0, il numero di porta per il server dei nomi è **30001**. Il numero di porta per le comunicazioni dei metadati HSR è **40002**. Un'opzione è accedere a un nodo di lavoro, quindi controllare i servizi del nodo master. Per questo articolo, si è controllato il nodo di lavoro 2 nel sito 2 provando a connettersi al nodo master nel sito 2.

Controllare la porta del server dei nomi:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Per dimostrare che le comunicazioni tra i nodi avvengono attraverso la subnet **10.0.2.0/24**, il risultato dovrebbe essere simile all'output di esempio seguente.
Solo la connessione tramite la subnet **10.0.2.0/24** dovrebbe avere esito positivo:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Controllare ora la porta HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Per dimostrare che le comunicazioni HSR avvengono attraverso la subnet **10.0.1.0/24**, il risultato dovrebbe essere simile all'output di esempio seguente.
Solo la connessione tramite la subnet **10.0.1.0/24** dovrebbe avere esito positivo:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


Il file di configurazione **corosync** deve essere corretto in ogni nodo del cluster, compreso il nodo di maggioranza. Se l'aggiunta di un nodo nel cluster non funziona come previsto, creare o copiare **/etc/corosync/corosync.conf** manualmente in tutti i nodi e riavviare il servizio. 

Un esempio è offerto dal contenuto di **corosync.conf** del sistema di test.

La prima sezione è **totem**, come descritto nel passaggio 11 della procedura di [installazione del cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation). È possibile ignorare il valore per **mcastaddr**. È sufficiente mantenere la voce esistente. Le voci relative **token** e **consenso** deve essere impostata in base ai [documentazione di Microsoft Azure SAP HANA][sles-pacemaker-ha-guide].

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

La terza sezione mostra la **nodelist**. Tutti i nodi del cluster devono apparire con il relativo **nodeid**:

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

Nell'ultima sezione, **quorum**, è importante impostare correttamente il valore per **expected_votes**. Deve essere il numero dei nodi incluso il nodo di maggioranza. Inoltre, il valore per **two_node** deve essere **0**. Non rimuovere completamente la voce. È sufficiente impostare il valore su **0**.

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

Le istruzioni per configurare un dispositivo SBD su una macchina virtuale di Azure sono riportate nella sezione [Isolamento tramite SBD](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Per prima cosa, verificare se sulla macchina virtuale del server SBD sono presenti voci ACL per ogni nodo del cluster. Eseguire il seguente comando sulla macchina virtuale del server SBD:


<pre><code>
targetcli ls
</code></pre>


Nel sistema di test l'output del comando è simile all'esempio seguente. I nomi di ACL, come **iqn.2006-04.hso-db-0.local:hso-db-0**, devono essere immessi come i nomi di iniziatore corrispondenti nelle macchine virtuali. Tutte le macchine virtuali devono averne uno diverso.

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

Verificare quindi che i nomi di iniziatore in tutte le macchine virtuali siano diversi e corrispondano alle voci illustrate in precedenza. Ecco un esempio relativo al nodo di lavoro 1 nel sito 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

L'output ha un aspetto simile all'esempio seguente:

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

Verificare quindi che il comando **discovery** funzioni correttamente. Eseguire il comando seguente in ogni nodo del cluster usando l'indirizzo IP della macchina virtuale del server SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

L'output dovrebbe avere un aspetto simile all'esempio seguente:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Il punto di prova successivo consiste nel verificare che il nodo rilevi il dispositivo SDB. Verificare su ogni nodo, compreso il nodo di maggioranza:

<pre><code>
lsscsi | grep dbhso
</code></pre>

L'output dovrebbe avere un aspetto simile all'esempio seguente. I nomi, tuttavia, possono essere diversi. Il nome del dispositivo può anche cambiare dopo il riavvio della macchina virtuale:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

A seconda dello stato del sistema, può talvolta essere utile riavviare i servizi iSCSI per risolvere i problemi. Eseguire quindi i comandi seguenti:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Da qualsiasi nodo, è possibile verificare se tutti i nodi sono **clear**. Assicurarsi di usare il nome di dispositivo corretto in un nodo specifico:

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


Un altro controllo relativo a SBD è costituito dall'opzione **dump** del comando **sbd**. In questo esempio di comando e output relativo al nodo di maggioranza, il nome del dispositivo non era **sdm** ma **sdd**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

L'output, fatta eccezione per il nome del dispositivo, dovrebbe essere lo stesso in tutti i nodi:

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

Un ulteriore controllo per SBD è la possibilità di inviare un messaggio a un altro nodo. Per inviare un messaggio al nodo di lavoro 2 nel sito 2, eseguire il comando seguente nel nodo di lavoro 1 nel sito 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Sul lato della macchina virtuale di destinazione, che in questo esempio è **hso-hana-vm-s2-2**, è possibile trovare la voce seguente in **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Verificare che le voci in **/etc/sysconfig/sbd** corrispondano alla descrizione in [Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Verificare che l'impostazione di avvio in **/etc/iscsi/iscsid.conf** sia impostata su automatico.

Le voci seguenti sono importanti in **/etc/sysconfig/sbd**. Adattare il valore di **id**, se necessario:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Controllare l'impostazione di avvio in **/etc/iscsi/iscsid.conf**. L'impostazione richiesta dovrebbe essere stata definita con il comando **iscsiadm** seguente, descritto nella documentazione. Verificarla e adattarla manualmente con **vi** se è diversa.

Questo comando imposta il comportamento di avvio:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Immettere questa voce in **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Durante i test e le verifiche, dopo il riavvio di una macchina virtuale, in alcuni casi il dispositivo SBD non era più visibile. Questo avveniva a causa di una discrepanza tra l'impostazione di avvio e quanto mostrato in YaST2. Per controllare le impostazioni, seguire questa procedura:

1. Avviare YaST2.
2. Selezionare **Servizi di rete** nel riquadro sinistro.
3. Nel riquadro destro scorrere verso il basso fino a visualizzare la voce **Iniziatore iSCSI** e selezionarla.
4. Nella schermata successiva, nella scheda **Servizio**, dovrebbe essere visualizzato il nome univoco dell'iniziatore per il nodo.
5. Sopra il nome dell'iniziatore verificare che **Avvio servizio** sia impostato su **Durante l'avvio**.
6. In caso contrario, impostare **Durante l'avvio** anziché **Manualmente**.
7. Passare quindi alla scheda **Destinazioni connesse**.
8. Nella schermata **Destinazioni connesse** dovrebbe essere visualizzata una voce per il dispositivo SBD, come in questo esempio: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Verificare se **Avvio** è impostato su **on boot**.
10. In caso contrario, scegliere **Modifica** e modificare l'impostazione.
11. Salvare le modifiche e uscire da YaST2.



## <a name="pacemaker"></a>Pacemaker

Dopo aver completato la configurazione, è possibile eseguire il comando seguente in ogni nodo per controllare lo stato del servizio Pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

La parte superiore dell'output dovrebbe avere un aspetto simile all'esempio seguente. È importante che lo stato, dopo **Attivo**, sia visualizzato come **caricato** e **attivo (in esecuzione)** . Lo stato dopo **Loaded** deve essere visualizzato come **enabled**.

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

Se l'impostazione è ancora **disabled**, eseguire il comando seguente:

<pre><code>
systemctl enable pacemaker
</code></pre>

Per visualizzare tutte le risorse configurate in Pacemaker, eseguire il comando seguente:

<pre><code>
crm status
</code></pre>

L'output dovrebbe avere un aspetto simile all'esempio seguente. È accettabile che le risorse **cln** e **msl** risultino arrestate sulla macchina virtuale di maggioranza, **hso-hana-dm**. Non è presente alcuna installazione di SAP HANA sul nodo di maggioranza e pertanto le risorse **cln** e **msl** risultano arrestate. È importante che sia visualizzato il numero totale di macchine virtuali corretto, ovvero **7**. Tutte le macchine virtuali che fanno parte del cluster devono essere elencate con stato **Online**. Il nodo master primario corrente deve essere riconosciuto correttamente. In questo esempio è **hso-hana-vm-s1-0**:

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

Una funzionalità importante di Pacemaker è data dalla modalità di manutenzione. In questa modalità è possibile apportare modifiche senza provocare un'azione immediata del cluster. Un esempio è offerto dal riavvio di una macchina virtuale. Un tipico caso d'uso può essere la manutenzione pianificata del sistema operativo o dell'infrastruttura di Azure. Vedere [Manutenzione pianificata](#planned-maintenance). Usare il comando seguente per impostare Pacemaker in modalità di manutenzione:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Quando si esegue una verifica con il comando **crm status**, nell'output è possibile osservare che tutte le risorse sono contrassegnate come **unmanaged**. In questo stato, il cluster non reagisce a eventuali modifiche, come l'avvio o l'arresto di SAP HANA.
L'esempio seguente mostra l'output del comando **crm status** mentre il cluster è in modalità di manutenzione:

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


Questo esempio di comando mostra come terminare la modalità di manutenzione del cluster:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Un altro comando **crm** ottiene la configurazione completa del cluster e la visualizza in un editor, da cui è possibile modificarla. Dopo il salvataggio delle modifiche, il cluster avvia le azioni appropriate:

<pre><code>
crm configure edit
</code></pre>

Per esaminare la configurazione completa del cluster, usare l'opzione **crm show**:

<pre><code>
crm configure show
</code></pre>



Dopo che si sono verificati errori delle risorse del cluster, il comando **crm status** mostra un elenco di azioni non riuscite, **Failed Actions**. Vedere l'output di esempio seguente:


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

Dopo gli errori è necessario eseguire la pulizia del cluster. Usare nuovamente il comando **crm** e l'opzione di comando **cleanup** per rimuovere queste voci relative ad azioni non riuscite. Assegnare un nome alla risorsa del cluster corrispondente, come illustrato di seguito:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Il comando restituirà un output simile all'esempio seguente:

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



## <a name="failover-or-takeover"></a>Failover o takeover

Come indicato nella sezione [Note importanti](#important-notes), è opportuno evitare di usare un arresto normale standard per testare il failover del cluster o il takeover di SAP HANA HSR. In alternativa, è consigliabile attivare un kernel panic, forzare una migrazione di risorse o eventualmente arrestare tutte le reti al livello del sistema operativo di una macchina virtuale. Un'altra soluzione è offerta dal comando **crm \<nodo\> standby**. Vedere le [documento SUSE][sles-12-ha-paper]. 

I tre comandi di esempio seguenti possono forzare un failover del cluster:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Come descritto in [Manutenzione pianificata](#planned-maintenance), un buon metodo per monitorare le attività del cluster consiste nell'eseguire **SAPHanaSR-showAttr** con il comando **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

È inoltre utile controllare lo stato del panorama SAP HANA restituito da uno script Python di SAP. Questo è il valore di stato cercato dalla configurazione del cluster. Risulta evidente se si pensa a un errore di un nodo di lavoro. Se un nodo di lavoro diventa inattivo, SAP HANA non restituisce immediatamente un errore relativo all'integrità dell'intero sistema di scale-out. 

Esistono alcuni tentativi per evitare failover non necessari. Il cluster reagisce solo se lo stato passa da **Ok**, con valore restituito **4**, a **error**, con valore restituito **1**. È quindi corretto se l'output restituito da **SAPHanaSR-showAttr** mostra una macchina virtuale con stato **offline**. Non vi sono ancora tuttavia attività che determinano il passaggio da primaria a secondaria. Nessuna attività del cluster viene attivata, purché SAP HANA non restituisca un errore.

È possibile monitorare lo stato di integrità del panorama SAP HANA come utente **\<HANA SID\>adm** chiamando lo script Python di SAP, come illustrato di seguito. Può essere necessario adattare il percorso:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

L'output di questo comando dovrebbe essere simile all'esempio seguente. La colonna **Host Status** e lo **stato complessivo dell'host** sono entrambi importanti. L'output effettivamente restituito è più ampio e include altre colonne.
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


Esiste un altro comando per controllare le attività correnti del cluster. Vedere il comando seguente e la parte finale dell'output dopo che il nodo master del sito primario è stato terminato. È possibile visualizzare l'elenco delle azioni di transizione come l'**innalzamento di livello** del precedente nodo master secondario, **hso-hana-vm-s2-0**, come nuovo master primario. Se non vi sono problemi e tutte le attività sono terminate, l'elenco **Transition Summary** deve essere vuoto.

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

Esistono diversi casi d'uso della manutenzione pianificata. Può ad esempio trattarsi semplicemente di attività di manutenzione dell'infrastruttura, come le modifiche a livello di sistema operativo e configurazione del disco o un aggiornamento di HANA.
È possibile trovare informazioni aggiuntive nei documenti da SUSE, ad esempio [verso tempi di inattività nulli][sles-zero-downtime-paper] or [SAP HANA SR Performance Optimized Scenario][sles-12-for-sap]. Questi documenti includono anche esempi di come eseguire manualmente la migrazione di un sito primario.

Sono stati eseguiti intensi test interni per verificare il caso d'uso di manutenzione dell'infrastruttura. Per evitare problemi correlati alla migrazione del sito primario, è stato deciso di eseguire sempre la migrazione di un sito primario prima di impostare un cluster in modalità di manutenzione. In questo modo, non è necessario far dimenticare al cluster come era la situazione precedente, ovvero la distinzione tra il sito primario e quello secondario.

A tale proposito, si possono verificare due diverse situazioni:

- **Manutenzione pianificata sul sito secondario corrente**. In questo caso, è sufficiente impostare il cluster in modalità di manutenzione e svolgere le attività sul sito secondario senza alcun effetto sul cluster.

- **Manutenzione pianificata sul sito primario corrente**. In questo caso, per consentire agli utenti di continuare a lavorare durante la manutenzione, è necessario forzare un failover. Con questo approccio è necessario attivare il failover del cluster tramite Pacemaker e non solo a livello di SAP HANA HSR. La configurazione di Pacemaker attiva automaticamente il takeover di SAP HANA. È inoltre necessario attivare il failover prima di impostare il cluster in modalità di manutenzione.

La procedura per la manutenzione sul sito secondario corrente è la seguente:

1. Impostare il cluster in modalità di manutenzione.
2. Svolgere le attività sul sito secondario. 
3. Terminare la modalità di manutenzione del cluster.

La procedura per la manutenzione del sito primario corrente è più complessa:

1. Attivare manualmente un failover o un takeover di SAP HANA tramite la migrazione di una risorsa di Pacemaker. Vedere i dettagli che seguono.
2. SAP HANA nel sito primario precedente viene arrestato dalla configurazione del cluster.
3. Impostare il cluster in modalità di manutenzione.
4. Al termine delle attività di manutenzione, registrare il sito primario precedente come nuovo sito secondario.
5. Pulire la configurazione del cluster. Vedere i dettagli che seguono.
6. Terminare la modalità di manutenzione del cluster.


La migrazione di una risorsa aggiunge una voce alla configurazione del cluster. Un esempio è dato da un failover forzato. È necessario pulire queste voci prima di terminare la modalità di manutenzione. Vedere l'esempio seguente.

Per prima cosa, forzare un failover del cluster eseguendo la migrazione della risorsa **msl** al nodo master secondario corrente. Questo comando visualizza un avviso per segnalare che è stato creato un **vincolo di spostamento**:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Controllare il processo di failover tramite il comando **SAPHanaSR-showAttr**. Per monitorare lo stato del cluster, aprire una finestra della shell dedicata e avviare il comando con **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

L'output dovrebbe mostrare il failover manuale. Il nodo master secondario precedente è stato **alzato di livello**, in questo esempio, **hso-hana-vm-s2-0**. Il sito primario precedente è stato arrestato, **lss** con valore **1** per il nodo master primario precedente **hso-hana-vm-s1-0**: 

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

Dopo il failover del cluster e il takeover di SAP HANA, impostare il cluster in modalità di manutenzione, come descritto in [Pacemaker](#pacemaker).

I comandi **SAPHanaSR-showAttr** e **crm status** non indicano alcun dettaglio sui vincoli creati dalla migrazione delle risorse. Un'opzione per rendere visibili tali vincoli consiste nel visualizzare la configurazione completa delle risorse del cluster con il comando seguente:

<pre><code>
crm configure show
</code></pre>

All'interno della configurazione del cluster, si trova un nuovo vincolo di posizione causato dalla precedente migrazione manuale della risorsa. Questa voce di esempio inizia con **location cli-** :

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Sfortunatamente, questi vincoli possono influire sul comportamento complessivo del cluster. È pertanto obbligatorio rimuoverli nuovamente prima di ripristinare il backup dell'intero sistema. Con il comando **unmigrate** è possibile pulire i vincoli di posizione creati in precedenza. La denominazione potrebbe generare confusione. Il comando non tenta di eseguire la migrazione della risorsa a ritroso, verso la macchina virtuale di origine. Rimuove semplicemente i vincoli di posizione e restituisce le informazioni corrispondenti quando viene eseguito:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Al termine delle operazioni di manutenzione, si arresta la modalità di manutenzione del cluster come illustrato in [Pacemaker](#pacemaker).



## <a name="hbreport-to-collect-log-files"></a>oggetto hb_report per raccogliere i file di log

Per analizzare i problemi del cluster Pacemaker è utile, nonché richiesto dal supporto tecnico di SUSE, eseguire l'utilità **hb_report**. Questa utilità raccoglie tutti i file di log importanti che sono necessari per analizzare che cosa è successo. La chiamata di esempio seguente usa l'ora di inizio e di fine di un intervallo di tempo in cui si è verificato un determinato evento imprevisto. Vedere anche [Note importanti](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Il comando indica il percorso di archiviazione dei file di log compressi:

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


Nell'intervallo di tempo specificato, il nodo master corrente **hso-hana-vm-s1-0** è stato terminato. È possibile trovare le voci correlate a questo evento nel file **journal.log**:

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

Un altro esempio è dato dal file di log di Pacemaker nel nodo master secondario che è diventato il nuovo master primario. Questo estratto mostra che lo stato del nodo master primario terminato è stato impostato su **offline**:

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


Di seguito sono riportati alcuni estratti del file **global.ini** di SAP HANA nel sito 2 del cluster. Questo esempio mostra le voci di risoluzione dei nomi host per l'uso di reti diverse per le comunicazioni tra nodi di SAP HANA e HSR:

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



## <a name="hawk"></a>Hawk

La soluzione cluster offre un'interfaccia utente grafica su browser per gli utenti che preferiscono i menu e la grafica rispetto ai comandi a livello di shell.
Per usare l'interfaccia del browser, nell'URL seguente sostituire **\<node\>** con un nodo di SAP HANA effettivo. Immettere quindi le credenziali del cluster (utente **cluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Questo screenshot mostra il dashboard del cluster:


![Dashboard del cluster Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Questo esempio mostra i vincoli di posizione generati da una migrazione di risorse del cluster, come illustrato in [Manutenzione pianificata](#planned-maintenance):


![Elenco di vincoli in Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


È anche possibile caricare l'output di **hb_report** in Hawk nell'area **History** (Cronologia), come illustrato di seguito. Oggetto hb_report per raccogliere i file di log, vedere: 

![Caricamento dell'output di hb_report in Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Con **History Explorer** (Esplora cronologia) è quindi possibile scorrere tutte le transizioni del cluster incluse nell'output di **hb_report**:

![Transizioni di Hawk nell'output di hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Questo screenshot finale mostra la sezione **Details** (Dettagli) relativa a una singola transizione. Il cluster ha reagito in seguito a un arresto anomalo del nodo master primario, **hso-hana-vm-s1-0**. Sta ora alzando di livello il nodo secondario come nuovo master, **hso-hana-vm-s2-0**:

![Singola transizione in Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Passaggi successivi

Questa guida alla risoluzione dei problemi ha illustrato la disponibilità elevata per SAP HANA in una configurazione di tipo scale-out. Oltre ai database, un componente importante in un panorama applicativo SAP è costituito dallo stack SAP NetWeaver. Scopri [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure che usano SUSE Enterprise Linux Server][sap-nw-ha-guide-sles].

