---
title: Informazioni su Hive e su come usare HiveQL | Microsoft Docs
description: Informazioni su Apache Hive e su come usarlo con Hadoop in HDInsight. Scegliere come eseguire il processo Hive e usare HiveQL per analizzare un file Apache log4j di esempio.
keywords: hiveql, che cos’è hive
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/19/2016
ms.author: larryfr

---
# Usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file Apache log4j di esempio
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Questa esercitazione fornisce informazioni su come usare Apache Hive di Hadoop su HDInsight e come scegliere la modalità di esecuzione del processo Hive. Descrive inoltre il linguaggio HiveQL e illustra come analizzare un file Apache log4j di esempio.

## <a id="why"></a>Che cos'è Hive e perché usarlo?
[Apache Hive](http://hive.apache.org/) è un sistema di data warehouse per Hadoop, che consente di eseguire attività di riepilogo, query e analisi di volumi di dati tramite HiveQL (un linguaggio di query simile a SQL). Può essere usato per esplorare i dati in modo interattivo o per creare processi di elaborazione di batch riusabili.

Hive consente di proiettare la struttura su dati principalmente non strutturati. Dopo aver definito la struttura, Hive consente di eseguire una query sui dati anche senza alcuna conoscenza di Java o MapReduce. **HiveQL**, il linguaggio di query di Hive, consente di scrivere query con istruzioni simili a quelle di T-SQL.

Hive è in grado di gestire perfettamente dati strutturati e semistrutturati, ad esempio file di testo in cui i campi sono delimitati da caratteri specifici. Hive supporta inoltre **serializzatori/deserializzatori** personalizzati per dati complessi o strutturati in modo irregolare. Per altre informazioni, vedere l'articolo su [come usare un serializzatore/deserializzatore JSON personalizzato con HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## Funzioni definite dall'utente (UDF)
Hive può anche essere esteso tramite **funzioni definite dall'utente (UDF)**, che consentono di implementare funzionalità o logica non facilmente modellate in HiveQL. Per un esempio sull'uso di funzioni definite dall'utente con Hive, vedere gli argomenti seguenti:

* [Usare una funzione Java definita dall'utente con Hive](hdinsight-hadoop-hive-java-udf.md)
* [Usare Python con Hive e Pig in HDInsight](hdinsight-python.md)
* [Usare C# con Hive e Pig in HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Come aggiungere una funzione definita dall'utente Hive personalizzata in HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)
* [Esempio di funzione definita dall'utente Hive personalizzata per convertire i formati di data/ora in timestamp Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## Confronto tra le tabelle interne ed esterne di Hive
Vi sono alcune informazioni che è necessario conoscere sulle tabelle Hive interna ed esterna:

* Il comando **CREATE TABLE** crea una tabella interna. Il file di dati deve trovarsi nel contenitore predefinito.
* Il comando **CREATE TABLE** sposta il file di dati nella cartella /hive/warehouse/<TableName>.
* Il comando **CREATE EXTERNAL TABLE** crea una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
* Il comando **CREATE EXTERNAL TABLE** non sposta il file di dati.
* Il comando **CREATE EXTERNAL TABLE** non consente la creazione di cartelle in LOCATION. È per questo motivo che nell'esercitazione si esegue una copia del file sample.log.

Per altre informazioni, vedere l'[articolo introduttivo alle tabelle Hive interne ed esterne di HDInsight][cindygross-hive-tables].

## <a id="data"></a>Informazioni sui dati di esempio: file log4j di Apache
Questo esempio usa un file di esempio *log4j*, che viene archiviato in **/example/data/sample.log** nel contenitore di archiviazione BLOB. Ogni log all'interno del file è costituito da una riga di campi che contiene un campo `[LOG LEVEL]` per visualizzare il tipo e la gravità. Ad esempio:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Nell'esempio precedente, il livello log è ERROR.

> [!NOTE]
> È anche possibile generare un file log4j usando lo strumento di registrazione [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e quindi caricandolo nel contenitore BLOB. Per istruzioni, vedere [Caricamento di dati in HDInsight](hdinsight-upload-data.md). Per altre informazioni sul modo in cui HDInsight usa l'archivio BLOB di Azure, vedere [Usare l'archivio BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).
> 
> 

I dati di esempio vengono archiviati nell'archivio BLOB di Azure, usata da HDInsight come file system predefinito. HDInsight può accedere ai file archiviati nei BLOB tramite il prefisso **wasb**. Ad esempio, per accedere al file sample.log, usare la sintassi seguente:

    wasbs:///example/data/sample.log

Poiché l'archivio BLOB di Azure è la risorsa di archiviazione predefinita per HDInsight, è anche possibile accedere al file usando **/example/data/sample.log** in HiveQL.

> [!NOTE]
> La sintassi precedente, **wasbs:///**, consente di accedere ai file archiviati nel contenitore di archiviazione predefinito per il cluster HDInsight. Se durante il provisioning del cluster sono stati specificati account di archiviazione aggiuntivi e si vuole accedere ai file archiviati in tali account, è possibile accedere ai dati specificando il nome del contenitore e l'indirizzo dell'account di archiviazione, ad esempio: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.
> 
> 

## <a id="job"></a>Processo di esempio: Proiettare colonne in dati delimitati
Le istruzioni HiveQL seguenti consentono di proiettare colonne in dati delimitati archiviati nella directory **wasbs:///example/data**:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

Nell'esempio precedente, le istruzioni HiveQL eseguono le azioni seguenti:

* **set hive.execution.engine=tez;**: imposta il motore di esecuzione per l'uso di Tez. L'uso di Tez invece di MapReduce offre un aumento delle prestazioni delle query. Per altre informazioni su Tez, vedere la sezione [Usare Apache Tez per ottenere prestazioni migliorate](#usetez).
  
  > [!NOTE]
  > Questa istruzione è obbligatorio solo quando si usa un cluster HDInsight basato su Windows. Tez è il motore di esecuzione predefinito per HDInsight basato su Linux.
  > 
  > 
* **DROP TABLE**: elimina la tabella e il file di dati, se la tabella esiste già.
* **CREATE EXTERNAL TABLE**: crea una nuova tabella **esterna** in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione e nel formato originali.
* **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.
* **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo. I dati possono essere contenuti in un file o distribuiti tra più file all'interno della directory.
* **SELECT**: seleziona il numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.
* **INPUT\_\_FILE\_\_NAME come '%.log'** -indica ad Hive che si dovrebbero restituire solo i dati da file che terminano con. log. Questo limita la ricerca al file sample. log che contiene i dati, ed evita la restituzione di dati da altri file di dati di esempio che non corrispondono allo schema che è stato definito.

> [!NOTE]
> È consigliabile usare tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, e si desidera che le query Hive usino sempre i dati più recenti.
> 
> L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.
> 
> 

Dopo aver creato la tabella esterna, è possibile creare una **tabella** interna usando le istruzioni seguenti.

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Di seguito sono elencate le istruzioni che eseguono queste azioni:

* **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive.
* **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Questo è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
* **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs**.

> [!NOTE]
> A differenza delle tabelle esterne, se si elimina una tabella interna vengono eliminati anche i dati sottostanti.
> 
> 

## <a id="usetez"></a>Usare Apache Tez per ottenere prestazioni migliorate
[Apache Tez](http://tez.apache.org) è un framework che consente di eseguire applicazioni come Hive, che richiedono un uso elevato di dati, in modo molto più efficiente e scalabile. Nella versione più recente di HDInsight, Hive supporta l'esecuzione su Tez. Tez è abilitata come impostazione predefinita per i cluster HDInsight basati su Linux.

> [!NOTE]
> Tez è attualmente disattivata per impostazione predefinita per i cluster HDInsight basati su Windows e deve essere abilitata. Per poter usufruire dei vantaggi di Tez, è necessario impostare il valore seguente per una query Hive:
> 
> ```set hive.execution.engine=tez;```
> 
> È possibile inviarlo in ogni query inserendolo all'inizio della stessa. È anche possibile impostarlo come valore predefinito su un cluster scegliendo il valore di configurazione al momento della creazione del cluster. Per informazioni più dettagliate, vedere [Provisioning di cluster HDInsight](hdinsight-provision-clusters.md).
> 
> 

La [documentazione sulla progettazione di Hive su Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contiene una serie di informazioni dettagliate sulle scelte di implementazione e l'ottimizzazione delle configurazioni.

Per facilitare il debug di processi eseguiti mediante Tez, HDInsight fornisce le interfacce utente Web seguenti che consentono di visualizzare i dettagli dei processi Tez:

* [Usare l'interfaccia utente di Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md)
* [Usare la vista Ambari Tez in HDInsight basato su Linux](hdinsight-debug-ambari-tez-view.md)

## <a id="run"></a>Scegliere la modalità di esecuzione del processo HiveQL
HDInsight è in grado di eseguire processi HiveQL in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo** se si desidera... | ...una shell **interattiva** | ...elaborazione **batch** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:--- |:---:|:---:|:--- |:--- |
| [Vista di Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Qualsiasi versione (basata su browser) |
| [Comando beeline (da una sessione SSH)](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [Comando hive (da una sessione SSH)](hdinsight-hadoop-use-hive-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [Curl](hdinsight-hadoop-use-hive-curl.md) |& nbsp; |✔ |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Console di query](hdinsight-hadoop-use-hive-query-console.md) |& nbsp; |✔ |Windows |Qualsiasi versione (basata su browser) |
| [HDInsight Tools per Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |& nbsp; |✔ |Linux o Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |& nbsp; |✔ |Linux o Windows |Windows |
| [Desktop remoto](hdinsight-hadoop-use-hive-remote-desktop.md) |✔ |✔ |Windows |Windows |

## Esecuzione di processi Hive in Azure HDInsight tramite SQL Server Integration Services locali
È inoltre possibile utilizzare SQL Server Integration Services (SSIS) per eseguire un processo Hive. Il Feature Pack di Azure per SSIS fornisce i seguenti componenti che funzionano con i processi Hive in HDInsight.

* [Attività di Hive di Azure HDInsight][hivetask]
* [Gestione connessione della sottoscrizione di Azure][connectionmanager]

Altre informazioni sul Feature Pack di Azure per SSIS sono disponibili [qui][ssispack].

## <a id="nextsteps"></a>Passaggi successivi
Dopo aver appreso cos'è Hive e come si usa con Hadoop in HDInsight, vedere i collegamenti seguenti per scoprire altre modalità di utilizzo di Azure HDInsight.

* [Caricare dati in HDInsight][hdinsight-upload-data]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop.md)
* [Usare Oozie con HDInsight](hdinsight-use-oozie.md)
* [Usare processi MapReduce con HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

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

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

<!---HONumber=AcomDC_0921_2016-->