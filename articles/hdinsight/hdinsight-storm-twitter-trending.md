<properties
   pageTitle="Temi di tendenza Twitter con Apache Storm in HDInsight | Microsoft Azure"
   description="Informazioni su come usare Trident per creare una topologia Apache Storm in grado di determinare i temi di tendenza in base agli hashtag Twitter."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Determinare i temi di tendenza Twitter con Apache Storm in HDInsight

Informazioni su come usare Trident per creare una topologia Storm in grado di determinare i temi di tendenza (hashtag) in Twitter.

Trident è un'astrazione generale che fornisce strumenti quali join, aggregazioni, raggruppamento, funzioni e filtri. Trident fornisce inoltre primitive per l'elaborazione incrementale e con informazioni sullo stato. Questo esempio illustra come creare una topologia usando uno spout e una funzione personalizzati, oltre a diverse funzioni predefinite fornite da Trident.

> [AZURE.NOTE]L'articolo si basa in larga misura sull'esempio [Trident Storm](https://github.com/jalonsoramos/trident-storm) di Juan Alonso.

##Requisiti

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java e JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Un account sviluppatore Twitter

##Scaricare il progetto

Usare il codice seguente per clonare il progetto in locale.

	git clone https://github.com/Blackmist/TwitterTrending

##Topologia

Di seguito è illustrata la topologia di questo esempio:

![topologia](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE]Si tratta di una visualizzazione semplificata della topologia. Tra i nodi del cluster verranno infatti distribuite più istanze dei componenti.

Il codice Trident che implementa la topologia è il seguente:

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

Il codice esegue le attività seguenti:

1. Creazione di un nuovo flusso dallo spout. Lo spout recupera tweet da Twitter filtrandoli in base a parole chiave specifiche. In questo esempio, si tratta delle parole amore, musica e caffè.

2. Uso della funzione personalizzata HashtagExtractor per estrarre gli hashtag da ciascun tweet. Gli hashtag vengono indirizzati al flusso.

3. Raggruppamento del flusso in base agli hashtag e passaggio a un aggregatore. L'aggregatore crea un conteggio delle occorrenze di ciascun hashtag, informazione che viene mantenuta in memoria. Al termine viene generato un nuovo flusso contenente l'hashtag e la relativa frequenza.

4. Ai fini di questo esempio, per un determinato batch di tweet vengono presi in considerazione solo gli hashtag più frequenti. Per restituire solo i primi 10 valori più frequenti in base al campo Count, viene applicato l'assembly **FirstN**.

> [AZURE.NOTE]A differenza dello spout e di HashtagExtractor, in questa sede viene usata una funzionalità di Trident predefinita.
>
> Per informazioni sulle operazioni predefinite, vedere l'articolo relativo a <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin</a>.
>
> Per le implementazioni Trident relative allo stato diverse da MemoryMapState, vedere i seguenti argomenti:
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">storm-trident-elasticsearch</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">trident-redis</a>

###Spout

Lo spout, **TwitterSpout**, usa <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> per recuperare tweet da Twitter. Viene creato un filtro (in questo esempio, amore, musica e caffè) e i tweet in ingresso (stato) che corrispondono al filtro vengono memorizzati in una coda di blocco collegata. Per altre informazioni, vedere <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">Classe LinkedBlockingQueue</a>. Al termine, gli elementi vengono rimossi dalla coda e inseriti nella topologia.

###HashtagExtractor

Per estrarre gli hashtag, usare <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> per recuperare tutti gli hashtag contenuti nel tweet. Gli hashtag vengono quindi indirizzati nel flusso.

##Abilitare Twitter

Per registrare una nuova applicazione Twitter e ottenere informazioni sull'utente e sul token di accesso necessarie per leggere da Twitter, effettuare le operazioni seguenti:

1. Passare a <a href="https://apps.twitter.com" target="_blank">Twitter Apps</a> e quindi fare clic sul pulsante **Create new app**. Quando si compila il modulo, lasciare il campo **Callback URL** vuoto.

2. Quando l'app viene creata, fare clic sulla scheda **Keys and Access Tokens**.

3. Copiare le informazioni **Consumer Key** e **Consumer Secret**.

4. Se non è disponibile alcun token, selezionare **Create my access token** nella parte inferiore della pagina. Dopo avere creato i token, copiare le informazioni **Access Token** e **Access Token Secret**.

5. Nel progetto **TwitterSpoutTopology** clonato in precedenza aprire il file **resources/twitter4j.properties**, aggiungere le informazioni copiate nei passaggi precedenti e salvare il file.

##Creare la topologia

Per creare il progetto, usare il codice seguente.

		cd [directoryname]
		mvn compile

##Testare la topologia

Per testare la topologia in locale, usare il comando seguente.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Dopo l'avvio della topologia, vengono visualizzate informazioni di debug contenenti gli hashtag e i conteggi generati dalla topologia stessa. L'output sarà simile al seguente:

	DEBUG: [Quicktellervalentine, 7]
	DEBUG: [GRAMMYs, 7]
	DEBUG: [AskSam, 7]
	DEBUG: [poppunk, 1]
	DEBUG: [rock, 1]
	DEBUG: [punkrock, 1]
	DEBUG: [band, 1]
	DEBUG: [punk, 1]
	DEBUG: [indonesiapunkrock, 1]

##Passaggi successivi

Dopo aver eseguito il test della topologia in locale, è possibile apprendere come distribuire la topologia. Vedere l'articolo [Distribuire e gestire topologie Apache Storm in HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Altri argomenti di interesse:

* [Sviluppare topologie Java per Storm in HDInsight con Maven](hdinsight-storm-develop-java-topology.md)

* [Sviluppare topologie C# per Storm in HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Per altri esempi su Storm per HDinsight:

* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=Sept15_HO4-->