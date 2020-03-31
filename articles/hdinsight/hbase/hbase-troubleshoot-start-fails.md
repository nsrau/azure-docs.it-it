---
title: Apache HBase Master fails to start in Azure HDInsight
description: Apache HBase Master (HMaster) fails to start in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887207"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) fails to start in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="scenario-atomic-renaming-failure"></a>Scenario: errore di ridenominazione atomicaScenario: Atomic renaming failure

### <a name="issue"></a>Problema

File imprevisti identificati durante il processo di avvio.

### <a name="cause"></a>Causa

Durante il processo di avvio, HMaster esegue molti passaggi di inizializzazione, tra cui lo spostamento dei dati dalla cartella scratch (tmp) alla cartella dei dati. HMaster esamina anche la cartella dei registri write-ahead (WAL) per verificare se sono presenti server di area che non rispondono.

HMaster esegue un comando di elenco di base sulle cartelle WAL. Se in qualunque momento rileva un file imprevisto in una delle cartelle, HMaster genera un'eccezione e non viene avviato.

### <a name="resolution"></a>Risoluzione

Controllare lo stack di chiamate e provare a determinare quale cartella potrebbe causare il problema (ad esempio, potrebbe essere la cartella WAL o la cartella tmp). In Cloud Explorer o con comandi HDFS provare quindi a individuare il file problematico, Di solito, `*-renamePending.json` questo è un file. (Il `*-renamePending.json` file è un file journal utilizzato per implementare l'operazione di ridenominazione atomica nel driver WASB. A causa di bug in questa implementazione, questi file possono essere lasciati dopo gli arresti anomali del processo e così via.) Forzare l'eliminazione di questo file in Cloud Explorer o utilizzando i comandi HDFS.

A volte, potrebbe anche esserci un `$$$.$$$` file temporaneo denominato qualcosa di simile in questa posizione. Per visualizzare questo file è necessario usare il comando HDFS `ls`. Non viene visualizzato in Cloud Explorer. Per eliminare il file, usare il comando HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.

Dopo l'esecuzione di questi comandi, HMaster dovrebbe essere avviato immediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Scenario: nessun indirizzo server elencato

### <a name="issue"></a>Problema

È possibile che venga visualizzato `hbase: meta` un messaggio che indica che la tabella non è online. L'esecuzione `hbck` `hbase: meta table replicaId 0 is not found on any region.` potrebbe segnalare che nei registri HMaster è possibile che venga visualizzato il messaggio: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Causa

Impossibile inizializzare HMaster dopo il riavvio di HBase.

### <a name="resolution"></a>Risoluzione

1. Nella shell di HBase digitare i comandi seguenti, modificando i valori effettivi a seconda dei casi:

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Eliminare `hbase: namespace` la voce. Questa voce potrebbe essere lo stesso errore `hbase: namespace` che viene segnalato quando la tabella viene analizzata.

1. Riavviare Active HMaster dall'interfaccia utente di Ambari per ripristinare lo stato di esecuzione di HBase.

1. Nella shell di HBase eseguire questo comando per rendere disponibili tutte le tabelle offline:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenario: java.io.IOException: Timedout

### <a name="issue"></a>Problema

HMaster timeout con eccezione `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`irreversibile simile a: .

### <a name="cause"></a>Causa

Questo problema potrebbe verificarsi se al riavvio dei servizi HMaster sono presenti diverse tabelle e aree che non sono state scaricate. Il timeout è un difetto noto con HMaster. Le attività di avvio generali del cluster possono richiedere molto tempo. HMaster viene arrestato se la tabella dello spazio dei nomi non è ancora assegnata. Le lunghe attività di avvio si verificano dove esiste una grande quantità di dati non scaricati e un timeout di cinque minuti non è sufficiente.

### <a name="resolution"></a>Risoluzione

1. Dall'interfaccia utente di Apache Ambari, vai a **HBase** > **Configs**. Nel file `hbase-site.xml` personalizzato aggiungere l'impostazione seguente:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Riavviare i servizi necessari (HMaster e possibilmente altri servizi HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scenario: riavvii frequenti del server di area

### <a name="issue"></a>Problema

I nodi si riavviano periodicamente. Dai registri del server di regione è possibile visualizzare voci simili alle:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Lunga `regionserver` pausa JVM GC. La pausa `regionserver` causerà di non rispondere e non in grado di inviare battito cardiaco a HMaster entro il timeout di sessione zk 40s. HMaster crederà che `regionserver` sia `regionserver` morto e interromperà il e riavvierà.

### <a name="resolution"></a>Risoluzione

Modificare il timeout della sessione `hbase-site` `zookeeper.session.timeout` di lo `zoo.cfg` zoo,, non solo l'impostazione, ma anche l'impostazione `maxSessionTimeout` di lo zookeeper devono essere modificati.

1. Accedere all'interfaccia utente di Ambari, passare a **HBase -> Configs -> Settings**, nella sezione Timeout , modificare il valore di Timeout sessione di .

1. Accedere all'interfaccia utente di Ambari, passare a **-> Configs -> personalizzata** `zoo.cfg`, aggiungere/modificare l'impostazione seguente. Assicurarsi che il valore sia `zookeeper.session.timeout`lo stesso di HBase .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Riavviare i servizi necessari.

---

## <a name="scenario-log-splitting-failure"></a>Scenario: errore di suddivisione del registroScenario: Log splitting failure

### <a name="issue"></a>Problema

HMasters non è riuscito a venire su un cluster HBase.

### <a name="cause"></a>Causa

Impostazioni HDFS e HBase non configurate correttamente per un account di archiviazione secondario.

### <a name="resolution"></a>Risoluzione

impostare hbase.rootdir: wasb://@.blob.core.windows.net/hbase e riavviare i servizi su Ambari.

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
