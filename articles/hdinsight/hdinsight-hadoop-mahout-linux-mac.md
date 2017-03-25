---
title: Generare raccomandazioni con Mahout e HDInsight (SSH) | Documentazione Microsoft
description: Informazioni su come usare la libreria di Machine Learning Apache Mahout per generare raccomandazioni di film con HDInsight (Hadoop).
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: 163bf5b8d2884f678f7fea2207055eeb78b4e8ba
ms.lasthandoff: 02/21/2017


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>Generare raccomandazioni di film tramite Apache Mahout con Hadoop basato su Linux in HDInsight (SSH)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Informazioni su come usare la libreria di Machine Learning [Apache Mahout](http://mahout.apache.org) con Azure HDInsight per generare raccomandazioni di film.

Mahout è una libreria di [apprendimento automatico][ml] per Apache Hadoop. Mahout contiene gli algoritmi per l'elaborazione dei dati, ad esempio applicazione di filtri, classificazione e clustering. In questo articolo si userà un motore di raccomandazione per generare consigli cinematografici in base ai film visti dai propri amici.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster HDInsight basato su Linux. Per informazioni su come crearne uno, vedere [Introduzione all'uso di Hadoop basato su Linux in HDInsight][getstarted].

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="mahout-versioning"></a>Controllo delle versioni di Mahout

Per altre informazioni sulla versione di Mahout in HDInsight, vedere l'articolo relativo a [versioni di HDInsight e componenti di Hadoop](hdinsight-component-versioning.md).

## <a name="recommendations"></a>Informazioni sulle raccomandazioni

Una delle funzioni fornite da Mahout è un motore di raccomandazione. Questo motore accetta i dati nei formati `userID`, `itemId` e `prefValue` (la preferenza per l'elemento). Mahout può quindi eseguire l'analisi delle co-occorrenze per determinare che gli *utenti con una preferenza per un elemento hanno anche una preferenza per altri elementi*. Mahout determina quindi gli utenti con preferenze di elementi simili, che possono essere usate per le raccomandazioni.

Il flusso di lavoro seguente costituisce un esempio semplificato che usa dati relativi ai film:

* **Co-occorrenza**: a Joe, Alice e Bob piacciono *Guerre stellari*, *L'Impero colpisce ancora* e *Il ritorno dello Jedi*. Mahout determina che agli utenti a cui piace uno di questi film piacciono anche gli altri due.

* **Co-occorrenza**: a Bob e Alice piacciono anche *La minaccia fantasma*, *L'attacco dei cloni* e *La vendetta dei Sith*. Mahout determina che agli utenti a cui piacciono i tre film precedenti piacciono anche questi tre.

* **Raccomandazione per somiglianza**: poiché a Joe piacciono i primi tre film, Mahout cerca i film che piacciono ad altri utenti con preferenze simili ma che Joe non ha guardato o per i quali non ha ancora espresso una preferenza o una valutazione. In questo caso, Mahout raccomanda *La minaccia fantasma*, *L'attacco dei cloni* e *La vendetta dei Sith*.

### <a name="understanding-the-data"></a>Informazioni sui dati

[GroupLens Research][movielens] fornisce i dati di classificazione dei film in un formato compatibile con Mahout. Questi dati sono disponibili nello spazio di archiviazione predefinito del cluster in `/HdiSamples/HdiSamples/MahoutMovieData`.

Sono disponibili due file: `moviedb.txt` e `user-ratings.txt`. Il file user-ratings.txt viene usato durante l'analisi, mentre moviedb.txt viene usato per offrire informazioni di testo descrittive quando si visualizzano i risultati dell'analisi.

I dati contenuti in user-ratings.txt presentano una struttura `userID`, `movieID`, `userRating` e `timestamp` che indica come ogni utente ha classificato un film. Di seguito è riportato un esempio dei dati:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Eseguire l'analisi

Da una connessione SSH al cluster usare questi comandi per eseguire il processo di raccomandazione:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> Il completamento del processo potrebbe richiedere alcuni minuti ed è possibile eseguire più processi MapReduce.

## <a name="view-the-output"></a>Visualizzare l'output

1. Una volta completato il processo, usare il seguente comando per visualizzare l'output generato:
    
    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    L'output viene visualizzato come segue:
   
        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
   
    La prima colonna rappresenta il valore `userID`. I valori racchiusi tra "[" e "]" sono `movieId`:`recommendationScore`.

2. È possibile usare l'output, insieme a moviedb.txt, per fornire altre informazioni sulle raccomandazioni. È necessario innanzitutto copiare i file localmente usando i comandi seguenti:

   ```bash
   hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
   hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
   ```

    I dati di output vengono copiati da questo comando in un file denominato **recommendations.txt** nella directory corrente insieme ai file dei dati relativi ai film.

3. Usare il comando seguente per creare uno script Python che cerca i nomi dei film per i dati presenti nell'output delle raccomandazioni:

   ```bash
   nano show_recommendations.py
   ```

    All'apertura dell'editor usare il testo seguente come contenuto del file:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```
   
    Premere **CTRL-X**, **Y** e infine **INVIO** per salvare i dati.

4. Usare il comando seguente per rendere il file eseguibile:
   
   ```bash
   chmod +x show_recommendations.py
   ```

5. Eseguire lo script Python. Il comando seguente presuppone che l'utente si trovi nella directory in cui sono stati scaricati tutti i file:
   
   ```bash
   ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
   ```
    
    Questo comando esamina le raccomandazioni generate per l'ID utente 4.
   
   * Il file **user-ratings.txt** viene usato per recuperare i film che sono stati classificati.

   * Il file **moviedb.txt** viene usato per recuperare i nomi dei film.

   * Il file **recommendations.txt** viene usato per recuperare le raccomandazioni di film per questo utente.
     
     L'output di questo comando è simile al testo seguente:
     
     ```
       Reading Movies Descriptions
       Reading Rated Movies
       Reading Recommendations
     
     ##   Rated Movies
       Mimic (1997), rating=3
       Ulee's Gold (1997), rating=5
       Incognito (1997), rating=5
       One Flew Over the Cuckoo's Nest (1975), rating=4
       Event Horizon (1997), rating=4
       Client, The (1994), rating=3
       Liar Liar (1997), rating=5
       Scream (1996), rating=4
       Star Wars (1977), rating=5
       Wedding Singer, The (1998), rating=5
       Starship Troopers (1997), rating=4
       Air Force One (1997), rating=5
       Conspiracy Theory (1997), rating=3
       Contact (1997), rating=5
       Indiana Jones and the Last Crusade (1989), rating=3
       Desperate Measures (1998), rating=5
       Seven (Se7en) (1995), rating=4
       Cop Land (1997), rating=5
       Lost Highway (1997), rating=5
       Assignment, The (1997), rating=5
       Blues Brothers 2000 (1998), rating=5
       Spawn (1997), rating=2
       Wonderland (1997), rating=5
     
     ##   In & Out (1997), rating=5
     ##   Recommended Movies
       Seven Years in Tibet (1997), score=5.0
       Indiana Jones and the Last Crusade (1989), score=5.0
       Jaws (1975), score=5.0
       Sense and Sensibility (1995), score=5.0
       Independence Day (ID4) (1996), score=5.0
       My Best Friend's Wedding (1997), score=5.0
       Jerry Maguire (1996), score=5.0
       Scream 2 (1997), score=5.0
       Time to Kill, A (1996), score=5.0
     
     ##   Rock, The (1996), score=5.0
     ```

## <a name="delete-temporary-data"></a>Eliminare i dati temporanei

I processi Mahout non rimuovono i dati temporanei creati durante l'elaborazione del processo. Nel processo di esempio è specificato il parametro `--tempDir` per isolare i file temporanei in un percorso specifico per semplificarne l'eliminazione. Per rimuovere i file temporanei, usare il comando seguente:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> Se si vuole eseguire nuovamente il comando, è inoltre necessario eliminare la directory di output. Per eliminare la directory, usare il codice seguente:
> 
> ```hdfs dfs -rm -f -r /example/data/mahoutout```


## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come usare Mahout, trovare altri modi per usare i dati in HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Pig con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools


