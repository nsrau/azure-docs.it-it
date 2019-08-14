---
title: Non è possibile avviare Apache HBase Master in Azure HDInsight
description: Non è possibile avviare Apache HBase Master (HMaster) in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935406"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Non è possibile avviare Apache HBase Master (HMaster) in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="scenario-atomic-renaming-failure"></a>Scenario: Errore di ridenominazione atomica

### <a name="issue"></a>Problema

File imprevisti identificati durante il processo di avvio.

### <a name="cause"></a>Causa

Durante il processo di avvio, HMaster esegue molti passaggi di inizializzazione, incluso lo stato di trasferimento dei dati dalla cartella Scratch (. tmp) alla cartella Data. HMaster esamina anche la cartella WALs (write ahead log) per verificare se sono presenti server di aree non recapitabili. Durante tutte queste situazioni, esegue un comando di `list` base in queste cartelle. Se in qualsiasi momento viene visualizzato un file imprevisto in una di queste cartelle, viene generata un'eccezione e quindi non viene avviata.

### <a name="resolution"></a>Risoluzione

In una situazione di questo tipo, controllare lo stack di chiamate per vedere quale cartella potrebbe causare un problema (ad esempio, cartella WALs o cartella. tmp). Per individuare il file di problema, quindi, Cloud Explorer o tramite i comandi HDFS. Il file del problema è in `*-renamePending.json` genere un file, ovvero un file journal usato per implementare l'operazione di ridenominazione atomica nel driver WASB. A causa di bug in questa implementazione, questi file possono essere lasciati in caso di arresto anomalo del processo. Forzare l'eliminazione del file tramite Cloud Explorer. Potrebbe inoltre essere presente un file temporaneo della natura $ in questa posizione. Il file non può essere visualizzato tramite Cloud Explorer e solo tramite `ls` il comando HDFS. Per eliminare il file, `hdfs dfs -rm //\$\$\$.\$\$\$` è possibile usare il comando HDFS.

Una volta rimosso il file di problema, HMaster dovrebbe avviarsi immediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Scenario: Nessun indirizzo server elencato

### <a name="issue"></a>Problema

HMaster log mostra un messaggio di errore simile a "nessun indirizzo server elencato in HBase: meta per Region xxx".

### <a name="cause"></a>Causa

Impossibile inizializzare HMaster dopo il riavvio di HBase.

### <a name="resolution"></a>Risoluzione

1. Eseguire i comandi seguenti nella shell di HBase (modificare i valori effettivi come applicabile):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. Eliminare la voce di hbase: namespace, in quanto lo stesso errore potrebbe essere segnalato durante l'analisi della tabella hbase: namespace.

1. Riavviare Active HMaster dall'interfaccia utente di Ambari per ripristinare lo stato di esecuzione di HBase.

1. Eseguire il comando seguente nella shell di HBase per visualizzare tutte le tabelle offline:

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenario: Java. io. IOException: TimedOut

### <a name="issue"></a>Problema

Si verifica il timeout di HMaster con `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`un'eccezione irreversibile come.

### <a name="cause"></a>Causa

Il timeout è un difetto noto con HMaster. Le attività di avvio generali del cluster possono richiedere molto tempo. HMaster viene arrestato se la tabella dello spazio dei nomi non è ancora assegnata. Le attività di avvio lunghe si verificano quando esiste una grande quantità di dati non scaricati e un timeout di cinque minuti non è sufficiente.

### <a name="resolution"></a>Risoluzione

1. Accedere all'interfaccia utente di Ambariri, passare a HBase-> configs, in Custom `hbase-site.xml` aggiungere la seguente impostazione:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Riavviare i servizi necessari, in particolare HMaster e possibilmente altri servizi HBase.

---

## <a name="scenario-frequent-regionserver-restarts"></a>Scenario: Riavvii regionserver frequenti

### <a name="issue"></a>Problema

I nodi vengono riavviati periodicamente. Dai registri di regionserver è possibile che vengano visualizzate voci simili alle seguenti:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Pausa GC regionserver Long. Con la pausa, regionserver non risponde e non è in grado di inviare il battito cardiaco a HMaster entro il timeout della sessione ZK. HMaster rileverà che regionserver è inattivo e interromperà il regionserver e verrà riavviato.

### <a name="resolution"></a>Risoluzione

Modificare il timeout della sessione Zookeeper, non solo l'impostazione `zookeeper.session.timeout` HBase sito, ma è necessario modificare anche l'impostazione `maxSessionTimeout` Zookeeper Zoo. cfg.

1. Accedere all'interfaccia utente di Ambariri, passare a **HBase-> configs-> Settings**, nella sezione timeouts, modificare il valore del timeout della sessione Zookeeper.

1. Accedere all'interfaccia utente di Ambariri, passare a **Zookeeper-> configs-> Custom** Zoo. cfg, aggiungere/modificare l'impostazione seguente. Verificare che il valore corrisponda a HBase `zookeeper.session.timeout`.

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
