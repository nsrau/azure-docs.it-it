---
title: Eseguire esempi di MapReduce in HDInsight | Documentazione Microsoft
description: Introduzione all&quot;uso di esempi MapReduce con HDInsight. Usare SSH per connettersi al cluster, quindi usare il comando di Hadoop per eseguire i processi di esempio.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: d94e633273ef298079673c100c6edbf95dc3c96d
ms.lasthandoff: 03/25/2017


---
# <a name="run-the-hadoop-samples-in-hdinsight"></a>Eseguire gli esempi di Hadoop in HDInsight
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

I cluster HDInsight forniscono un set di esempi MapReduce che è possibile usare per acquisire familiarità con l'esecuzione di processi Hadoop MapReduce. Questo documento descrive gli esempi disponibili e illustra l'esecuzione di alcuni di essi.

## <a name="prerequisites"></a>Prerequisiti


* **Un cluster HDInsight basato su Linux**: vedere [Introduzione all'uso di Hadoop con Hive in HDInsight in Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* **Un client SSH**: per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-samples"></a>Esempi
**Percorso**: gli esempi sono disponibili nel cluster HDInsight in **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Contenuto**: gli esempi seguenti sono contenuti in questo archivio:

* **aggregatewordcount**: programma di mapping/riduzione basato su aggregazione che conta le parole nei file di input.
* **aggregatewordhist**: programma di mapping/riduzione basato su aggregazione che calcola l'istogramma delle parole nei file di input.
* **bbp**: programma di mapping/riduzione che usa la formula Bailey-Borwein-Plouffe per calcolare le cifre esatte del pi greco.
* **dbcount**: processo di esempio che conta i log di pageview memorizzati in un database
* **distbbp**: programma di mapping /riduzione che usa una formula di tipo BBP per calcolare i bit esatti del pi greco.
* **grep**: programma di mapping /riduzione che conta le corrispondenze di un regex nell'input.
* **join**: processo che interessa un join su set di dati ordinati ed equamente partizionati.
* **multifilewc**: processo che conta le parole da più file.
* **pentomino**: programma di tassellatura di mapping/riduzione per trovare soluzioni ai problemi relativi ai pentamini.
* **pi**: programma di mapping/riduzione che stima il pi greco usando un metodo simile a Monte Carlo.
* **randomtextwriter**: programma di mapping/riduzione che scrive 10 GB di dati testuali causali per nodo.
* **randomtextwriter**: programma di mapping/riduzione che scrive 10 GB di dati testuali casuali per nodo.
* **secondarysort**: esempio che definisce un ordinamento secondario per la riduzione.
* **sort**: programma di mapping/riduzione che ordina i dati scritti dal writer casuale.
* **sudoku**: risolutore di sudoku.
* **teragen**: genera dati per terasort.
* **terasort**: esegue il processo terasort.
* **teravalidate**: verifica i risultati di terasort.
* **wordcount**: programma di mapping/riduzione che conta le parole nei file di input.
* **wordcount**: programma di mapping/riduzione che conta la lunghezza media delle parole nei file di input.
* **wordmedian**: programma di mapping/riduzione che conta la lunghezza mediana delle parole nei file di input.
* **wordstandarddeviation**: programma di mapping/riduzione che conta la deviazione standard della lunghezza delle parole nei file di input.

**Codice sorgente**: il codice sorgente per questi esempi è incluso nel cluster HDInsight in **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [!NOTE]
> `2.2.4.9-1` è la versione di Hortonworks Data Platform per il cluster HDInsight e può cambiare quando si aggiorna HDInsight.
> 
> 

## <a name="how-to-run-the-samples"></a>Come eseguire gli esempi
1. Connettersi a HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Dal prompt di `username@#######:~$` usare il comando seguente per ottenere l'elenco degli esempi:
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
   
    Ciò consente di generare l'elenco degli esempi presenti nella sezione precedente di questo documento.
3. Usare il comando seguente per ottenere informazioni della guida su un esempio specifico. In questo caso, l'esempio **wordcount** :
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
   
    Dovrebbe essere visualizzato il messaggio seguente:
   
        Usage: wordcount <in> [<in>...] <out>
   
    Ciò indica che è possibile specificare alcuni percorsi di input per i documenti di origine. Il percorso finale corrisponde alla posizione in cui verrà memorizzato l'output (conteggio delle parole nei documenti di origine).
4. Usare il comando seguente per contare tutte le parole nei Codici di Leonardo da Vinci, forniti come dati di esempio con il cluster:
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
   
    L'input per questo processo viene letto da **wasbs:///example/data/gutenberg/davinci.txt**.
   
    L'output per questo esempio viene archiviato in **wasbs:///example/data/davinciwordcount**.
   
   > [!NOTE]
   > Come indicato nelle informazioni della guida per l'esempio wordcount, è anche possibile specificare più file di input. Ad esempio, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` eseguirebbe il conteggio delle parole in davinci.txt e in ulysses.txt.
   > 
   > 
5. Dopo il completamento del processo, usare il comando seguente per visualizzare l'output:
   
        hdfs dfs -cat /example/data/davinciwordcount/*
   
    Tutti i file di output prodotti dal processo sono concatenati e visualizzati. Per questo esempio di base viene usato un solo file, tuttavia se fossero specificati più file, il comando li scorrerebbe tutti.
   
    L'output è simile al seguente:
   
        zum     1
        zur     1
        zwanzig 1
        zweite  1
   
    Ogni riga rappresenta una parola e quante volte ricorre nei dati di input.

## <a name="sudoku"></a>sudoku
L'esempio Sudoku include istruzioni di utilizzo alquanto inutili, ovvero "includere un puzzle nella riga di comando".

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) è un rompicapo logico composto da griglie di 9 celle in formato 3x3. Alcune celle della griglia contengono numeri, altre sono vuote e l'obiettivo consiste nel risolvere le celle vuote. Il collegamento sopra riportato contiene altre informazioni sul puzzle, ma è lo scopo di questo esempio consiste nel risolvere le celle vuote. L'input dovrà essere un file nel formato seguente:

* Nove righe di nove colonne.
* Ogni colonna può contenere un numero o `?` (che indica una cella vuota)
* Le celle sono separate da uno spazio.

Esiste un modo certo per creare un puzzle Sudoku in base al quale non è possibile ripetere un numero in una colonna o una riga. Nel cluster di HDInsight è disponibile un esempio realizzato correttamente. Si trova in **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** e contiene quanto segue:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [!NOTE]
> `2.2.4.9-1` del percorso può variare quando il cluster HDInsight viene aggiornato.
> 
> 

Per eseguire questo esempio di Sudoku, usare il comando seguente:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Il risultato dovrebbe essere simile al seguente:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>Pi greco (π)
Per calcolare il valore di Pi greco, l'esempio usa un metodo statistico simile al metodo Monte Carlo. I punti posizionati in modo casuale all'interno di un quadrato unitario rientrano anche in un cerchio incluso in tale quadrato con una probabilità uguale all'area del cerchio, Pi greco/4. Il valore di Pi greco può essere stimato in base a valore di 4R, dove R indica il rapporto tra il numero di punti che si trovano all’interno del cerchio e il numero totale di punti che si trovano all’interno del quadrato. La precisione del calcolo è direttamente proporzionale al numero di punti utilizzati.

Il mapper di questo esempio genera un numero di punti casuali all'interno di un quadrato unitario e quindi conta il numero di punti all'interno del cerchio.

Il reducer accumula i punti contati dai mapper e quindi calcola il valore del pi greco in base alla formula 4R, dove R indica il rapporto tra il numero di punti contati all'interno del cerchio e il numero totale di punti all'interno del quadrato.

Usare il comando seguente per eseguire l'esempio. Usa 16 mappe con 10.000.000 di esempi, ognuno per il calcolo del pi greco:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

Il valore restituito dovrebbe essere simile a **3,14159155000000000000**. A scopo di riferimento, le prime 10 cifre decimali del pi greco sono 3,1415926535.

## <a name="10gb-greysort"></a>Greysort da 10 GB
GraySort è un ordinamento benchmark che usa come metrica la velocità di ordinamento (TB/minuto) ottenuta durante l'ordinamento di quantità di dati molto elevate, in genere almeno 100 TB.

In questo esempio vengono usati solo 10 GB di dati, in modo da consentire un'esecuzione relativamente rapida. Vengono usate le applicazioni MapReduce sviluppate da Owen O'Malley e Arun Murthy, vincitrici del benchmark annuale di ordinamento generico di terabyte ("daytona") nel 2009 con una velocità pari a 0,578 TB/min (100 TB in 173 minuti). Per ulteriori informazioni su questo e su altri benchmark di ordinamento, vedere il sito [Sortbenchmark](http://sortbenchmark.org/) .

In questo esempio vengono utilizzati tre set di programmi MapReduce:

* **TeraGen**: programma MapReduce che genera righe di dati da ordinare.
* **TeraSort**esegue il campionamento dei dati di input e usa MapReduce per organizzare i dati in un ordine totale.
  
    TeraSort è un ordinamento standard di funzioni MapReduce, ad eccezione di un partitioner personalizzato che usa un elenco ordinato di N-1 chiavi sottoposte a campionamento che definiscono l'intervallo di chiavi per ogni riduzione. In particolare, tutte le chiavi corrispondenti al criterio sample[i-1]<= chiave < sample[i] vengono inviate alla funzione reduce i. Ciò garantisce che tutti gli output di reduce i siano inferiori all'output della funzione reduce i+1.
* **TeraValidate**: un programma MapReduce che convalida l'ordinamento globale dell'output
  
    Crea una funzione map per ogni file nella directory di output e ogni funzione map assicura che ogni chiave sia inferiore o uguale alla precedente. La funzione map genera inoltre record della prima e dell'ultima chiave di ogni file, mentre la funzione reduce assicura che la prima chiave del file sia superiore all'ultima chiave di file i-1. Eventuali problemi vengono segnalati come output della funzione reduce insieme alle chiavi che non rispettano l'ordinamento.

Usare i passaggi seguenti per generare dati, ordinarli e quindi convalidare l'output:

1. Generare 10 GB di dati che verranno archiviati nell'archiviazione predefinita del cluster HDInsight in **wasbs:///example/data/10GB-sort-input**:
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
   
    `-Dmapred.map.tasks` indica ad Hadoop quante attività di mapping usare per questo processo. I due parametri finali indicano al processo di creare 10 GB di dati e di archiviarli in **wasbs:///example/data/10GB-sort-input**.
2. Eseguire il comando seguente per ordinare i dati:
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
   
    `-Dmapred.reduce.tasks` indica ad Hadoop quante attività di riduzione usare per questo processo. I due parametri finali sono solo i percorsi di input e output per i dati.
3. Per convalidare i dati generati dall'ordinamento, usare il comando seguente:
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come eseguire gli esempi inclusi nei cluster HDInsight basati su Linux. Per le esercitazioni sull'uso di Pig, Hive e MapReduce con HDInsight, vedere gli argomenti seguenti:

* [Usare Pig con Hadoop in HDInsight][hdinsight-use-pig]
* [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive]
* [Usare MapReduce con Hadoop in HDInsight][hdinsight-use-mapreduce]

[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

