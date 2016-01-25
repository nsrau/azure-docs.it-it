<properties
   pageTitle="Usare Pig di Hadoop in HDInsight | Microsoft Azure"
   description="Informazioni su come usare Pig con Hadoop in HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/08/2016"
   ms.author="larryfr"/>

# Usare Pig con Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache Pig](http://pig.apache.org/) è una piattaforma che consente la creazione di programmi per Hadoop usando un linguaggio procedurale denominato *Pig Latin*. Pig è un'alternativa a Java per la creazione di soluzioni *MapReduce* ed è incluso in Azure HDInsight.

Questo articolo illustra come usare Pig con HDInsight.

##<a id="why"></a>Perché usare Pig?

Quando si elaborano dati usando MapReduce in Hadoop, uno dei problemi principali è riuscire a implementare la logica di elaborazione usando solo una mappa e una funzione di riduzione. In caso di esigenze di elaborazione complesse, è spesso necessario interrompere l'elaborazione in più operazioni di MapReduce concatenate insieme per ottenere il risultato desiderato.

Pig non costringe l'utente a usare solo una mappa e una funzione di riduzione, ma consente di definire l'elaborazione come una serie di trasformazioni dei flussi dati fino a raggiungere l'output desiderato.

Il linguaggio Pig Latin consente di descrivere il flusso dati dall'input non elaborato fino all'output desiderato, attraverso una o più trasformazioni. I programmi in Pig Latin seguono questo modello generale:

- **Load**: lettura dei dati da manipolare dal file system
- **Transform**: manipolazione dei dati
- **Dump or store**: output dei dati sullo schermo o archiviazione per l'elaborazione

Pig Latin supporta anche funzioni definite dall'utente (UDF), che consentono di richiamare componenti esterni in grado di implementare logica difficile da modellare in Pig Latin.

Per altre informazioni su Pig Latin, vedere il [manuale di riferimento di Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e il [manuale di riferimento di Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Per un esempio sull'uso di funzioni definite dall'utente con Pig, vedere i documenti seguenti:

* [Usare DataFu con Pig in HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu è una raccolta di utili funzioni definite dall'utente gestite mediante Apache

* [Usare Python con Pig e Hive in HDInsight](hdinsight-python.md)

* [Usare C# con Hive e Pig in HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Informazioni sui dati di esempio

Questo esempio usa un file di esempio *log4j*, che viene archiviato in **/example/data/sample.log** nel contenitore di archiviazione BLOB. Ogni log all'interno del file è costituito da una riga di campi che contiene un campo `[LOG LEVEL]` per visualizzare il tipo e la gravità. Ad esempio:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Nell'esempio precedente, il livello log è ERROR.

> [AZURE.NOTE]È anche possibile generare un file log4j usando lo strumento di registrazione [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e quindi caricandolo nel BLOB. Per istruzioni, vedere [Caricamento di dati in HDInsight](hdinsight-upload-data.md). Per altre informazioni sul modo in cui HDInsight usa l'archiviazione BLOB di Azure, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).

I dati di esempio vengono archiviati nell'archiviazione BLOB di Azure, usata da HDInsight come file system predefinito per i cluster Hadoop. HDInsight può accedere ai file archiviati nei BLOB tramite il prefisso **wasb**. Ad esempio, per accedere al file sample.log, usare la sintassi seguente:

	wasb:///example/data/sample.log

Poiché WASB è la risorsa di archiviazione predefinita per HDInsight, è anche possibile accedere al file usando **/example/data/sample.log** in Pig Latin.

> [AZURE.NOTE]La sintassi ****wasb:///**, viene usata per accedere ai file archiviati nel contenitore di archiviazione predefinito per il cluster HDInsight. Se durante il provisioning del cluster sono stati specificati account di archiviazione aggiuntivi e si vuole accedere ai file archiviati in tali account, è possibile accedere ai dati specificando il nome del contenitore e l'indirizzo dell'account di archiviazione, ad esempio ****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Informazioni sull'app di esempio

Il processo Pig Latin seguente carica il file **sample.log** dal percorso di archiviazione predefinito per il cluster HDInsight. Esegue quindi una serie di trasformazioni che generano un conteggio delle occorrenze di ciascun livello di log presente nei dati di input. I risultati vengono trasmessi a STDOUT.

	LOGS = LOAD 'wasb:///example/data/sample.log';
	LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
	FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
	GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
	FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
	RESULT = order FREQUENCIES by COUNT desc;
	DUMP RESULT;

L'immagine seguente illustra un riepilogo dei dati generati da ogni operazione di trasformazione.

![Rappresentazione grafica delle trasformazioni][image-hdi-pig-data-transformation]

##<a id="run"></a>Eseguire il processo Pig Latin

HDInsight è in grado di eseguire processi Pig Latin in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo** se si desidera... | ...una shell **interattiva** | ...elaborazione **batch** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X o Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) | & nbsp; | ✔ | Linux o Windows | Linux, Unix, Mac OS X o Windows |
| [.NET SDK per Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | & nbsp; | ✔ | Linux o Windows | Windows (per ora) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) | & nbsp; | ✔ | Linux o Windows | Windows |
| [Desktop remoto](hdinsight-hadoop-use-pig-remote-desktop.md) | ✔ | ✔ | Windows | Windows |


## Esecuzione di processi Pig in Azure HDInsight tramite SQL Server Integration Services

È inoltre possibile utilizzare SQL Server Integration Services per eseguire un processo Pig. Il Feature Pack di Azure per SSIS fornisce i seguenti componenti che funzionano con i processi Pig in HDInsight.


- [Attività di Pig di Azure HDInsight][pigtask]
- [Gestione connessione della sottoscrizione di Azure][connectionmanager]


Altre informazioni sul Feature Pack di Azure per SSIS sono disponibili [qui][ssispack].


##<a id="nextsteps"></a>Passaggi successivi

Dopo aver appreso come usare Pig con HDInsight, vedere i collegamenti seguenti per scoprire altre modalità di utilizzo di Azure HDInsight.

* [Caricare dati in HDInsight][hdinsight-upload-data]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare processi MapReduce con HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

<!---HONumber=AcomDC_0114_2016-->