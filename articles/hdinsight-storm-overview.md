<properties 
	pageTitle="Informazioni su Apache Storm in HDInsight (hadoop)" 
	description="Informazioni su come usare Apache Storm in HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Panoramica di Apache Storm in HDInsight

## Informazioni su Storm

[Apache Storm][apachestorm] è un sistema di calcolo distribuito, a tolleranza di errore e open source che consente di elaborare i dati in tempo reale. Le soluzioni Storm possono fornire anche l'elaborazione garantita dei dati che consente di ripetere l'operazione per i dati non elaborati correttamente la prima volta.

## Informazioni su Storm in HDInsight

Storm viene offerto come cluster gestito integrato nell'ambiente Azure, in cui può essere usato come componente di una soluzione Azure più completa. Ad esempio, Storm può usare i dati provenienti da servizi come le code del bus di servizio o l'hub eventi e usare Siti Web o Servizi cloud per fornire la visualizzazione dei dati. I cluster Storm possono anche essere configurati in una rete virtuale di Azure. Ciò riduce la latenza nella comunicazione con le altre risorse della stessa rete virtuale e può anche consentire una comunicazione protetta con le risorse all'interno di un data center privato.

Per iniziare a usare Storm, vedere [Introduzione a Storm in HDInsight][gettingstarted].

## Come vengono elaborati i dati in Storm in HDInsight?

Un cluster Storm elabora **topologie** al posto dei processi MapReduce con cui l'utente ha familiarità in HDInsight o Hadoop. Un cluster Storm contiene due tipi di nodi, i nodi head che eseguono **Nimbus** e i nodi di lavoro che eseguono **Supervisor**

* **Nimbus**: simile a JobTracker di Hadoop, è responsabile della distribuzione del codice nell'intero cluster, dell'assegnazione delle attività ai computer e al monitoraggio degli errori. HDInsight fornisce due nodi Nimbus, quindi non esiste un singolo punto di errore per un cluster Storm

* **Supervisor**: il supervisore di ciascun nodo di lavoro, responsabile di avviare e arrestare i **processi di lavoro** nel nodo.

* **Processo di lavoro**: un processo di lavoro esegue un subset di una **topologia**. Una topologia in esecuzione viene distribuita tra più processi di lavoro nel cluster.

* **Topologia**: definisce un grafico di calcolo che elabora i **flussi** di dati. A differenza dei processi MapReduce, le topologie restano in esecuzione finché non vengono arrestate

* **Flusso**: una raccolta non associata di **tuple**. I flussi vengono prodotti da **Spout** e **Bolt** e vengono usati da **Bolt**.

* **Tupla**: un elenco denominato di valori tipizzati dinamicamente.

* **Spout**: usa i dati dall'origine dati e genera uno o più **flussi**.

	> [AZURE.NOTE] In molti casi i dati vengono letti da una coda come Kafka, le code del bus di servizio di Azure o gli hub eventi. La coda assicura la persistenza dei dati in caso di interruzione.

* **Bolt**: usa i **flussi**, esegue l'elaborazione sulle **tuple** e può generare **flussi**. I Bolt sono anche responsabili della scrittura dei dati in una risorsa di archiviazione esterna, ad esempio una coda HDInsight HBase, un BLOB o altri archivi dati

* **Thrift**: Apache Thrift è un framework software per lo sviluppo di servizi scalabili per più linguaggi. Consente di creare servizi che funzionano con C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk e altri linguaggi.

	* **Nimbus** è un servizio Thrift e una **topologia** è una definizione Thrift, quindi è possibile sviluppare le topologie usando diversi linguaggi di programmazione 

Per altre informazioni sui componenti Storm, vedere l'[esercitazione su Storm][apachetutorial] nel sito Web apache.org.

## Scenari: quali sono i casi di utilizzo di Storm?

Di seguito sono descritti alcuni scenari comuni in cui è possibile usare Apache Storm. Per informazioni sugli scenari reali, leggere l'articolo che descrive [in che modo le società stanno usando Storm][poweredby].

### Analisi in tempo reale

Grazie all'elaborazione in tempo reale dei flussi di dati, Storm è la soluzione ideale per l'analisi dei dati che prevede la ricerca e la reazione a specifici eventi o modelli nei flussi di dati man mano che arrivano. Ad esempio, una topologia Storm può monitorare i dati del sensore per determinare l'integrità del sistema e generare messaggi SMS per avvisare l'utente quando si presenta un determinato modello.

### Extract Transform Load (ETL)

ETL può essere considerato quasi come un effetto collaterale dell'elaborazione Storm. Ad esempio, se si esegue il rilevamento di illeciti mediante l'analisi in tempo reale, i dati vengono già inseriti e trasformati. È anche opportuno avere un Bolt che archivi i dati in HBase, Hive o in altri archivi dati per le analisi future.

### RPC distribuito

L'RPC distribuito è un modello che è possibile creare mediante Storm. Una richiesta viene passata a Storm che distribuisce il calcolo in più nodi e infine restituisce un flusso di risultati al client in attesa.

Per altre informazioni su RPC distribuito e DRPCClient fornito con Storm, vedere [RPC distribuito](https://storm.incubator.apache.org/documentation/Distributed-RPC.html).

### Machine Learning online

Storm può essere usato con una soluzione Machine Learning sottoposta precedentemente a training con l'elaborazione batch, ad esempio una soluzione basata su Mahout. Tuttavia, il suo modello di calcolo distribuito generico consente anche soluzioni Machine Learning basate su flussi. Ad esempio, il progetto [Scalable Advanced Massive Online Analysis (SAMOA)][samoa] è una libreria Machine Learning che usa l'elaborazione del flusso e può funzionare con Storm.

## Quali linguaggi di programmazione si possono usare?

Il cluster Storm in HDInsight fornisce il supporto predefinito per .NET, Java e Python. Storm [supporta altri linguaggi](https://storm.incubator.apache.org/about/multi-language.html), ma la gran parte di essi richiedono l'installazione di un altro linguaggio di programmazione nel cluster HDInsight da aggiungere alle altre modifiche alla configurazione. 

### .NET

SCP è un progetto che consente agli sviluppatori .NET di progettare e implementare una topologia (con Spout e Bolt). Il supporto per SCP viene fornito per impostazione predefinita con i cluster Storm.

Per altre informazioni sullo sviluppo con SCP, vedere [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm in HDInsight](/it-it/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application).

### Java

Molti esempi Java illustrati sono di tipo Java normale o Trident. Trident è un'astrazione di alto livello che semplifica diverse operazioni, ad esempio join, aggregazioni, raggruppamento e filtraggio. Tuttavia, Trident si applica a batch di tuple, mentre una soluzione Java non elaborata consente l'elaborazione di un flusso una tupla alla volta.

Per altre informazioni su Trident, vedere l'[esercitazione su Trident](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) nel sito Web apache.org.

Per esempi sulle topologie Java non elaborate e Trident, vedere la directory **%storm_home%\contrib\storm-starter** nel cluster Storm.

## Quali sono alcuni dei modelli di sviluppo più comuni?

### Elaborazione garantita dei messaggi

Storm può fornire diversi livelli di elaborazione garantita dei messaggi. Ad esempio, un'applicazione Storm di base può garantire un'elaborazione at-least-once, mentre Trident può garantire un'elaborazione exactly-once.

Per altre informazioni, vedere la sezione sulle [garanzie relative all'elaborazione dati](https://storm.apache.org/about/guarantees-data-processing.html) nel sito Web apache.org

### BasicBolt

Il modello che prevede la lettura di una tupla di input, la generazione di nessuna o più tuple e l'inserimento della tupla di input subito dopo la fine del metodo di esecuzione è così frequente che Storm fornisce l'interfaccia [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) per automatizzarlo.

### Join

La creazione di un join di due flussi di dati varia a seconda delle applicazioni. Ad esempio, è possibile creare un join di ciascuna tupla da più flussi a un nuovo flusso singolo oppure è possibile creare un solo join di più batch di tuple per una specifica finestra. In entrambi i casi il join può essere eseguito usando [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), che rappresenta un metodo per definire in che modo le tuple vengono indirizzate ai Bolt.

Nel seguente esempio Java, fieldsGrouping viene usato per indirizzare le tuple originate dai componenti "1", "2" e "3" al Bolt **MyJoiner**.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

### Creazione di batch

La creazione di batch può essere eseguita in diversi modi. Con una topologia Storm Java di base è possibile usare un contatore semplice per creare un batch con un numero X di tuple prima di emetterle oppure usare un meccanismo a tempo interno noto come tupla tick per generare un batch ogni X secondi.

Per un esempio relativo all'uso di tuple tick, vedere [Analisi dei dati dei sensori con Storm e HDInsight](/it-it/documentation/articles/hdinsight-storm-sensor-data-analysis.md)

Trident invece si basa sull'elaborazione di batch di tuple.

### Memorizzazione nella cache

La memorizzazione nella cache viene spesso usata come meccanismo per velocizzare l'elaborazione perché conserva le risorse usate di frequente in memoria. Poiché una topologia viene distribuita in più nodi e in più processi in ogni nodo, è opportuno usare [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) per assicurarsi che le tuple che contengono i campi usati per la ricerca nella cache vengano sempre indirizzate allo stesso processo. Ciò consente di evitare la duplicazione delle voci della cache nei processi.

### Streaming dei primi N elementi

Quando la topologia dipende dal calcolo di un valore  'top' N, ad esempio le prime cinque tendenze su Twitter, è necessario calcolare il valore "primi N" in parallelo, quindi unire l'output dei calcoli in un valore globale. A questo scopo usare [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) per l'indirizzamento in base al campo al Bolt parallelo che esegue il partizionamento dei dati in base al valore del campo e infine eseguire l'indirizzamento a un Bolt che determina globalmente il valore "primi N".

Per un esempio, vedere [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## Passaggi successivi

* [Introduzione a Storm in HDInsight][gettingstarted]

* [Analisi dei dati dei sensori con Storm e HDInsight](/it-it/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm in HDInsight](/it-it/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[poweredby]: https://storm.incubator.apache.org/documentation/Powered-By.html
[gettingstarted]: /it-it/documentation/articles/hdinsight-storm-getting-started

<!--HONumber=42-->
