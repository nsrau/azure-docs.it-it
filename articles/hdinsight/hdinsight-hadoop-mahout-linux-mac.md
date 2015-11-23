<properties
	pageTitle="Generare raccomandazioni utilizzando HDInsight basate su Mahout e Linux | Microsoft Azure"
	description="Informazioni su come usare la libreria di Machine Learning Apache Mahout per generare raccomandazioni di film con HDInsight basato su Linux (Hadoop)."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/06/2015"
	ms.author="larryfr"/>

#Generare raccomandazioni di film mediante Apache Mahout con Hadoop basato su Linux in HDInsight (anteprima)

[AZURE.INCLUDE [selettore di mahout](../../includes/hdinsight-selector-mahout.md)]

Informazioni su come usare la libreria di Machine Learning [Apache Mahout](http://mahout.apache.org) con Azure HDInsight per generare raccomandazioni di film.

Mahout è una libreria di [Machine Learning][ml] per Apache Hadoop. Mahout contiene gli algoritmi per l'elaborazione dei dati, ad esempio applicazione di filtri, classificazione e clustering. In questo articolo si userà un motore di raccomandazione per generare raccomandazioni di film in base ai film visti dai propri amici.

> [AZURE.NOTE]I passaggi descritti in questo documento richiedono un cluster Hadoop basato su Linux in HDInsight (anteprima). Per informazioni sull'uso di Mahout con un cluster basato su Windows, vedere [Generare raccomandazioni di film mediante Apache Mahout con Hadoop basato su Windows in HDInsight](hdinsight-mahout.md)

##Prerequisiti

* Un cluster Hadoop basato su Linux in HDInsight. Per informazioni su come crearne uno, vedere [Introduzione all'utilizzo di Hadoop basato su Linux in HDInsight][getstarted].

##Controllo delle versioni di Mahout

Per ulteriori informazioni sulla versione di Mahout inclusa con il cluster HDInsight, vedere [Versioni HDInsight e componenti Hadoop](hdinsight-component-versioning.md).

> [AZURE.WARNING]Sebbene sia possibile caricare una versione diversa di Mahout nel cluster HDInsight, solo i componenti forniti con il cluster HDInsight sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community come: [il forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Per i progetti Apache sono inoltre disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

##<a name="recommendations"></a>Informazioni sulle raccomandazioni

Una delle funzioni fornite da Mahout è un motore di raccomandazione. Questo motore accetta i dati nei formati `userID`, `itemId` e `prefValue` (la preferenza degli utenti per l'elemento). Mahout può quindi eseguire l'analisi delle co-occorrenze per determinare che gli _utenti con una preferenza per un elemento hanno anche una preferenza per altri elementi_. Mahout determina quindi gli utenti con preferenze di elementi simili, che possono essere usate per le raccomandazioni.

Di seguito è riportato un esempio molto semplice relativo ai film:

* __Co-occorrenza__: a Joe, Alice e Bob piacciono _Guerre stellari_, _L'Impero colpisce ancora_ e _Il ritorno dello Jedi_. Mahout determina che agli utenti a cui piace uno di questi film piacciono anche gli altri due.

* __Co-occorrenza__: a Bob e Alice piacciono anche _La minaccia fantasma_, _L'attacco dei cloni_ e _La vendetta dei Sith_. Mahout determina che agli utenti a cui piacciono i tre film precedenti piacciono anche questi tre.

* __Raccomandazione per somiglianza__: poiché a Joe piacciono i primi tre film, Mahout cerca i film che piacciono ad altri utenti con preferenze simili ma che Joe non ha guardato o per i quali non ha ancora espresso una preferenza o una valutazione. In questo caso, Mahout raccomanda _La minaccia fantasma_, _L'attacco dei cloni_ e _La vendetta dei Sith_.

##Caricare i dati

[GroupLens Research][movielens] fornisce i dati di classificazione dei film in un formato compatibile con Mahout. Attenersi alla seguente procedura per scaricare i dati, quindi caricarli nella memoria predefinita per il cluster:

1. Usare SSH per connettersi al cluster HDInsight basato su Linux. L'indirizzo da usare durante la connessione è `CLUSTERNAME-ssh.azurehdinsight.net` e la porta è `22`.

	Per ulteriori informazioni sull'utilizzo di SSH per connettersi a HDInsight, vedere i seguenti documenti:

    * **Client Linux, Unix o OS X**: vedere [Connettersi a un cluster HDInsight basato su Linux, Unix e OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Client Windows**: vedere [Connettersi a un cluster HDInsight basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

2. Scaricare l'archivio MovieLens 100k, che contiene 100.000 classificazioni di 1000 utenti per 1700 film.

        curl -O http://files.grouplens.org/datasets/movielens/ml-100k.zip

3. Estrarre l'archivio tramite il comando seguente:

        unzip ml-100k.zip

    Verrà estratto il contenuto in una nuova cartella denominata **ml-100k**.

4. Usare i comandi seguenti per copiare i dati nella memoria di HDInsight:

        cd ml-100k
        hdfs dfs -put u.data /example/data


    I dati contenuti in questo file presentano una struttura di `userID`, `movieID`, `userRating` e `timestamp`, che indica come ogni utente ha classificato un film. Di seguito è riportato un esempio dei dati:


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596

##Eseguire il processo

Per eseguire il processo di raccomandazione, usare il comando seguente:

	mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /example/data/u.data -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE]Il completamento del processo potrebbe richiedere alcuni minuti ed è possibile eseguire più processi MapReduce.

##Visualizzare l'output

1. Una volta completato il processo, usare il seguente comando per visualizzare l'output generato:

		hdfs dfs -text /example/data/mahoutout/part-r-00000

	L'output verrà visualizzato come segue:

		1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
		2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
		3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
		4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

	La prima colonna rappresenta il valore `userID`. I valori racchiusi tra '[' e ']' rappresentano `movieId`:`recommendationScore`.

2. Alcuni altri dati contenuti nella directory **ml-100k** possono essere usati per rendere i dati più descrittivi. Innanzitutto, scaricare i dati usando il comando seguente:

		hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt

	I dati di output verranno copiati in un file denominato **recommendations.txt** nella directory corrente.

3. Usare il comando seguente per creare un nuovo script Python che cercherà i nomi dei film per i dati presenti nell'output delle raccomandazioni:

		nano show_recommendations.py

	All’apertura dell’editor, usare il codice seguente come contenuto del file:

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

	Premere **Ctrl-X**, **Y** e infine **INVIO** per salvare i dati.

3. Usare il comando seguente per rendere il file eseguibile:

		chmod +x show_recommendations.py

4. Eseguire lo script Python. Nell'esempio seguente si presuppone di trovarsi nella directory ml-100k, dove si trovano i file `u.data` e `u.item`:

		./show_recommendations.py 4 u.data u.item recommendations.txt

	Verranno esaminate le raccomandazioni generate per l'ID utente 4.

	* Il file **u.data** viene usato per recuperare i film classificati dall'utente
	* Il **u.item** file viene usato per recuperare i nomi dei film
	* Il file **recommendations.txt** viene usato per recuperare le raccomandazioni sui film per questo utente

	L'output di questo comando sarà simile al seguente:

		Reading Movies Descriptions
		Reading Rated Movies
		Reading Recommendations
		Rated Movies
		------------------------
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
		In & Out (1997), rating=5
		------------------------
		Recommended Movies
		------------------------
		Seven Years in Tibet (1997), score=5.0
		Indiana Jones and the Last Crusade (1989), score=5.0
		Jaws (1975), score=5.0
		Sense and Sensibility (1995), score=5.0
		Independence Day (ID4) (1996), score=5.0
		My Best Friend's Wedding (1997), score=5.0
		Jerry Maguire (1996), score=5.0
		Scream 2 (1997), score=5.0
		Time to Kill, A (1996), score=5.0
		Rock, The (1996), score=5.0
		------------------------

##Eliminare i dati temporanei

I processi Mahout non rimuovono i dati temporanei creati durante l'elaborazione del processo. Nel processo di esempio è specificato il parametro `--tempDir` per isolare i file temporanei in un percorso specifico per semplificarne l'eliminazione. Per rimuovere i file temporanei, usare il comando seguente:

	hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING]Se si vuole eseguire nuovamente il comando, è inoltre necessario eliminare la directory di output. Per eliminare la directory, usare il codice seguente:
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

##Passaggi successivi

A questo punto, dopo aver appreso come usare Mahout, trovare altri modi per usare i dati in HDInsight:

* [Hive con HDInsight](../hadoop-use-hive.md)
* [Pig con HDInsight](../hadoop-use-pig.md)
* [MapReduce con HDInsight](../hadoop-use-mapreduce.md)

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
 

<!---HONumber=Nov15_HO3-->