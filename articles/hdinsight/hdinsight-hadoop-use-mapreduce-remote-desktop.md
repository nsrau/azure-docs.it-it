<properties
   pageTitle="MapReduce e Remote Desktop con Hadoop in HDInsight | Microsoft Azure"
   description="Informazioni su come usare Desktop remoto per connettersi a Hadoop in HDInsight ed eseguire processi MapReduce."
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
   ms.date="12/04/2015"
   ms.author="larryfr"/>

# Uso di MapReduce con Hadoop in HDInsight con Desktop remoto

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In questo articolo si apprenderà come connettersi a un cluster Hadoop in HDInsight usando Desktop remoto e quindi eseguire processi MapReduce usando il comando Hadoop.

##<a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Windows

* Un computer client che esegue Windows 10, Windows 8 o Windows 7

##<a id="connect"></a>Connettersi con Desktop remoto

Abilitare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Usare il comando Hadoop

Una volta connessi al desktop per il cluster HDInsight, seguire questa procedura per eseguire un processo MapReduce usando il comando Hadoop:

1. Dal desktop di HDInsight avviare la **riga di comando di Hadoop**. Viene aperta una finestra del prompt dei comandi nella directory **c:\\apps\\dist\\hadoop-&lt;numero versione>**.

	> [AZURE.NOTE]Quando Hadoop viene aggiornato, il numero di versione viene modificato. La variabile di ambiente **HADOOP\_HOME** può essere usata per trovare il percorso. Ad esempio, `cd %HADOOP_HOME%` modifica le directory nella directory di Hadoop, senza richiedere il nome della versione.

2. Per usare il comando **Hadoop** per l'esecuzione di un processo MapReduce di esempio, usare il comando seguente:

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Viene avviata la classe **wordcount**, contenuta nel file **hadoop-mapreduce-examples.jar** nella directory corrente. Come input, usa il documento ****wasb://example/data/gutenberg/davinci.txt** e l'output viene archiviato in ****wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE]Per altre informazioni su questo processo MapReduce e per dati di esempio, vedere <a href="hdinsight-use-mapreduce.md">Usare MapReduce in Hadoop in HDInsight</a>.

2. Il processo genera dettagli durante l'elaborazione e al completamento restituisce informazioni simili alle seguenti:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Al termine del processo, usare il seguente comando per elencare i file di output archiviati in ****wasb://example/data/WordCountOutput**:

		hadoop fs -ls wasb:///example/data/WordCountOutput

	In questo modo, vengono visualizzati due file: **\_SUCCESS** e **part-r-00000**. Il file **part-r-00000** contiene l'output del processo.

	> [AZURE.NOTE]Alcuni processi MapReduce possono dividere i risultati in più file **part-r-#####**. In questo caso, usare il suffisso ##### per indicare l'ordine dei file.

4. Per visualizzare l'output, usare il seguente comando:

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Questo comando visualizza un elenco delle parole contenute nel file ****wasb://example/data/gutenberg/davinci.txt** e il numero di occorrenze di ogni parola. Di seguito è riportato un esempio di dati contenuti nel file:

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Riepilogo

Come è possibile notare, il comando Hadoop fornisce un modo semplice per eseguire processi MapReduce in un cluster HDInsight e visualizzare l'output del processo.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce in HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_1210_2015-->