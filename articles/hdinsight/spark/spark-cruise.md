---
title: Usare SparkCruise in Azure HDInsight per velocizzare le query Apache Spark
description: Informazioni su come usare la piattaforma di ottimizzazione SparkCruise per migliorare l'efficienza delle query Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 1a73b4707f83d6a23dffc20d95aa7b8a0fa465b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88649058"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>SparkCruise in Azure HDInsight

Questo documento illustra la funzionalità *SparkCruise*di Azure HDInsight, che riutilizza automaticamente Apache Spark calcoli per aumentare l'efficienza delle query.

## <a name="overview"></a>Panoramica

Le query eseguite su una piattaforma di analisi, ad esempio Apache Spark, sono decomposte in un piano di query che contiene sottoquery più piccole. Queste sottoquery possono essere visualizzate ripetutamente nei piani di query per più query. Ogni volta che si verificano, vengono eseguite nuovamente per restituire i risultati. La nuova esecuzione della stessa query, tuttavia, può risultare inefficiente e causare costi di calcolo superflui.

*SparkCruise* è una piattaforma di ottimizzazione del carico di lavoro in grado di riutilizzare calcoli comuni, riducendo il tempo di esecuzione complessivo delle query e i costi di trasferimento dei dati. La piattaforma usa il concetto di *vista materializzata*, ovvero una query i cui risultati vengono archiviati in formato pre-calcolato. Questi risultati possono essere riutilizzati quando la query stessa viene nuovamente visualizzata in un secondo momento, anziché ricalcolare i risultati.

## <a name="setup-and-installation"></a>Installazione e configurazione

SparkCruise è disponibile in tutti i cluster HDInsight 4,0 con Spark 2,3 o 2,4. I file di libreria SparkCruise sono installati nella `/opt/peregrine/` directory del cluster HDInsight. Per funzionare correttamente, *SparkCruise* richiede le proprietà di configurazione seguenti, che sono impostate per impostazione predefinita.

* `spark.sql.queryExecutionListeners` è impostato su `com.microsoft.peregrine.spark.listeners.PlanLogListener` , che consente la registrazione dei piani di query.
* `spark.sql.extensions` è impostato su `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` , che Abilita le regole di ottimizzazione per la materializzazione e il riutilizzo online.

## <a name="computation-reuse-in-spark-sql"></a>Riutilizzo del calcolo in Spark SQL

Lo scenario di esempio seguente illustra come usare *SparkCruise* per ottimizzare le query Apache Spark. 

1. Connettersi tramite SSH al nodo head del cluster Spark.
1. Digitare `spark-shell`.
1. Eseguire il frammento di codice seguente, che esegue alcune query di base usando dati di esempio nel cluster.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. Usare lo strumento di analisi delle query *SparkCruise* per analizzare i piani di query delle query precedenti, che vengono archiviati nei log dell'applicazione Spark. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. Consente di visualizzare l'output del processo di analisi, ovvero un file di feedback. Questo file di feedback contiene le annotazioni per le query SQL Spark future. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

Il `analyze` comando analizza i piani di query e crea una rappresentazione tabulare del carico di lavoro. È possibile eseguire query su questa tabella del carico di lavoro usando il notebook di *WorkloadInsights* incluso nel repository di [esempi di HDInsight SparkCruise](https://github.com/Azure-Samples/azure-sparkcruise-samples) . Quindi, il `views` comando identifica le espressioni di sottopiano comuni e seleziona le espressioni di sottopiano interessanti per la materializzazione futura e il riutilizzo. L'output è un file di feedback contenente le annotazioni per le query SQL Spark future. 

Il `show` comando Visualizza un output simile al testo seguente:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

Il file di feedback contiene le voci nel formato seguente: `subplan-identifier [Materialize|Reuse] input/path/to/action` . In questo esempio sono presenti due firme univoche: una che rappresenta le prime due query ripetute e la seconda che rappresenta il predicato del filtro nelle ultime due query. Con questo file di feedback, le query seguenti, quando inviate nuovamente, verranno materializzate e riutilizzate automaticamente nei sottopiani comuni. 

Per testare le ottimizzazioni, eseguire un altro set di query di esempio.

1. Digitare `spark-shell`.
1. Eseguire il frammento di codice seguente

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. Visualizzare di nuovo il file di feedback per visualizzare le firme delle query riutilizzate.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

Il `show` comando restituisce un output simile al testo seguente:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

Sebbene il valore letterale nella query sia stato modificato da `'11%'` a `'12%'` , *SparkCruise* può comunque corrispondere alle query precedenti a nuove query con lievi variazioni come l'evoluzione dei valori letterali e delle versioni del set di dati. Se sono state apportate modifiche importanti a una query, è possibile eseguire nuovamente lo strumento di analisi per identificare le nuove query che possono essere riutilizzate.

Dietro le quinte, *SparkCruise* attiva una sottoquery per materializzazione il sottopiano selezionato dalla prima query che la contiene. Le query successive possono leggere direttamente i sottopiani materializzati anziché ricalcolarli. In questo carico di lavoro, i sottopiani verranno materializzati in modo online tramite la prima e la terza query. È possibile visualizzare la modifica del piano delle query dopo che i sottopiani comuni sono stati materializzati.

Si noterà che sono ora disponibili quattro nuove sottoespressioni materializzate che è possibile riutilizzare nelle query successive.

## <a name="clean-up"></a>Eseguire la pulizia

I file di feedback, i sottopiani materializzati e i log di query vengono mantenuti nelle sessioni Spark. Per rimuovere questi file, eseguire il comando seguente:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Passaggi successivi

* [Usare il notebook di workload Insights per determinare i vantaggi di SparkCruise](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Migliorare le prestazioni dei carichi di lavoro Apache Spark usando la cache IO di Azure HDInsight](apache-spark-improve-performance-iocache.md)
* [Ottimizzare i processi Apache Spark in HDInsight](./apache-spark-perf.md)
* [SparkCruise: riutilizzo del calcolo vivavoce in Spark](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Linee guida Apache Spark in Azure HDInsight](./spark-best-practices.md)
