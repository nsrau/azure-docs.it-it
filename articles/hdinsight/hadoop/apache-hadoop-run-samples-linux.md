---
title: Eseguire esempi di Apache Hadoop MapReduce in HDInsight - Azure
description: Introduzione all'uso di esempi di MapReduce in file JAR inclusi in HDInsight. Usare SSH per connettersi al cluster e quindi usare il comando di Hadoop per eseguire i processi di esempio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: cc0918f3d9739a214e682d7faa460b6cc519cb0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207833"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Eseguire gli esempi di MapReduce inclusi in HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Informazioni su come eseguire esempi di MapReduce inclusi con Apache Hadoop su HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Gli esempi di MapReduce

Gli esempi si trovano nel cluster HDInsight in `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`. Il codice sorgente per questi esempi è incluso nel cluster HDInsight in `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

Gli esempi seguenti sono contenuti in questo archivio:

|Esempio |Descrizione |
|---|---|
|aggregatewordcount|Conta le parole nei file di input.|
|aggregatewordhist|Calcola l'istogramma delle parole nei file di input.|
|BBP|USA Bailey-Borwein-Plouffe per calcolare le cifre esatte del pi greco.|
|DBCOUNT|Conta i log di visualizzazione archiviati in un database.|
|distbbp|Usa una formula di tipo BBP per calcolare i bit esatti del pi greco.|
|grep|Conta le corrispondenze di un'espressione regolare nell'input.|
|join|Esegue un join su set di impostazioni, equamente partizionati.|
|multifilewc|Conta le parole da più file.|
|Pentomino|Programma di disposizione dei riquadri per trovare soluzioni per Pentomino problemi.|
|pi|Stima pi greco usando un metodo quasi-Monte Carlo.|
|randomtextwriter|Scrive 10 GB di dati testuali casuali per nodo.|
|randomtextwriter|Scrive 10 GB di dati casuali per nodo.|
|secondarySort|Definisce un ordinamento secondario per la fase di riduzione.|
|sort|Ordina i dati scritti dal writer casuale.|
|Sudoku|Un risolutore di sudoku.|
|teragen|Generare dati per terasort.|
|terasort|Eseguire il processo terasort.|
|teravalidate|Verificare i risultati di terasort.|
|WordCount|Conta le parole nei file di input.|
|WordCount|Conta la lunghezza media delle parole nei file di input.|
|wordmedian|Conta la lunghezza mediana delle parole nei file di input.|
|wordstandarddeviation|Conta la deviazione standard della lunghezza delle parole nei file di input.|

## <a name="run-the-wordcount-example"></a>Eseguire l'esempio wordcount

1. Connettersi a HDInsight tramite SSH. Sostituire `CLUSTER` con il nome del cluster e quindi immettere il comando seguente:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Dalla sessione SSH usare il comando seguente per elencare gli esempi:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Tale comando consente di generare l'elenco degli esempi presenti nella sezione precedente del documento.

3. Usare il comando seguente per ottenere informazioni della guida su un esempio specifico. In questo caso, l'esempio **wordcount** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Viene visualizzato il messaggio seguente:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Tale messaggio indica che è possibile specificare più percorsi di input per i documenti di origine. Il percorso finale corrisponde alla posizione in cui verrà memorizzato l'output (conteggio delle parole nei documenti di origine).

4. Usare quanto segue per conteggiare tutte le parole nei notebook di Leonardo da Vinci, che vengono forniti come dati di esempio con il cluster:

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

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Ogni riga rappresenta una parola e quante volte ricorre nei dati di input.

## <a name="the-sudoku-example"></a>L'esempio di Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) è un rompicapo logico composto da griglie di 9 celle in formato 3x3. Alcune celle della griglia contengono numeri, altre sono vuote e l'obiettivo consiste nel risolvere le celle vuote. Il collegamento precedente contiene altre informazioni sul puzzle, ma lo scopo di questo esempio consiste nel risolvere le celle vuote. L'input dovrà essere un file nel formato seguente:

* Nove righe di nove colonne.
* Ogni colonna può contenere un numero o `?` (che indica una cella vuota)
* Le celle sono separate da uno spazio.

Esiste un modo certo per creare un puzzle Sudoku in base al quale non è possibile ripetere un numero in una colonna o una riga. Nel cluster di HDInsight è disponibile un esempio realizzato correttamente. Si trova in `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` e contiene il testo seguente:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Per eseguire questo esempio di Sudoku, usare il comando seguente:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Il risultato è simile al testo seguente:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>Esempio di Pi greco (π)

Per calcolare il valore di Pi greco, l'esempio usa un metodo statistico simile al metodo Monte Carlo. I punti vengono posizionati in modo casuale in un quadrato unitario. Il quadrato contiene anche un cerchio. La probabilità che i punti rientrino nel cerchio è uguale all'area del cerchio, pi greco/4. Il valore di pi greco può essere stimato dal valore di 4R. R indica il rapporto tra il numero di punti che si trovano all'interno del cerchio e il numero totale di punti che si trovano all'interno del quadrato. La precisione del calcolo è direttamente proporzionale al numero di punti utilizzati.

Usare il comando seguente per eseguire l'esempio. Il comando usa 16 mappe con 10.000.000 di esempi, ognuno per il calcolo del pi greco:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Il valore restituito da questo comando è simile a **3,14159155000000000000**. A scopo di riferimento, le prime 10 cifre decimali del pi greco sono 3,1415926535.

## <a name="10-gb-graysort-example"></a>esempio di GraySort da 10 GB

GraySort è un ordinamento benchmark. La metrica è la velocità di ordinamento (TB/minuto) ottenuta durante l'ordinamento di quantità di dati elevate, in genere almeno 100 TB.

In questo esempio vengono usati solo 10 GB di dati, in modo da consentire un'esecuzione relativamente rapida. Usa le applicazioni di MapReduce sviluppate da Owen O'Malley e Arun Murthy. Queste applicazioni hanno ottenuto il benchmark di ordinamento annuale per utilizzo generale ("Daytona") terabyte in 2009, con una frequenza di 0,578 TB/min (100 TB in 173 minuti). Per ulteriori informazioni su questo e altri benchmark di ordinamento, vedere il sito [benchmark di ordinamento](https://sortbenchmark.org/) .

In questo esempio vengono utilizzati tre set di programmi MapReduce:

* **TeraGen**: programma MapReduce che genera righe di dati da ordinare.

* **TeraSort**esegue il campionamento dei dati di input e usa MapReduce per organizzare i dati in un ordine totale.

    TeraSort è un ordinamento standard di MapReduce, ad eccezione di un partitioner personalizzato. Il partitioner usa un elenco ordinato di chiavi campionate N-1 che definiscono l'intervallo di chiavi per ogni riduzione. In particolare, tutte le chiavi corrispondenti al criterio sample[i-1]<= chiave < sample[i] vengono inviate alla funzione reduce i. Il partitioner garantisce che tutti gli output di reduce i siano inferiori all'output della funzione reduce i+1.

* **TeraValidate**: un programma MapReduce che convalida l'ordinamento globale dell'output

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

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)
