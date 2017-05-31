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
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 04d5b7ea9835e3196bbe300384e9097d7c667e3f
ms.contentlocale: it-it
ms.lasthandoff: 05/04/2017


---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Usare MapReduce con Hadoop in HDInsight con SSH

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Informazioni su come inviare processi MapReduce da una connessione Secure Shell (SSH) a HDInsight.

> [!NOTE]
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux ma non si è esperti di HDInsight, vedere [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Prerequisiti

* Un cluster HDInsight (Hadoop in HDInsight) basato su Linux.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'articolo sul [controllo delle versioni del componente di HDInsight](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Un client SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="ssh"></a>Connettersi con SSH

Connettersi al cluster tramite SSH. Il comando seguente, ad esempio, stabilisce la connessione a un cluster denominato **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se si usa una chiave del certificato per l'autenticazione SSH**, è possibile che sia necessario specificare il percorso della chiave privata nel sistema client, ad esempio:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se si usa una password per l'autenticazione SSH**, è necessario fornire la password quando viene richiesta.

Per altre informazioni sull'uso di SSH con HDInsight, vedere l'articolo [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Usare i comandi Hadoop

1. Dopo essersi connessi al cluster HDInsight, usare il comando seguente per avviare un processo MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Questo comando avvia la classe `wordcount`, contenuta nel file `hadoop-mapreduce-examples.jar`. Usa il documento `/example/data/gutenberg/davinci.txt` come input, mentre l'output viene archiviato in `/example/data/WordCountOutput`.

    > [!NOTE]
    > Per altre informazioni su questo processo MapReduce e per dati di esempio, vedere [Usare MapReduce in Hadoop in HDInsight](hdinsight-use-mapreduce.md).

2. Il processo genera dettagli durante l'elaborazione e, al termine, restituisce informazioni simili alle seguenti:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Al termine del processo, usare il comando seguente per elencare i file di output:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Con questo comando vengono visualizzati due file: `_SUCCESS` e `part-r-00000`. Il file `part-r-00000` contiene l'output del processo.

    > [!NOTE]
    > Alcuni processi MapReduce possono dividere i risultati in più file **part-r-#####** . In questo caso, usare il suffisso ##### per indicare l'ordine dei file.

4. Per visualizzare l'output, usare il seguente comando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Viene visualizzato un elenco di parole contenute nel file **wasbs://example/data/gutenberg/davinci.txt** e il numero di occorrenze di ogni parola. Di seguito è riportato un esempio dei dati contenuti nel file:

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

