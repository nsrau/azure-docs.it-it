---
title: Connessione di MapReduce e SSH con Hadoop in HDInsight | Documentazione Microsoft
description: Informazioni su come usare SSH per eseguire processi MapReduce con Hadoop in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: e696906adf604ebdb665a7a29fd1bba25095e842
ms.lasthandoff: 03/25/2017


---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Usare MapReduce con Hadoop in HDInsight con SSH

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Questo articolo illustra come usare SSH (Secure Shell) per connettersi a un cluster Hadoop in HDInsight e quindi inviare processi MapReduce usando i comandi Hadoop.

> [!NOTE]
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux ma non si è esperti di HDInsight, vedere [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Linux.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Un client SSH. I sistemi operativi Linux, Unix e Mac dovrebbero essere dotati di un client SSH. Per gli utenti di Windows è necessario scaricare un client, ad esempio [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a id="ssh"></a>Connettersi con SSH

Connettersi al nome di dominio completo (FQDN) del cluster HDInsight usando il comando SSH. L'FQDN è costituito dal nome assegnato al cluster, seguito da **.azurehdinsight.net**. Ad esempio, il seguente comando stabilirà la connessione a un cluster denominato **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client, ad esempio:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, sarà necessario fornire la password quando richiesto.

Per altre informazioni sull'uso di SSH con HDInsight, vedere l'articolo [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Usare i comandi Hadoop

1. Dopo essersi connessi al cluster HDInsight, usare il seguente comando **Hadoop** per l'avvio di un processo MapReduce:
   
    ```
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Viene avviata la classe **wordcount**, contenuta nel file **hadoop-mapreduce-examples.jar**. Come input, la classe usa il documento **/example/data/gutenberg/davinci.txt** e l'output viene archiviato in **/example/data/WordCountOutput**.
   
    > [!NOTE]
    > Per altre informazioni su questo processo MapReduce e per dati di esempio, vedere [Usare MapReduce in Hadoop in HDInsight](hdinsight-use-mapreduce.md).

2. Il processo genera dettagli durante l'elaborazione e al completamento restituisce informazioni simili alle seguenti:
   
        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Al termine del processo, usare il comando seguente per elencare i file di output archiviati in **wasbs://example/data/WordCountOutput**:
   
    ```
    hdfs dfs -ls /example/data/WordCountOutput
    ```
   
    In questo modo, vengono visualizzati due file: **_SUCCESS** e **part-r-00000**. Il file **part-r-00000** contiene l'output del processo.
   
    > [!NOTE]
    > Alcuni processi MapReduce possono dividere i risultati in più file **part-r-#####** . In questo caso, usare il suffisso ##### per indicare l'ordine dei file.

4. Per visualizzare l'output, usare il seguente comando:
   
    ```
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```
   
    Viene visualizzato un elenco di parole contenute nel file **wasbs://example/data/gutenberg/davinci.txt** e il numero di occorrenze di ogni parola. Di seguito è riportato un esempio di dati contenuti nel file:
   
        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Riepilogo

Come è possibile notare, i comandi Hadoop forniscono un modo semplice per eseguire processi MapReduce in un cluster HDInsight e visualizzare l'output del processo.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce in HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)


