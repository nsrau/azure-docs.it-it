---
title: Eseguire esempi di Apache Hadoop MapReduce in HDInsight - Azure
description: Introduzione all'uso di esempi di MapReduce in file JAR inclusi in HDInsight. Usare SSH per connettersi al cluster e quindi usare il comando di Hadoop per eseguire i processi di esempio.
keywords: jar esempio di hadoop, jar esempi di hadoop, esempi di hadoop mapreduce, esempi di mapreduce
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5fd2d27533d725102a4c334f1e8a1abed6cd78cc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121895"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Eseguire gli esempi di MapReduce inclusi in HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Informazioni su come eseguire esempi di MapReduce inclusi con Apache Hadoop su HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* **Un cluster HDInsight**: Vedere [Introduzione all'uso di Apache Hadoop con Apache Hive in HDInsight su Linux](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]  
    > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Un client SSH**: Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Gli esempi di MapReduce

**Posizione**: Gli esempi si trovano nel cluster HDInsight in `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Contents**: Gli esempi seguenti sono contenuti in questo archivio:

* `aggregatewordcount`: Un programma MapReduce basato su aggregazione che conta le parole nei file di input.
* `aggregatewordhist`: Un programma MapReduce basato su aggregazione che calcola l'istogramma delle parole nei file di input.
* `bbp`: Un programma MapReduce che usa la formula Bailey-Borwein-Plouffe per calcolare le cifre esatte del pi greco.
* `dbcount`: Un processo di esempio che conta i log di pageview archiviati in un database.
* `distbbp`: Un programma MapReduce che usa una formula di tipo BBP per calcolare i bit esatti del pi greco.
* `grep`: Un programma MapReduce che conta le corrispondenze di un regex nell'input.
* `join`: Un processo che esegue un join su set di dati ordinati ed equamente partizionati.
* `multifilewc`: Un processo che conta le parole da più file.
* `pentomino`: Un programma di tassellatura MapReduce per trovare soluzioni ai problemi relativi ai pentamini.
* `pi`: Un programma MapReduce che stima il pi greco usando un metodo simile al metodo Monte Carlo.
* `randomtextwriter`: Un programma MapReduce che scrive 10 GB di dati testuali casuali per nodo.
* `randomwriter`: Un programma MapReduce che scrive 10 GB di dati casuali per nodo.
* `secondarysort`: Un esempio che definisce un ordinamento secondario per la fase di riduzione.
* `sort`: Un programma MapReduce che ordina i dati scritti dal writer casuale.
* `sudoku`: Un risolutore di sudoku.
* `teragen`: Generare dati per terasort.
* `terasort`: Eseguire il processo terasort.
* `teravalidate`: Verificare i risultati di terasort.
* `wordcount`: Un programma MapReduce che conta le parole nei file di input.
* `wordmean`: Un programma MapReduce che conta la lunghezza media delle parole nei file di input.
* `wordmedian`: Un programma MapReduce che conta la lunghezza mediana delle parole nei file di input.
* `wordstandarddeviation`: Un programma MapReduce che conta la deviazione standard della lunghezza delle parole nei file di input.

**Codice sorgente**: Il codice sorgente per questi esempi è incluso nel cluster HDInsight in `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Eseguire l'esempio wordcount

1. Connettersi a HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Dal prompt di `username@#######:~$` usare il comando seguente per ottenere l'elenco degli esempi:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Tale comando consente di generare l'elenco degli esempi presenti nella sezione precedente del documento.

3. Usare il comando seguente per ottenere informazioni della guida su un esempio specifico. In questo caso, l'esempio **wordcount** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Viene visualizzato il messaggio seguente:

        Usage: wordcount <in> [<in>...] <out>

    Tale messaggio indica che è possibile specificare più percorsi di input per i documenti di origine. Il percorso finale corrisponde alla posizione in cui verrà memorizzato l'output (conteggio delle parole nei documenti di origine).

4. Usare il comando seguente per contare tutte le parole nei Codici di Leonardo da Vinci, forniti come dati di esempio con il cluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    L'input per questo processo viene letto da `/example/data/gutenberg/davinci.txt`. L'output per questo esempio viene archiviato in `/example/data/davinciwordcount`. Entrambi i percorsi si trovano nella risorsa di archiviazione predefinita per il cluster, non nel file system locale.

   > [!NOTE]  
   > Come indicato nelle informazioni della guida per l'esempio wordcount, è anche possibile specificare più file di input. Ad esempio, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` eseguirebbe il conteggio delle parole in davinci.txt e in ulysses.txt.

5. Dopo il completamento del processo, usare il comando seguente per visualizzare l'output:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Il comando esegue la concatenazione di tutti i file di output prodotti dal processo. In seguito visualizza l'output nella console. L'output è simile al testo seguente:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Ogni riga rappresenta una parola e quante volte ricorre nei dati di input.

## <a name="the-sudoku-example"></a>L'esempio di Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) è un rompicapo logico composto da griglie di 9 celle in formato 3x3. Alcune celle della griglia contengono numeri, altre sono vuote e l'obiettivo consiste nel risolvere le celle vuote. Il collegamento precedente contiene altre informazioni sul puzzle, ma lo scopo di questo esempio consiste nel risolvere le celle vuote. L'input dovrà essere un file nel formato seguente:

* Nove righe di nove colonne.
* Ogni colonna può contenere un numero o `?` (che indica una cella vuota)
* Le celle sono separate da uno spazio.

Esiste un modo certo per creare un puzzle Sudoku in base al quale non è possibile ripetere un numero in una colonna o una riga. Nel cluster di HDInsight è disponibile un esempio realizzato correttamente. Si trova in `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` e contiene il testo seguente:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Per eseguire questo esempio di Sudoku, usare il comando seguente:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Il risultato è simile al testo seguente:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Esempio di Pi greco (π)

Per calcolare il valore di Pi greco, l'esempio usa un metodo statistico simile al metodo Monte Carlo. I punti vengono posizionati in modo casuale in un quadrato unitario. Il quadrato contiene anche un cerchio. La probabilità che i punti siano compresi nel cerchio sono uguali all'area del cerchio, pi greco/4. Il valore di pi greco può essere stimato dal valore di 4R. R indica il rapporto tra il numero di punti che si trovano all'interno del cerchio e il numero totale di punti che si trovano all'interno del quadrato. La precisione del calcolo è direttamente proporzionale al numero di punti utilizzati.

Usare il comando seguente per eseguire l'esempio. Il comando usa 16 mappe con 10.000.000 di esempi, ognuno per il calcolo del pi greco:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Il valore restituito da questo comando è simile a **3,14159155000000000000**. A scopo di riferimento, le prime 10 cifre decimali del pi greco sono 3,1415926535.

## <a name="10-gb-greysort-example"></a>Esempio di Greysort 10 GB

GraySort è un ordinamento benchmark. La metrica è la velocità di ordinamento (TB/minuto) ottenuta durante l'ordinamento di quantità di dati elevate, in genere almeno 100 TB.

In questo esempio vengono usati solo 10 GB di dati, in modo da consentire un'esecuzione relativamente rapida. Usa le applicazioni di MapReduce sviluppate da Owen O'Malley e Arun Murthy. Queste applicazioni sono vincitrici del benchmark annuale di ordinamento generico di terabyte ("daytona") nel 2009 con una velocità pari a 0,578 TB/min (100 TB in 173 minuti). Per ulteriori informazioni su questo e su altri benchmark di ordinamento, vedere il sito [Sortbenchmark](https://sortbenchmark.org/) .

In questo esempio vengono utilizzati tre set di programmi MapReduce:

* **TeraGen**: Un programma MapReduce che genera righe di dati da ordinare

* **TeraSort**: Esegue il campionamento dei dati di input e usa MapReduce per organizzare i dati in un ordine totale

    TeraSort è un ordinamento standard di MapReduce, ad eccezione di un partitioner personalizzato. Il partitioner usa un elenco ordinato di chiavi campionate N-1 che definiscono l'intervallo di chiavi per ogni riduzione. In particolare, tutte le chiavi corrispondenti al criterio sample[i-1]<= chiave < sample[i] vengono inviate alla funzione reduce i. Il partitioner garantisce che tutti gli output di reduce i siano inferiori all'output della funzione reduce i+1.

* **TeraValidate**: Un programma MapReduce che convalida l'ordinamento globale dell'output

    Crea una funzione map per ogni file nella directory di output e ogni funzione map assicura che ogni chiave sia inferiore o uguale alla precedente. La funzione map genera i record delle prime e delle ultime chiavi di ogni file. La funzione reduce assicura che la prima chiave del file i sia maggiore dell'ultima chiave del file i-1. Eventuali problemi vengono segnalati come output della fase reduce insieme alle chiavi che non rispettano l'ordinamento.

Usare i passaggi seguenti per generare dati, ordinarli e quindi convalidare l'output:

1. Generare 10 GB di dati, che vengono archiviati nella risorsa di archiviazione predefinita del cluster HDInsight in `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` indica ad Hadoop quante attività di mapping usare per questo processo. I due parametri finali indicano al processo di creare 10 GB di dati e di archiviarli in `/example/data/10GB-sort-input`.

2. Eseguire il comando seguente per ordinare i dati:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` indica ad Hadoop quante attività di riduzione usare per questo processo. I due parametri finali sono solo i percorsi di input e output per i dati.

3. Per convalidare i dati generati dall'ordinamento, usare il comando seguente:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire gli esempi inclusi nei cluster HDInsight basati su Linux. Per le esercitazioni sull'uso di Pig, Hive e MapReduce con HDInsight, vedere gli argomenti seguenti:

* [Usare Apache Pig con Apache Hadoop su HDInsight](hdinsight-use-pig.md)
* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md

