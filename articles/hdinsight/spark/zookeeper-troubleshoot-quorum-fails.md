---
title: Il server Apache ZooKeeper non è in grado di formare un quorum in Azure HDInsight
description: Il server Apache ZooKeeper non è in grado di formare un quorum in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 05/20/2020
ms.openlocfilehash: 9038630a2623a8b20ddfcf98899ce9a89f16bdc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84673361"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Il server Apache ZooKeeper non è in grado di formare un quorum in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi a Zookeeper nei cluster Azure HDInsight.

## <a name="symptoms"></a>Sintomi

* Entrambi gli strumenti di gestione delle risorse passano in modalità standby
* I NameNode sono entrambi in modalità standby
* I processi Spark, Hive e Yarn o le query Hive non riescono a causa di errori di connessione Zookeeper
* L'avvio dei daemon LLAP non riesce nei cluster Spark sicuri o nei cluster Interactive Hive sicuri

## <a name="sample-log"></a>Log di esempio

È possibile che venga visualizzato un messaggio di errore simile al seguente nei log yarn (/var/log/Hadoop-Yarn/Yarn/Yarn-Yarn *. log in nodi head):

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Problemi correlati

* I servizi a disponibilità elevata come Yarn, NameNode e Livy possono diventare inattivi per diversi motivi.
* Verificare nei log che il problema sia correlato alle connessioni Zookeeper
* Verificare che il problema si verifichi ripetutamente (non usare queste soluzioni per casi isolati)
* Possono verificarsi errori temporanei nei processi a causa di problemi di connessione di Zookeeper

## <a name="common-causes-for-zookeeper-failure"></a>Cause comuni dei problemi di connessione di Zookeeper

* Utilizzo elevato della CPU nei server Zookeeper
  * Se nell'interfaccia utente di Ambari viene visualizzato un utilizzo della CPU che sfiora il 100% nei server Zookeeper, le sessioni Zookeeper aperte durante tale periodo possono scadere e andare in timeout
* I client Zookeeper segnalano frequenti timeout
  * Nei log di Resource Manager, NameNode e altri servizi vengono visualizzati frequenti timeout delle connessioni client
  * Questi timeout possono comportare perdita del quorum, frequenti failover e altri problemi

## <a name="check-for-zookeeper-status"></a>Controllare lo stato di Zookeeper

* Trovare i server Zookeeper usando il file /etc/hosts o l'interfaccia utente di Ambari
* Eseguire il comando seguente
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (o 2182)  
  * La porta 2181 è l'istanza di Apache Zookeeper
  * La porta 2182 viene usata dall'istanza di Zookeeper in HDInsight (per fornire disponibilità elevata ai servizi che non la supportano in modalità nativa)
  * Se il comando non visualizza alcun output, significa che i server Zookeeper non sono in esecuzione
  * Se i server sono in esecuzione, il risultato includerà le statistiche delle connessioni client e altre statistiche

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>Si verifica un picco della CPU ogni ora

* Accedere al server Zookeeper e controllare il file /etc/crontab
* Se in questo momento sono in esecuzione processi orari, distribuire in modo casuale l'ora di inizio tra diversi server Zookeeper.
  
## <a name="purging-old-snapshots"></a>Eliminazione degli snapshot meno recenti

* I server Zookeeper sono configurati per l'eliminazione automatica degli snapshot meno recenti
* Per impostazione predefinita, vengono conservati gli ultimi 30 snapshot
* Il numero di snapshot conservati è controllato dalla chiave di configurazione `autopurge.snapRetainCount`. Questa proprietà è disponibile nei file seguenti:
  * `/etc/zookeeper/conf/zoo.cfg` per Hadoop Zookeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` per HDInsight Zookeeper
* Impostare `autopurge.snapRetainCount` su 3 e riavviare i server Zookeeper
  * La configurazione di Hadoop Zookeeper può essere aggiornata e il servizio può essere riavviato tramite Ambari
  * Arrestare e riavviare HDInsight Zookeeper manualmente
    * `sudo lsof -i :2182` fornisce l'ID del processo da terminare
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Non eliminare gli snapshot manualmente, in quanto potrebbe verificarsi una perdita di dati

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CancelledKeyException nel server Zookeeper non richiede la pulizia degli snapshot

* Questa eccezione verrà visualizzata nei file Zookeeper (/var/log/Zookeeper/Zookeeper-Zookeeper-* o/var/log/HDInsight-Zookeeper/Zookeeper *)
* Questa eccezione in genere indica che il client non è più attivo e il server non è in grado di inviare un messaggio
* Questa eccezione indica anche che il client Zookeeper chiude le sessioni prematuramente
* Cercare gli altri sintomi illustrati in questo documento

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).
- Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.
- Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
