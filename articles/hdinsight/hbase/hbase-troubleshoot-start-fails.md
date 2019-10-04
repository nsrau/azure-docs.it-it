---
title: Non è possibile avviare Apache HBase Master in Azure HDInsight
description: Non è possibile avviare Apache HBase Master (HMaster) in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: d994fe1501dedf6a8ea2c3366f6559c7abac0892
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091619"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Non è possibile avviare Apache HBase Master (HMaster) in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="scenario-atomic-renaming-failure"></a>Scenario: Errore di ridenominazione atomica

### <a name="issue"></a>Problema

File imprevisti identificati durante il processo di avvio.

### <a name="cause"></a>Causa

Durante il processo di avvio, HMaster esegue molti passaggi di inizializzazione, incluso lo stato di trasferimento dei dati dalla cartella Scratch (. tmp) alla cartella Data. HMaster esamina anche la cartella log write-ahead (WAL) per verificare se sono presenti server di area che non rispondono.

HMaster esegue un comando list di base nelle cartelle WAL. Se in qualunque momento rileva un file imprevisto in una delle cartelle, HMaster genera un'eccezione e non viene avviato.

### <a name="resolution"></a>Risoluzione

Controllare lo stack di chiamate e provare a determinare quale cartella potrebbe avere causato il problema (ad esempio, la cartella WAL o la cartella. tmp). In Cloud Explorer o con comandi HDFS provare quindi a individuare il file problematico, In genere, si tratta `*-renamePending.json` di un file. Il `*-renamePending.json` file è un file journal usato per implementare l'operazione di ridenominazione atomica nel driver WASB. A causa di alcuni bug in questa implementazione, questi file possono essere ancora presenti in seguito ad arresti anomali dei processi e così via. Forzare l'eliminazione del file in Cloud Explorer o con comandi HDFS.

Talvolta potrebbe essere presente anche un file temporaneo denominato simile `$$$.$$$` a questa posizione. Per visualizzare questo file è necessario usare il comando HDFS `ls`. Non viene visualizzato in Cloud Explorer. Per eliminare il file, usare il comando HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.

Dopo l'esecuzione di questi comandi, HMaster dovrebbe essere avviato immediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Scenario: Nessun indirizzo server elencato

### <a name="issue"></a>Problema

Potrebbe essere visualizzato un messaggio che indica che la `hbase: meta` tabella non è online. L' `hbck` esecuzione di potrebbe `hbase: meta table replicaId 0 is not found on any region.` segnalare che nei log HMaster potrebbe essere visualizzato il messaggio: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Causa

Impossibile inizializzare HMaster dopo il riavvio di HBase.

### <a name="resolution"></a>Risoluzione

1. Nella shell di HBase digitare i comandi seguenti, modificando i valori effettivi a seconda dei casi:

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Eliminare la `hbase: namespace` voce. Questa voce potrebbe essere lo stesso errore che viene segnalato quando viene eseguita `hbase: namespace` l'analisi della tabella.

1. Riavviare Active HMaster dall'interfaccia utente di Ambari per ripristinare lo stato di esecuzione di HBase.

1. Nella shell di HBase eseguire questo comando per rendere disponibili tutte le tabelle offline:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenario: Java. io. IOException: TimedOut

### <a name="issue"></a>Problema

Si verifica il timeout di HMaster con un'eccezione `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`irreversibile simile a:.

### <a name="cause"></a>Causa

Questo problema potrebbe verificarsi se al riavvio dei servizi HMaster sono presenti diverse tabelle e aree che non sono state scaricate. Il timeout è un difetto noto con HMaster. Le attività di avvio generali del cluster possono richiedere molto tempo. HMaster viene arrestato se la tabella dello spazio dei nomi non è ancora assegnata. Le attività di avvio lunghe si verificano quando esiste una grande quantità di dati non scaricati e un timeout di cinque minuti non è sufficiente.

### <a name="resolution"></a>Risoluzione

1. Dall'interfaccia utente di Apache Ambari, passare a **HBase** > **configs**. Nel file personalizzato `hbase-site.xml` aggiungere l'impostazione seguente:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Riavviare i servizi necessari (HMaster e possibilmente altri servizi HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scenario: Riavvio del server di area frequente

### <a name="issue"></a>Problema

I nodi vengono riavviati periodicamente. Nei log del server di area è possibile che vengano visualizzate voci simili alle seguenti:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Pausa `regionserver` GC Long JVM. La sospensione `regionserver` provocherà la mancata risposta e non potrà inviare il battito del cuore a HMaster entro il timeout della sessione ZK. HMaster rileverà `regionserver` che è inattivo e `regionserver` interromperà e riavvierà.

### <a name="resolution"></a>Risoluzione

Modificare il timeout della sessione Zookeeper, non `hbase-site` solo `zookeeper.session.timeout` l'impostazione ma `zoo.cfg` anche `maxSessionTimeout` l'impostazione Zookeeper deve essere modificata.

1. Accedere all'interfaccia utente di Ambariri, passare a **HBase-> configs-> Settings**, nella sezione timeouts, modificare il valore del timeout della sessione Zookeeper.

1. Accedere all'interfaccia utente di Ambariri, passare a **Zookeeper-> configs-> Custom** `zoo.cfg`, aggiungere/modificare l'impostazione seguente. Verificare che il valore corrisponda a HBase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Riavviare i servizi necessari.

---

## <a name="scenario-log-splitting-failure"></a>Scenario: Errore di suddivisione del log

### <a name="issue"></a>Problema

HMasters non è riuscito a trovare un cluster HBase.

### <a name="cause"></a>Causa

Impostazioni HDFS e HBase non configurate correttamente per un account di archiviazione secondario.

### <a name="resolution"></a>Risoluzione

impostare HBase. RootDir: wasb://@.blob.core.windows.net/hbase e riavviare i servizi su Ambari.

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
