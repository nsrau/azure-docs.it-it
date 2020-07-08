---
title: Connessione di MapReduce e SSH con Apache Hadoop-Azure HDInsight
description: Informazioni su come usare SSH per eseguire processi MapReduce con Apache Hadoop in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 2736d0cfe514252e36ba6d7e0d71efe09da15aae
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86076301"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Usare MapReduce con Apache Hadoop in HDInsight con SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Informazioni su come inviare processi MapReduce da una connessione Secure Shell (SSH) a HDInsight.

> [!NOTE]
> Se si ha già familiarità con l'uso di server Apache Hadoop basati su Linux ma non si è esperti di HDInsight, vedere [Informazioni sull'uso di HDInsight in Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Hadoop in HDInsight. Vedere [creare cluster Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Usare i comandi Hadoop

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Dopo essersi connessi al cluster HDInsight, usare il comando seguente per avviare un processo MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Questo comando avvia la classe `wordcount`, contenuta nel file `hadoop-mapreduce-examples.jar`. Usa il documento `/example/data/gutenberg/davinci.txt` come input, mentre l'output viene archiviato in `/example/data/WordCountOutput`.

    > [!NOTE]
    > Per altre informazioni su questo processo MapReduce e per i dati di esempio, vedere [Usare MapReduce in Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md).

    Il processo genera dettagli durante l'elaborazione e, al termine, restituisce informazioni simili alle seguenti:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Al termine del processo, usare il comando seguente per elencare i file di output:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Con questo comando vengono visualizzati due file: `_SUCCESS` e `part-r-00000`. Il file `part-r-00000` contiene l'output del processo.

    > [!NOTE]  
    > Alcuni processi MapReduce possono dividere i risultati in più file **part-r-#####** . In questo caso, usare il suffisso ##### per indicare l'ordine dei file.

1. Per visualizzare l'output, usare il seguente comando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Viene visualizzato un elenco di parole contenute nel file **wasbs://example/data/gutenberg/davinci.txt** e il numero di occorrenze di ogni parola. Di seguito è riportato un esempio dei dati contenuti nel file:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Passaggi successivi

Come è possibile notare, i comandi Hadoop forniscono un modo semplice per eseguire processi MapReduce in un cluster HDInsight e visualizzare l'output del processo. Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare MapReduce in HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)
