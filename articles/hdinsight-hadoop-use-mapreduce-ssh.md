<properties
   pageTitle="MapReduce con Hadoop in HDInsight | Azure"
   description="Informazioni su come usare SSH per eseguire processi MapReduce con Hadoop in HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Uso di Hive con Hadoop in HDInsight tramite SSH

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

In questo articolo si apprenderà come usare SSH per connettersi a un cluster Hadoop in HDInsight e quindi inviare processi MapReduce usando il comando Hadoop.

> [AZURE.NOTE] Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere <a href="../hdinsight-hadoop-linux-information/" target="_blank">Informazioni utili su Hadoop in HDInsight basato su Linux</a>.

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Linux.

* Un client SSH. I sistemi operativi Linux, Unix e Mac dovrebbero essere forniti con un client SSH. Per gli utenti di Windows è necessario scaricare un client, ad esempio <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

## <a id="ssh"></a>Connettersi tramite SSH

Connettersi al nome di dominio completo (FQDN) del cluster HDInsight usando il comando SSH. L'FQDN è costituito dal nome assegnato al cluster, seguito da **.azurehdinsight.net**. Ad esempio, il seguente comando stabilirà la connessione a un cluster denominato **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, sarà necessario fornire la password quando richiesto.

### PuTTY (client di Windows)

Windows non fornisce un client SSH incorporato. È consigliabile usare **Putty**, disponibile per il download da <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Per altre informazioni sull'uso di PuTTY, vedere la sezione sull'**uso di PuTTY per la connessione a un computer Linux** nella pagina relativa a <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Come usare SSH con Linux in Azure</a>.

> [AZURE.NOTE] Se è stato usato un certificato per l'autenticazione SSH per il cluster HDInsight, sarà necessario anche leggere la sezione sulla **creazione di una chiave privata PPK per PuTTY** nella pagina relativa a <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Come usare SSH con Linux in Azure</a>

## <a id="hadoop"></a>Usare il comando Hadoop

1. Dopo essersi connessi al cluster HDInsight, seguire quanto riportato di seguito per usare il comando **Hadoop** per l'avvio di un processo MapReduce.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Viene avviata la classe **wordcount**, contenuta nel file **hadoop-mapreduce-examples.jar**. Come input, la classe usa il documento **wasb://example/data/gutenberg/davinci.txt** e l'output viene archiviato in **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Per altre informazioni su questo processo MapReduce e per dati di esempio, vedere <a href="../hdinsight-use-mapreduce/" target="_blank">Usare MapReduce in Hadoop in HDInsight</a>.

2. Il processo genererà dettagli durante l'elaborazione, e al completamento restituirà informazioni simili alle seguenti.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Al termine, usare il seguente comando per elencare i file di output archiviati in **wasb://example/data/WordCountOutput**.

		hadoop fs -ls wasb:///example/data/WordCountOutput

	In questo modo, vengono visualizzati due file: **_SUCCESS** e **part-r-00000**. Il file **part-r-00000** contiene l'output del processo.

	> [AZURE.NOTE] Alcuni processi MapReduce possono dividere i risultati in più file **part-r-#####**. In questo caso, usare il suffisso ##### per indicare l'ordine dei file.

4. Per visualizzare l'output, usare il seguente comando:

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Verrà visualizzato un elenco di parole contenute nel file **wasb://example/data/gutenberg/davinci.txt** e il numero di occorrenze di ogni parola.  Di seguito è riportato un esempio di dati contenuti nel file.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

## <a id="summary"></a>Riepilogo

Come è possibile notare, il comando Hadoop fornisce un modo semplice per eseguire processi MapReduce in un cluster HDInsight e visualizzare l'output del processo.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight.

* [Usare MapReduce in HDInsight Hadoop](../hdinsight-use-mapreduce/)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Hive con Hadoop in HDInsight](../hdinsight-use-hive/)

* [Usare Pig con Hadoop in HDInsight](../hdinsight-use-pig/)
<!--HONumber=47-->
