---
title: Sviluppare processi MapReduce per la creazione di flussi Python con HDInsight - Azure | Documentazione Microsoft
description: Informazioni su come usare Python nel flusso di processi MapReduce. Hadoop fornisce un'API di flusso per MapReduce per la scrittura in linguaggi diversi da Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 76cf2556f911948689c96deb1cbab350f2559817
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Sviluppare programmi MapReduce per la creazione di flussi Python per HDInsight

Informazioni su come usare Python nel flusso di operazioni MapReduce. In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. La procedura descritta in questo documento implementa i componenti di mapping e riduzione in Python.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Hadoop basato su Linux in HDInsight

  > [!IMPORTANT]
  > I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un editor di testo

  > [!IMPORTANT]
  > L'editor di testo deve usare LF come terminazione di riga. L'uso di una terminazione di riga di CRLF causerà errori in seguito all'esecuzione del processo MapReduce su cluster HDInsight basati su Linux.

* I comandi `ssh` e `scp` oppure [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Conteggio di parole

In questo esempio viene implementato un conteggio di parole di base in Python usando un mapper e un reducer. Il mapper scompone le frasi in singole parole e il reducer aggrega le parole e i conteggi per produrre l'output.

Il diagramma di flusso illustra ciò che accade durante le fasi di mapping e riduzione.

![illustrazione del processo MapReduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Flusso di MapReduce

Hadoop consente di specificare un file che contiene la logica di mapping e riduzione usata da un processo. I requisiti specifici per la logica di mapping e riduzione sono:

* **Input**: i componenti di mapping e riduzione devono leggere i dati di input da STDIN.
* **Output**: i componenti di mapping e riduzione devono scrivere i dati di output in STDOUT.
* **Formato dati**: i dati usati e prodotti devono essere una coppia chiave/valore, separati da un carattere di tabulazione.

Python è in grado di gestire facilmente questi requisiti usando il modulo `sys` per leggere da STDIN e il modulo `print` per stampare in STDOUT. Il resto dell'attività consiste semplicemente nella formattazione dei dati con un carattere di tabulazione (`\t`) tra la chiave e il valore.

## <a name="create-the-mapper-and-reducer"></a>Creare il mapper e il reducer

1. Creare un file denominato `mapper.py` e usare il codice seguente come contenuto:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Creare un file denominato **reducer.py** e usare il codice seguente come contenuto:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Esecuzione con PowerShell

Per garantire che i file abbiano le terminazioni di riga corrette, usare lo script di PowerShell seguente:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Usare lo script di PowerShell seguente per caricare i file, eseguire il processo e visualizzare l'output:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Eseguire da una sessione SSH

1. Nell'ambiente di sviluppo, usare il comando seguente dalla stessa directory dei file `mapper.py` e `reducer.py`:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Sostituire `username` con il nome utente SSH del cluster e `clustername` con il nome del cluster.

    Questo comando copia i file dal sistema locale nel nodo head.

    > [!NOTE]
    > Se è stata usata una password per proteggere l'account SSH, viene richiesto di specificarla. Se è stata usata una chiave SSH, potrebbe essere necessario usare il parametro `-i` e il percorso della chiave privata. Ad esempio, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Connettersi al cluster usando SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Per garantire che mapper.py e reducer.py abbiano le terminazioni di riga corrette, usare i comandi seguenti:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Usare il seguente comando per avviare il processo MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Questo comando include le seguenti parti:

   * **hadoop-streaming.jar**: usato durante l'esecuzione di operazioni di flusso MapReduce. Consente ad Hadoop di interagire con il codice MapReduce esterno fornito dall'utente.

   * **-file**: aggiunge i file specificati al processo MapReduce.

   * **-mapper**: indica ad Hadoop quale file usare come mapper.

   * **-reducer**: indica ad Hadoop quale file usare come reducer.

   * **-input**: il file di input di cui devono essere contate le parole.

   * **-output**: la directory in cui viene scritto l'output.

    Mentre il processo MapReduce è in esecuzione, il processo viene visualizzato sotto forma di valori percentuali.

        15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%


5. Per visualizzare l'output, usare il seguente comando:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Viene visualizzato un elenco di parole con l'indicazione del numero di occorrenze di ogni parola.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come usare i processi di flusso MapReduce con HDInsight, vedere i seguenti collegamenti per esplorare altri modi di uso di Azure HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)
