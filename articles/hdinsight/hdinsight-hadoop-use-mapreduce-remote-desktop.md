---
title: MapReduce e Remote Desktop con Hadoop in HDInsight | Documentazione Microsoft
description: Informazioni su come usare Desktop remoto per connettersi a Hadoop in HDInsight ed eseguire processi MapReduce.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: dc8e9647d99b39cdee36ec11e144452326e2d968
ms.openlocfilehash: 99a3e44730737a9e1b3db8eead6dfe181382962b


---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Uso di MapReduce con Hadoop in HDInsight con Desktop remoto
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In questo articolo si apprenderà come connettersi a un cluster Hadoop in HDInsight usando Desktop remoto e quindi eseguire processi MapReduce usando il comando Hadoop.

> [!IMPORTANT]
> Desktop re,moto è disponibile solo nei cluster HDInsight basati su Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).
>
> Per HDInsight 3.4 o versione successiva, vedere [Usare MapReduce con SSH](hdinsight-hadoop-use-mapreduce-ssh.md) per informazioni sulla connessione al cluster HDInsight e l'esecuzione di processi MapReduce.

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Prerequisiti
Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Windows
* Un computer client che esegue Windows 10, Windows 8 o Windows 7

## <a name="a-idconnectaconnect-with-remote-desktop"></a><a id="connect"></a>Connettersi con Desktop remoto
Abilitare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a name="a-idhadoopause-the-hadoop-command"></a><a id="hadoop"></a>Usare il comando Hadoop
Una volta connessi al desktop per il cluster HDInsight, seguire questa procedura per eseguire un processo MapReduce usando il comando Hadoop:

1. Dal desktop di HDInsight avviare la **riga di comando di Hadoop**. Viene aperta una finestra del prompt dei comandi nella directory **c:\apps\dist\hadoop-&lt;numero versione>**.

   > [!NOTE]
   > Quando Hadoop viene aggiornato, il numero di versione viene modificato. La variabile di ambiente **HADOOP_HOME** può essere usata per trovare il percorso. Ad esempio, `cd %HADOOP_HOME%` modifica le directory nella directory di Hadoop, senza richiedere il nome della versione.
   >
   >
2. Per usare il comando **Hadoop** per l'esecuzione di un processo MapReduce di esempio, usare il comando seguente:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Viene avviata la classe **wordcount**, contenuta nel file **hadoop-mapreduce-examples.jar** nella directory corrente. Come input, la classe usa il documento **wasbs://example/data/gutenberg/davinci.txt** e l'output viene archiviato in: **wasbs:///example/data/WordCountOutput**.

   > [!NOTE]
   > Per altre informazioni su questo processo MapReduce e per dati di esempio, vedere <a href="hdinsight-use-mapreduce.md">Usare MapReduce in Hadoop in HDInsight</a>.
   >
   >
3. Il processo genera dettagli durante l'elaborazione e al completamento restituisce informazioni simili alle seguenti:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Al termine del processo, usare il comando seguente per elencare i file di output archiviati in **wasbs://example/data/WordCountOutput**:

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    In questo modo, vengono visualizzati due file: **_SUCCESS** e **part-r-00000**. Il file **part-r-00000** contiene l'output del processo.

   > [!NOTE]
   > Alcuni processi MapReduce possono dividere i risultati in più file **part-r-#####** . In questo caso, usare il suffisso ##### per indicare l'ordine dei file.
   >
   >
5. Per visualizzare l'output, usare il seguente comando:

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Viene visualizzato un elenco di parole contenute nel file **wasbs://example/data/gutenberg/davinci.txt** con il numero di occorrenze di ogni parola. Di seguito è riportato un esempio di dati contenuti nel file:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a name="a-idsummaryasummary"></a><a id="summary"></a>Riepilogo
Come è possibile notare, il comando Hadoop fornisce un modo semplice per eseguire processi MapReduce in un cluster HDInsight e visualizzare l'output del processo.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Passaggi successivi
Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce in HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)



<!--HONumber=Jan17_HO3-->


