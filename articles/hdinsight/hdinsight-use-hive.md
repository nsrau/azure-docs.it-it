---
title: Cosa sono Apache Hive e HiveQL - Azure HDInsight | Microsoft Docs
description: "Apache Hive è un sistema di data warehouse per Hadoop. È possibile eseguire query sui dati archiviati in Hive tramite HiveQL, che è simile a Transact-SQL. Questo documento riporta informazioni su come usare Hive e HiveQL con Azure HDInsight."
keywords: "hiveql, cos&quot;è hive, hadoop hiveql, come usare hive, informazioni su hive, cos&quot;è hive"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/12/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: b8c32f6e15d65efc8dc464017027d3cde94b2667
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Cosa sono Apache Hive e HiveQL in Azure HDInsight

[Apache Hive](http://hive.apache.org/) è un sistema di data warehouse per Hadoop. Hive consente di eseguire attività di riepilogo, query e analisi dei dati. Le query di Hive sono scritte in HiveQL, linguaggio di query simile a SQL.

Hive consente di proiettare la struttura su dati principalmente non strutturati. Dopo aver definito la struttura, è possibile usare HiveQL per eseguire una query sui dati anche senza alcuna conoscenza di Java o MapReduce.

HDInsight offre diversi tipi di cluster ottimizzati per carichi di lavoro specifici. I tipi di cluster usati più di frequente per le query Hive sono i seguenti:

* __Interactive Hive__: un cluster Hadoop che offre la funzionalità [Low Latency Analytical Processing (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) per migliorare i tempi di risposta per le query interattive. Per altre informazioni, vedere il documento su come [iniziare a usare Interactive Hive in HDInsight](hdinsight-hadoop-use-interactive-hive.md).

* __Hadoop__: un cluster Hadoop che è ottimizzato per carichi di lavoro di elaborazione batch. Per altre informazioni, vedere il documento su come [iniziare a usare Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

* __Spark__: Apache Spark ha una funzionalità integrata per l'utilizzo di Hive. Per altre informazioni, vedere il documento su come [iniziare a usare Spark in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* __HBase__: HiveQL può essere usato per eseguire query sui dati archiviati in HBase. Per altre informazioni, vedere il documento su come [iniziare a usare HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md).

## <a name="how-to-use-hive"></a>Come usare Hive

Consultare la tabella seguente per informazioni su come usare Hive con HDInsight:

| **Usare questo metodo** se si vuole... | ...una shell **interattiva** | ...elaborazione**batch** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:--- |:---:|:---:|:--- |:--- |
| [Vista di Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Qualsiasi versione (basata su browser) |
| [Client Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [API REST](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux o Windows* |Linux, Unix, Mac OS X o Windows |
| [HDInsight Tools per Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux o Windows* |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux o Windows* |Windows |

> [!IMPORTANT]
> \* Linux è l'unico sistema operativo utilizzato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Deprecazione di HDInsight 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).
>
> Se si usa un cluster HDInsight basato su Windows, è possibile usare la [console Query](hdinsight-hadoop-use-hive-query-console.md) dal browser o [Desktop remoto](hdinsight-hadoop-use-hive-remote-desktop.md) per eseguire query Hive.

## <a name="hiveql-language-reference"></a>Informazioni di riferimento sul linguaggio HiveQL

Informazioni di riferimento sul linguaggio HiveQL sono disponibili nel [manuale del linguaggio (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive e la struttura dei dati

Hive è in grado di usare dati strutturati e semistrutturati. Ad esempio, file di testo in cui i campi sono delimitati da caratteri specifici. L'istruzione HiveQL seguente crea una tabella di dati delimitati da spazi:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive supporta inoltre **serializzatori/deserializzatori** personalizzati per dati complessi o strutturati in modo irregolare. Per altre informazioni, vedere l'articolo su [come usare un serializzatore/deserializzatore JSON personalizzato con HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

Per altre informazioni sui formati di file supportati da Hive, vedere il [manuale del linguaggio (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

## <a name="hive-internal-tables-vs-external-tables"></a>Confronto tra le tabelle interne ed esterne di Hive

Con Hive è possibile creare due tipi di tabelle:

* __Interna__: i dati vengono archiviati nel data warehouse di Hive. Il data warehouse si trova in `/hive/warehouse/` nella risorsa di archiviazione predefinita per il cluster.

    Usare tabelle interne quando:

    * I dati sono temporanei.
    * Si desidera che Hive gestisca il ciclo di vita della tabella e dei dati.

* __Interna__: i dati vengono archiviati all'esterno del data warehouse. I dati possono essere archiviati in tutte le risorse di archiviazione accessibili dal cluster.

    Usare tabelle esterne quando:

    * I dati vengono usati anche all'esterno di Hive. Ad esempio, i file di dati vengono aggiornati da un altro processo (che non blocca i file).
    * I dati devono rimanere nel percorso sottostante, anche dopo l'eliminazione della tabella.
    * È necessario un percorso personalizzato, ad esempio un account di archiviazione non predefinito.
    * Un programma diverso da Hive gestisce il formato dei dati, il percorso e così via.

Per altre informazioni, vedere il post di blog [Hive Internal and External Tables Intro][cindygross-hive-tables] (Introduzione alle tabelle interne ed esterne di Hive).

## <a name="user-defined-functions-udf"></a>Funzioni definite dall'utente (UDF)

Hive può anche essere esteso tramite **funzioni definite dall'utente (UDF)**, che consentono di implementare funzionalità o logica non facilmente modellate in HiveQL. Per un esempio sull'uso di funzioni definite dall'utente con Hive, vedere i documenti seguenti:

* [Usare una funzione Java definita dall'utente con Hive](hdinsight-hadoop-hive-java-udf.md)

* [Usare una funzione Python definita dall'utente con Hive e Pig](hdinsight-python.md)

* [Usare una funzione C# definita dall'utente con Hive e Pig](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Come aggiungere una funzione Hive personalizzata definita dall'utente in HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Esempio di funzione Hive personalizzata definita dall'utente per convertire i formati di data/ora in timestamp Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Dati di esempio

Hive in HDInsight include una tabella interna denominata `hivesampletable`. HDInsight offre inoltre set di dati di esempio che possono essere usati con Hive. Questi set di dati sono archiviati nelle directory `/example/data` e `/HdiSamples`. Queste directory si trovano nella risorsa di archiviazione predefinita per il cluster.

## <a id="job"></a>Query Hive di esempio

Le seguenti istruzioni di HiveQL creano colonne nel file `/example/data/sample.log`:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

Nell'esempio precedente, le istruzioni HiveQL eseguono le azioni seguenti:

* `set hive.execution.engine=tez;`: configura il motore di esecuzione per l'uso di Tez. L'uso di Tez invece di MapReduce offre un aumento delle prestazioni delle query. For more information on Tez, see the [Use Apache Tez for improved performance](#usetez) section.

    > [!NOTE]
    > Questa istruzione è obbligatoria solo quando si usa un cluster HDInsight basato su Windows. Tez è il motore di esecuzione predefinito per HDInsight basato su Linux.

* `DROP TABLE`: se la tabella esiste già, eliminarla.

* `CREATE EXTERNAL TABLE`: crea una nuova tabella **esterna** in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso e nel formato originale.

* `ROW FORMAT`: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

* `STORED AS TEXTFILE LOCATION`: indica a Hive dove sono archiviati i dati (la directory `example/data`) e che sono archiviati come testo. I dati possono essere contenuti in un file o distribuiti tra più file all'interno della directory.

* `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna **t4** contiene il valore **[ERROR]**. L'istruzione restituisce un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.

* `INPUT__FILE__NAME LIKE '%.log'`: Hive tenta di applicare lo schema a tutti i file della directory. In questo caso la directory contiene file che non corrispondono allo schema. Per evitare dati errati nei risultati, questa istruzione indica a Hive che devono essere restituiti dati solo da file che terminano con .log.

> [!NOTE]
> Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo di caricamento dati automatizzato o un'operazione MapReduce.
>
> L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

Per creare una tabella **interno** anziché esterna, usare il codice HiveQL seguente:

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Di seguito sono elencate le istruzioni che eseguono queste azioni:

* `CREATE TABLE IF NOT EXISTS`: se la tabella non esiste, crearla. Poiché non viene usata la parola chiave **EXTERNAL**, questa istruzione crea una tabella interna. La tabella viene archiviata nel data warehouse di Hive e gestita completamente da Hive.

* `STORED AS ORC`: archivia i dati nel formato ORC, Optimized Row Columnar. ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.

* `INSERT OVERWRITE ... SELECT`: seleziona dalla tabella**log4jLogs** le righe contenenti **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs**.

> [!NOTE]
> A differenza delle tabelle esterne, se si elimina una tabella interna vengono eliminati anche i dati sottostanti.

## <a name="improve-hive-query-performance"></a>Ottimizzare le prestazioni delle query di Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) è un framework che consente di eseguire applicazioni come Hive, che richiedono un uso elevato di dati, in modo molto più efficiente e scalabile. Tez è abilitata come impostazione predefinita per i cluster HDInsight basati su Linux.

> [!NOTE]
> Tez è attualmente disattivata per impostazione predefinita per i cluster HDInsight basati su Windows e deve essere abilitata. Per poter usufruire dei vantaggi di Tez, è necessario impostare il valore seguente per una query Hive:
>
> `set hive.execution.engine=tez;`
>
> Tez è il motore predefinito per i cluster HDInsight basati su Linux.

La [documentazione sulla progettazione di Hive su Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contiene le informazioni dettagliate sulle scelte di implementazione e l'ottimizzazione delle configurazioni.

Per facilitare il debug di processi eseguiti mediante Tez, HDInsight fornisce le interfacce utente Web seguenti che consentono di visualizzare i dettagli dei processi Tez:

* [Usare la vista Ambari Tez in HDInsight basato su Linux](hdinsight-debug-ambari-tez-view.md)

* [Usare l'interfaccia utente di Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Low Latency Analytical Processing (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (o Live Long and Process) è una nuova funzionalità di Hive 2.0 che consente di mettere in cache le query in memoria. LLAP rende molto più veloci le query Hive, in alcuni casi fino a [26 volte più veloci rispetto a Hive 1.x](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight offre LLAP nel tipo di cluster Interactive Hive. Per altre informazioni, vedere il documento su come [iniziare a usare Interactive Hive](hdinsight-hadoop-use-interactive-hive.md).

## <a name="hive-jobs-and-sql-server-integration-services"></a>Processi di Hive e SQL Server Integration Services

È possibile usare SQL Server Integration Services (SSIS) per eseguire un processo Hive. Il Feature Pack di Azure per SSIS fornisce i seguenti componenti che funzionano con i processi Hive in HDInsight.

* [Attività di Hive di Azure HDInsight][hivetask]

* [Gestione connessione della sottoscrizione di Azure][connectionmanager]

Altre informazioni sul Feature Pack di Azure per SSIS sono disponibili [qui][ssispack].

## <a id="nextsteps"></a>Passaggi successivi

Dopo avere appreso che cos'è Hive e come si usa con Hadoop in HDInsight, vedere i collegamenti seguenti per scoprire altri modi di usare Azure HDInsight.

* [Caricare dati in HDInsight][hdinsight-upload-data]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare processi MapReduce con HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

