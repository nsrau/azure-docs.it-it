<properties
   pageTitle="Connessione di MapReduce e SSH con Hadoop in HDInsight | Microsoft Azure"
   description="Informazioni su come usare SSH per eseguire processi MapReduce con Hadoop in HDInsight."
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
   ms.date="06/06/2016"
   ms.author="larryfr"/>

# Usare MapReduce con Hadoop in HDInsight con SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In questo articolo si apprenderà come usare SSH (Secure Shell) per connettersi a un cluster Hadoop in HDInsight e quindi inviare processi MapReduce usando i comandi Hadoop.

> [AZURE.NOTE] Se si ha già familiarità con l'uso di server Hadoop basati su Linux ma non si è esperti di HDInsight, vedere [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Linux.

* Un client SSH. I sistemi operativi Linux, Unix e Mac dovrebbero essere dotati di un client SSH. Per gli utenti di Windows è necessario scaricare un client, ad esempio [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Connettersi con SSH

Connettersi al nome di dominio completo (FQDN) del cluster HDInsight usando il comando SSH. L'FQDN è costituito dal nome assegnato al cluster, seguito da **.azurehdinsight.net**. Ad esempio, il seguente comando stabilirà la connessione a un cluster denominato **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client, ad esempio:

	ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, sarà necessario fornire la password quando richiesto.

Per altre informazioni sull'uso di SSH con HDInsight, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (client di Windows)

Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Usare i comandi Hadoop

1. Dopo essersi connessi al cluster HDInsight, usare il seguente comando **Hadoop** per l'avvio di un processo MapReduce:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

	Viene avviata la classe **wordcount**, contenuta nel file **hadoop-mapreduce-examples.jar**. Come input, usa il documento **wasbs://example/data/gutenberg/davinci.txt** e l'output viene archiviato in **wasbs:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Per altre informazioni su questo processo MapReduce e per dati di esempio, vedere [Usare MapReduce in Hadoop in HDInsight](hdinsight-use-mapreduce.md).

2. Il processo genera dettagli durante l'elaborazione e al completamento restituisce informazioni simili alle seguenti:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Al termine del processo, usare il seguente comando per elencare i file di output archiviati in **wasbs://example/data/WordCountOutput**:

		hdfs dfs -ls wasbs:///example/data/WordCountOutput

	In questo modo vengono visualizzati due file: **\_SUCCESS** e **part-r-00000**. Il file **part-r-00000** contiene l'output del processo.

	> [AZURE.NOTE] Alcuni processi MapReduce possono dividere i risultati in più file **part-r-#####**. In questo caso, usare il suffisso ##### per indicare l'ordine dei file.

4. Per visualizzare l'output, usare il seguente comando:

		hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

	Questo comando visualizza un elenco delle parole contenute nel file **wasbs://example/data/gutenberg/davinci.txt** e il numero di occorrenze di ogni parola. Di seguito è riportato un esempio di dati contenuti nel file:

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Riepilogo

Come è possibile notare, i comandi Hadoop forniscono un modo semplice per eseguire processi MapReduce in un cluster HDInsight e visualizzare l'output del processo.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce in HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0727_2016-->