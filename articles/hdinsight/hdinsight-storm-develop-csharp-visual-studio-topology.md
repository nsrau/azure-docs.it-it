<properties
   pageTitle="Topologie di Apache Storm con Visual Studio e Visual C# | Microsoft Azure"
   description="Informazioni su come creare topologie Storm in C# mediante la creazione di una semplice topologia di conteggio parole in Visual Studio usando HDInsight Tools per Visual Studio."
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

# Sviluppare topologie C# per Apache Storm in HDInsight tramite gli strumenti Hadoop per Visual Studio

Informazioni su come creare una topologia Storm C# usando HDInsight Tools per Visual Studio. Questa esercitazione illustra il processo di creazione di un nuovo progetto Storm in Visual Studio, l'esecuzione del test in locale e la distribuzione in un cluster Apache Storm in HDInsight.

Verrà inoltre spiegato come creare topologie ibride che usano componenti C# e Java.

##Prerequisiti

-	Una delle seguenti versioni di Visual Studio

	-	Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

	-	Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

	-	Visual Studio 2015 o [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

-	Azure SDK 2.5.1 o versioni successive

-	HDInsight Tools per Visual Studio: per installare e configurare gli strumenti HDInsight per Visual Studio, vedere [Introduzione all'uso di HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

    > [AZURE.NOTE]Gli strumenti HDInsight per Visual Studio non sono supportati in Visual Studio Express

-	Apache Storm in cluster HDInsight: per la procedura relativa alla creazione di un cluster, vedere [Introduzione ad Apache Storm in HDInsight](hdinsight-storm-getting-started.md).

	> [AZURE.NOTE]Attualmente HDInsight Tools per Visual Studio supporta Storm solo su cluster HDInsight versione 3.2.

##Modelli

Gli strumenti HDInsight per Visual Studio includono i modelli seguenti:

| Tipo di progetto | Dimostra |
| ------------ | ------------- |
| Applicazione Storm | Un progetto di topologia Storm vuoto |
| Esempio di writer SQL di Azure per Storm | Come scrivere nel database SQL di Azure |
| Esempio di lettore DocumentDB per Storm | Come leggere da Azure DocumentDB |
| Esempio di writer DocumentDB per Storm | Come scrivere in Azure DocumentDB |
| Esempio di lettore EventHub per Storm | Come leggere da Hub eventi di Azure |
| Esempio di writer EventHub per Storm | Come scrivere in Hub eventi di Azure |
| Esempio di lettore HBase per Storm | Come leggere da cluster HBase in HDInsight |
| Esempio di writer HBase per Storm | Come scrivere in cluster HBase in HDInsight |
| Esempio ibrido di Storm | Come usare un componente Java |
| Esempio di Storm | Topologia di conteggio parole di base |

> [AZURE.NOTE]Gli esempi di lettore e writer HBase usano l'API REST HBase per comunicare con un cluster HBase in HDInsight e non l'API Java HBase.

Nei passaggi di questo documento, si userà il tipo di progetto di applicazione Storm di base per creare una nuova topologia.

##Creare una topologia C#

1.	Se la versione più recente di HDInsight Tools per Visual Studio non è ancora installata, vedere [Introduzione all'uso di HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2.	Aprire Visual Studio, selezionare **File** > **Nuovo** e quindi **Progetto**.

3.	Nella schermata **Nuovo progetto** espandere **Installato** > **Modelli** e quindi selezionare **HDInsight**. Dall'elenco dei modelli selezionare **Applicazione Storm**. Nella parte inferiore della schermata immettere **WordCount** come nome dell'applicazione.

	![immagine](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4.	Dopo aver creato il progetto, è necessario disporre dei seguenti file:

	-	**Program.cs**: definisce la topologia per il progetto. Per impostazione predefinita viene creata una topologia predefinita costituita da uno spout e da un bolt.

	-	**Spout.cs**: spout di esempio che genera numeri casuali.

	-	**Bolt.cs**: bolt di esempio che tiene conto dei numeri generati dallo spout.

	Come parte della creazione del progetto, verranno scaricati da NuGet i [pacchetti SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) più recenti.

Nelle sezioni successive il progetto verrà trasformato in un'applicazione WordCount di base.

###Implementare lo spout

1.	Aprire **Spout.cs**. Gli spout vengono usati per leggere i dati in una topologia da un'origine esterna. Di seguito sono indicati i componenti principali di uno spout:

	-	**NextTuple**: chiamato da Storm quando allo spout è consentita la generazione di nuove tuple.

	-	**Ack** (solo topologia transazionale): gestisce i messaggi di riconoscimento avviati da altri componenti della topologia, per tuple inviate dallo spout. Il riconoscimento di una tupla consente allo spout di sapere che tale tupla è stata elaborata correttamente dai componenti downstream.

	-	**Fail** (solo topologia transazionale): gestisce tuple la cui elaborazione da parte di altri componenti della topologia ha avuto esito negativo. Questo offre la possibilità di generare nuovamente la tupla, in modo che possa essere elaborata di nuovo.

2.	Sostituire il contenuto della classe **Spout** con quanto riportato di seguito. Verrà creato uno spout che genera in modo casuale una frase nella topologia.

	```
	private Context ctx;
	private Random r = new Random();
	string[] sentences = new string[] {
	    "the cow jumped over the moon",
	    "an apple a day keeps the doctor away",
	    "four score and seven years ago",
	    "snow white and the seven dwarfs",
	    "i am at two with nature"
	};


	public Spout(Context ctx)
	{
	    // Set the instance context
	    this.ctx = ctx;


	    Context.Logger.Info("Generator constructor called");


	    // Declare Output schema
	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // The schema for the default output stream is
	    // a tuple that contains a string field
	    outputSchema.Add("default", new List<Type>() { typeof(string) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
	}


	// Get an instance of the spout
	public static Spout Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Spout(ctx);
	}


	public void NextTuple(Dictionary<string, Object> parms)
	{
	    Context.Logger.Info("NextTuple enter");
	    // The sentence to be emitted
	    string sentence;


	    // Get a random sentence
	    sentence = sentences[r.Next(0, sentences.Length - 1)];
	    Context.Logger.Info("Emit: {0}", sentence);
	    // Emit it
	    this.ctx.Emit(new Values(sentence));


	    Context.Logger.Info("NextTuple exit");
	}


	public void Ack(long seqId, Dictionary<string, Object> parms)
	{
	    // Only used for transactional topologies
	}


	public void Fail(long seqId, Dictionary<string, Object> parms)
	{
	    // Only used for transactional topologies
	}
	```

	Si consiglia di leggere con attenzione i commenti per comprendere le finalità del codice.

###Implementare i bolt

1.	Eliminare dal progetto il file **Bolt.cs** esistente.

2.	In **Esplora soluzioni** fare clic con il pulsante destro del mouse e selezionare **Aggiungi** > **Nuovo elemento**. Selezionare **Storm Bolt** dall'elenco e immettere **Splitter.cs** come nome. Ripetere l'operazione per creare un secondo bolt denominato **Counter.cs**.

	-	**Splitter.cs**: implementa un bolt che divide le frasi in singole parole e crea un nuovo flusso di parole.

	-	**Counter.cs**: implementa un bolt che conta ogni parola e genera un nuovo flusso di parole con il numero di occorrenze di ciascuna di esse.

	> [AZURE.NOTE]I bolt sopra descritti si limitano a leggere e scrivere nei flussi. È tuttavia possibile usare un bolt per comunicare con origini quali un database o un servizio.

3.	Aprire **Splitter.cs**. Si noti che, per impostazione predefinita, dispone soltanto del metodo **Execute**. Tale metodo viene chiamato quando il bolt riceve una tupla da elaborare. È possibile leggere ed elaborare tuple in ingresso e generare tuple in uscita.

4.	Sostituire il contenuto della classe **Splitter** con il codice riportato di seguito:

	```
	private Context ctx;


	// Constructor
	public Splitter(Context ctx)
	{
	    Context.Logger.Info("Splitter constructor called");
	    this.ctx = ctx;


	    // Declare Input and Output schemas
	    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
	    // Input contains a tuple with a string field (the sentence)
	    inputSchema.Add("default", new List<Type>() { typeof(string) });
	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // Outbound contains a tuple with a string field (the word)
	    outputSchema.Add("default", new List<Type>() { typeof(string) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
	}


	// Get a new instance of the bolt
	public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Splitter(ctx);
	}


	// Called when a new tuple is available
	public void Execute(SCPTuple tuple)
	{
	    Context.Logger.Info("Execute enter");


	    // Get the sentence from the tuple
	    string sentence = tuple.GetString(0);
	    // Split at space characters
	    foreach (string word in sentence.Split(' '))
	    {
	        Context.Logger.Info("Emit: {0}", word);
	        //Emit each word
	        this.ctx.Emit(new Values(word));
	    }


	    Context.Logger.Info("Execute exit");
	}
	```

	Si consiglia di leggere con attenzione i commenti per comprendere le finalità del codice.

5.	Aprire **Counter.cs** e sostituire il contenuto della classe con quanto riportato di seguito:

	```
	private Context ctx;


	// Dictionary for holding words and counts
	private Dictionary<string, int> counts = new Dictionary<string, int>();


	// Constructor
	public Counter(Context ctx)
	{
	    Context.Logger.Info("Counter constructor called");
	    // Set instance context
	    this.ctx = ctx;


	    // Declare Input and Output schemas
	    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
	    // A tuple containing a string field - the word
	    inputSchema.Add("default", new List<Type>() { typeof(string) });


	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // A tuple containing a string and integer field - the word and the word count
	    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
	}


	// Get a new instance
	public static Counter Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Counter(ctx);
	}


	// Called when a new tuple is available
	public void Execute(SCPTuple tuple)
	{
	    Context.Logger.Info("Execute enter");


	    // Get the word from the tuple
	    string word = tuple.GetString(0);
	    // Do we already have an entry for the word in the dictionary?
	    // If no, create one with a count of 0
	    int count = counts.ContainsKey(word) ? counts[word] : 0;
	    // Increment the count
	    count++;
	    // Update the count in the dictionary
	    counts[word] = count;


	    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
	    // Emit the word and count information
	    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));


	    Context.Logger.Info("Execute exit");
	}
	```

	Si consiglia di leggere con attenzione i commenti per comprendere le finalità del codice.

###Definire la topologia

Gli spout e i bolt vengono disposti in un grafico che definisce i flussi di dati tra i componenti. Di seguito è riportato il grafico relativo alla topologia in oggetto:

![immagine della disposizione degli elementi](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Lo spout genera frasi, che vengono quindi distribuite a istanze del bolt Splitter. Il bolt Splitter suddivide le frasi in parole, che vengono quindi distribuite al bolt Counter.

Poiché il conteggio delle parole viene mantenuto in locale nell'istanza del bolt Counter, si desidera assicurare che parole specifiche vengano indirizzate alla stessa istanza del bolt Counter, in modo che soltanto un'istanza tenga traccia di una determinata parola. Per quanto riguarda il bolt Splitter, tuttavia, non è importante stabilire quale bolt riceve quale frase. Si desidera semplicemente bilanciare il carico delle frasi tra le diverse istanze.

Aprire **Program.cs**. In questo caso il metodo importante è **ITopologyBuilder**, usato per definire la topologia inviata a Storm. Sostituire il contenuto di **ITopologyBuilder** con il seguente codice per implementare la topologia descritta in precedenza:

```
    // Create a new topology named 'WordCount'
    TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount");

    // Add the spout to the topology.
    // Name the component 'sentences'
    // Name the field that is emitted as 'sentence'
    topologyBuilder.SetSpout(
        "sentences",
        Spout.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
        },
        1);
    // Add the splitter bolt to the topology.
    // Name the component 'splitter'
    // Name the field that is emitted 'word'
    // Use suffleGrouping to distribute incoming tuples
    //   from the 'sentences' spout across instances
    //   of the splitter
    topologyBuilder.SetBolt(
        "splitter",
        Splitter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
        },
        1).shuffleGrouping("sentences");

    // Add the counter bolt to the topology.
    // Name the component 'counter'
    // Name the fields that are emitted 'word' and 'count'
    // Use fieldsGrouping to ensure that tuples are routed
    //   to counter instances based on the contents of field
    //   position 0 (the word). This could also have been
    //   List<string>(){"word"}.
    //   This ensures that the word 'jumped', for example, will always
    //   go to the same instance
    topologyBuilder.SetBolt(
        "counter",
        Counter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
        },
        1).fieldsGrouping("splitter", new List<int>() { 0 });

    // Add topology config
    topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
    {
        {"topology.kryo.register","["[B"]"}
    });

    return topologyBuilder;
```

Si consiglia di leggere con attenzione i commenti per comprendere le finalità del codice.

##Inviare la topologia

1.	In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e selezionare **Submit to Storm on HDInsight**.

	> [AZURE.NOTE]Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

2.	Selezionare il cluster Storm in HDInsight dall'elenco a discesa **Storm Cluster** e quindi selezionare **Submit**. È possibile verificare se l'invio è riuscito o meno usando la finestra **Output**.

3.	Dopo che la topologia è stata inviata correttamente, verrà visualizzato l'elenco **Storm Topologies** relativo al cluster. Selezionare dall'elenco la topologia **WordCount** per visualizzare le informazioni sulla topologia in esecuzione.

	> [AZURE.NOTE]È possibile visualizzare **Storm Topologies** anche da **Esplora server**, espandendo **Azure** > **HDInsight** e quindi facendo clic su un cluster Storm in HDInsight e selezionando **View Storm Topologies**.

	Usare i collegamenti per gli spout o i bolt per visualizzare informazioni su questi componenti. Verrà aperta una nuova finestra per ogni elemento selezionato.

4.	Nella visualizzazione **Topology Summary** selezionare **Kill** per arrestare la topologia.

	> [AZURE.NOTE]Le topologie Storm continuano l'esecuzione fino a quando non vengono disattivate o il cluster non viene eliminato.

##Topologia transazionale

La topologia descritta in precedenza è di tipo non transazionale. In altri termini, se si verifica un errore di elaborazione causato da un componente della topologia, i componenti all'interno della topologia stessa non implementano alcuna funzionalità per la ripetizione dei messaggi. Per un esempio di topologia transazionale, creare un nuovo progetto e selezionare **Storm Sample** come tipo di progetto.

Le topologie transazionali implementano le operazioni seguenti per supportare la ripetizione dei dati:

-	**Memorizzazione dei metadati nella cache**: lo spout deve memorizzare i metadati relativi ai dati generati, in modo che, in caso di errore, questi ultimi possano essere recuperati e generati nuovamente. Poiché l'esempio genera una quantità limitata di dati, i dati non elaborati di ogni tupla vengono archiviati in un dizionario per la ripetizione.

-	**Ack**: ogni bolt della topologia può chiamare `this.ctx.Ack(tuple)` per confermare che ha elaborato correttamente la tupla. Dopo che tutti i bolt hanno riconosciuto la tupla, viene richiamato il metodo `Ack` dello spout. Questo consente allo spout di rimuovere i dati memorizzati nella cache per la ripetizione, poiché tali dati sono stati completamente elaborati.

-	**Errore**: ciascun bolt può chiamare `this.ctx.Fail(tuple)` per indicare che l'elaborazione di una tupla non è riuscita. L'errore viene propagato al metodo `Fail` dello spout, dove la tupla può essere ripetuta usando i metadati memorizzati nella cache.

-	**ID sequenza**: quando si genera una tupla, è possibile specificare un ID sequenza. Deve trattarsi di un valore che identifica la tupla per la ripetizione dell'elaborazione (Riconoscimento ed Errore). Ad esempio, quando genera i dati, lo spout presente nel progetto **Storm Sample** usa il codice riportato di seguito:

	```
	this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);
	```

	Questo genera una nuova tupla contenente una frase indirizzata al flusso predefinito, con il valore di ID sequenza contenuto in **lastSeqId**. Ai fini di questo esempio, **lastSeqId** viene semplicemente incrementato per ogni tupla generata.

Come dimostrato nel progetto **Storm Sample**, è possibile specificare in fase di esecuzione, in base alla configurazione, se un componente è transazionale o meno.

##Topologia ibrida

HDInsight Tools per Visual Studio può essere usato anche per creare topologie ibride, dove alcuni componenti sono in C# e altri in Java.

Per un esempio di topologia ibrida, creare un nuovo progetto e selezionare **Storm Hybrid Sample**. Viene creato un esempio interamente commentato contenente diverse topologie che illustrano le operazioni seguenti:

-	**Java spout** e **C# bolt**: definiti in **HybridTopology\_javaSpout\_csharpBolt**

	-	Una versione transazionale è definita in **HybridTopologyTx\_javaSpout\_csharpBolt**

-	**C# spout** e **Java bolt**: definiti in **HybridTopology\_csharpSpout\_javaBolt**

	-	Una versione transazionale è definita in **HybridTopologyTx\_csharpSpout\_javaBolt**

		> [AZURE.NOTE]Questa versione illustra anche come usare codice Clojure da un file di testo come componente Java.

Quando si invia il progetto, per passare da una topologia all'altra spostare semplicemente l'istruzione `[Active(true)]` nella topologia che si desidera usare prima di effettuare l'invio al cluster.

> [AZURE.NOTE]Tutti i file Java necessari vengono forniti come parte di questo progetto nella cartella **JavaDependency**.

Durante la creazione e l'invio di una topologia ibrida, tenere presente quanto riportato di seguito:

-	Per creare una nuova istanza della classe Java per uno spout o un bolt è necessario usare **JavaComponentConstructor**.

-	Per serializzare i dati all'interno o all'esterno di componenti Java da oggetti Java a JSON è necessario usare **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer**.

-	Durante l'invio della topologia al server, è necessario usare l'opzione **Additional configurations** per specificare i **percorsi dei file Java**. Il percorso specificato deve corrispondere alla directory contenente i file con estensione jar in cui sono presenti le classi Java.

###Hub eventi di Azure

La versione 0.9.4.203 di SCP.Net introduce una nuova classe e un nuovo metodo in modo specifico per l'utilizzo con lo spout dell'hub eventi (uno spout Java che legge dall'hub eventi). Quando si crea una topologia che utilizza questo spout, utilizzare i metodi seguenti:

-	Classe **EventHubSpoutConfig**: crea un oggetto che contiene la configurazione per il componente spout

-	Metodo **TopologyBuilder.SetEventHubSpout**: aggiunge il componente spout dell'hub eventi alla topologia

> [AZURE.NOTE]Benché questi elementi rendano più semplice utilizzare lo spot dell'hub eventi rispetto ad altri componenti Java, è necessario comunque utilizzare comunque CustomizedInteropJSONSerializer per serializzare i dati prodotti dallo spout.

##Come aggiornare SCP.NET

Le versioni recenti di SCP.NET supportano l'aggiornamento del pacchetto tramite NuGet. Quando è disponibile un nuovo aggiornamento, si riceverà una notifica. Per controllare manualmente la disponibilità di un aggiornamento, effettuare i passaggi seguenti:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Gestisci pacchetti NuGet**.

2. In Gestione pacchetti selezionare **Aggiornamenti**. Se un aggiornamento è disponibile, sarà presente nell'elenco. Fare clic sul pulsante **Aggiorna** relativo al pacchetto per installarlo.

> [AZURE.IMPORTANT]Se il progetto è stato creato con una delle versioni precedenti di SCP.NET che non usavano NuGet per gli aggiornamenti del pacchetto, è necessario eseguire i passaggi seguenti per effettuare l'aggiornamento alla nuova versione:
>
> 1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Gestisci pacchetti NuGet**.
> 2. Nel campo **Cerca** cercare **Microsoft.SCP.Net.SDK** e quindi aggiungerlo al progetto.

##Risoluzione dei problemi

###Testare la topologia in locale

Anche se la distribuzione di una topologia a un cluster è un'operazione semplice, in alcuni casi può essere necessario eseguire un test in locale. Per eseguire e testare la topologia di esempio descritta in questa esercitazione nell'ambiente di sviluppo locale, seguire questa procedura.

> [AZURE.WARNING]Il test locale funziona solo per topologie di base di tipo C#. È consigliabile non utilizzare il test locale per topologie ibride o topologie che utilizzano più flussi, onde evitare errori.

1.	In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**. Nelle proprietà del progetto impostare **Tipo di output** su **Applicazione console**.

	![tipo di output](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

	> [AZURE.NOTE]Ricordarsi di reimpostare **Tipo di output** su **Libreria di classi** prima di distribuire la topologia a un cluster.

2.	In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e quindi selezionare **Aggiungi** > **Nuovo elemento**. Selezionare **Classe** e immettere **LocalTest.cs** come nome della classe. Infine fare clic su **Aggiungi**.

3.	Aprire **LocalTest.cs** e aggiungere all'inizio l'istruzione **using** seguente:

	```
	using Microsoft.SCP;
	```

4.	Usare il codice seguente come contenuto della classe **LocalTest**:

	```
	// Drives the topology components
	public void RunTestCase()
	{
	    // An empty dictionary for use when creating components
	    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();


	    #region Test the spout
	    {
	        Console.WriteLine("Starting spout");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext spoutCtx = LocalContext.Get();
	        // Get a new instance of the spout, using the local context
	        Spout sentences = Spout.Get(spoutCtx, emptyDictionary);


	        // Emit 10 tuples
	        for (int i = 0; i < 10; i++)
	        {
	            sentences.NextTuple(emptyDictionary);
	        }
	        // Use LocalContext to persist the data stream to file
	        spoutCtx.WriteMsgQueueToFile("sentences.txt");
	        Console.WriteLine("Spout finished");
	    }
	    #endregion


	    #region Test the splitter bolt
	    {
	        Console.WriteLine("Starting splitter bolt");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext splitterCtx = LocalContext.Get();
	        // Get a new instance of the bolt
	        Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


	        // Set the data stream to the data created by the spout
	        splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
	        // Get a batch of tuples from the stream
	        List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
	        // Process each tuple in the batch
	        foreach (SCPTuple tuple in batch)
	        {
	            splitter.Execute(tuple);
	        }
	        // Use LocalContext to persist the data stream to file
	        splitterCtx.WriteMsgQueueToFile("splitter.txt");
	        Console.WriteLine("Splitter bolt finished");
	    }
	    #endregion


	    #region Test the counter bolt
	    {
	        Console.WriteLine("Starting counter bolt");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext counterCtx = LocalContext.Get();
	        // Get a new instance of the bolt
	        Counter counter = Counter.Get(counterCtx, emptyDictionary);


	        // Set the data stream to the data created by splitter bolt
	        counterCtx.ReadFromFileToMsgQueue("splitter.txt");
	        // Get a batch of tuples from the stream
	        List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
	        // Process each tuple in the batch
	        foreach (SCPTuple tuple in batch)
	        {
	            counter.Execute(tuple);
	        }
	        // Use LocalContext to persist the data stream to file
	        counterCtx.WriteMsgQueueToFile("counter.txt");
	        Console.WriteLine("Counter bolt finished");
	    }
	    #endregion
	}
	```

	Leggere con attenzione i commenti del codice. Questo codice usa **LocalContext** per eseguire i componenti dell'ambiente di sviluppo e rende permanente il flusso di dati tra i componenti e i file di testo sull'unità locale.

5.	Aprire **Program.cs** e aggiungere al metodo **Main** il codice seguente:

	```
	Console.WriteLine("Starting tests");
	System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
	// Initialize the runtime
	SCPRuntime.Initialize();


	//If we are not running under the local context, throw an error
	if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
	{
	    throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
	}
	// Create test instance
	LocalTest tests = new LocalTest();
	// Run tests
	tests.RunTestCase();
	Console.WriteLine("Tests finished");
	Console.ReadKey();
	```

6.	Salvare le modifiche, quindi usare **F5** o **Debug** > **Avvia debug** per avviare il progetto. Viene visualizzata una finestra della console, con lo stato del log aggiornato in base all'avanzamento dei test. Quando viene visualizzato il messaggio **Esecuzione test completata**, premere un tasto qualsiasi per chiudere la finestra.

7.	Usare **Esplora risorse** per trovare la directory che contiene il progetto, ad esempio, **C:\\Users<nome\_utente>\\Documenti\\Visual Studio 2013\\Projects\\WordCount\\WordCount**. In questa directory, aprire **Bin** e quindi fare clic su **Debug**. Verranno visualizzati i file di testo generati durante l'esecuzione dei test: sentences.txt, counter.txt e splitter.txt. Aprire ogni file di testo e verificare i dati.

	> [AZURE.NOTE]In questi file, i dati stringa vengono resi permanenti come matrice di valori decimali. Ad esempio, [[97,103,111]] nel file **splitter.txt** corrisponde alla parola 'and'.

Anche se il test di un'applicazione di conteggio parole di base è un'operazione abbastanza semplice, il valore effettivo risulta evidente nel caso di una topologia complessa che comunica con origini dati esterne o esegue l'analisi di dati complessi. Quando si lavora a un progetto di questo tipo, può essere necessario impostare punti di interruzione e scorrere il codice nei componenti per isolare i problemi.

> [AZURE.NOTE]Assicurarsi di reimpostare **Tipo di progetto** su **Libreria di classi** prima di eseguire la distribuzione a un cluster Storm in HDInsight.

###Informazioni di log

È possibile registrare facilmente le informazioni provenienti dai componenti della topologia usando `Context.Logger`. Il codice seguente, ad esempio, crea una voce di log informativa:

```
Context.Logger.Info("Component started");
```

È possibile visualizzare le informazioni registrate da **Hadoop Service Log**, disponibile in **Esplora Server**. Espandere la voce relativa al cluster Storm in HDInsight, quindi espandere **Hadoop Service Log**. Al termine, selezionare il file di log da visualizzare.

> [AZURE.NOTE]I log vengono memorizzati nell'account del servizio Archiviazione di Azure usato dal cluster. Se si tratta di una sottoscrizione diversa da quella con cui si è eseguita la connessione con Visual Studio, per visualizzare le informazioni sarà necessario accedere alla sottoscrizione che contiene l'account di archiviazione.

###Visualizzare le informazioni sugli errori

Per visualizzare gli errori che si sono verificati in una topologia in esecuzione, seguire questa procedura:

1.	Da **Esplora server** fare clic con il pulsante destro del mouse sul cluster Storm in HDInsight e selezionare **View Storm topologies**.

2.	Per quanto riguarda **Spout** e **Bolts**, nella colonna **Last Error** verranno visualizzate informazioni sull'ultimo errore verificatosi.

3.	Selezionare **Spout Id** o **Bolt Id** per il componente in cui si è verificato un errore. Viene visualizzata una pagina dei dettagli. Nella parte inferiore di tale pagina, nella sezione **Errors**, verranno visualizzate informazioni aggiuntive sugli errori.

4.	Per ottenere altre informazioni, selezionare una **porta** nella sezione **Executors** della pagina. Verrà visualizzato il log di lavoro di Storm relativo agli ultimi minuti.

##Passaggi successivi

Dopo aver appreso come sviluppare e distribuire topologie Storm usando HDInsight Tools per Visual Studio, vedere l'articolo [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md).

Per un esempio di una topologia di C# che divide il flusso di dati in più flussi, vedere [csharp-storm-example](https://github.com/Blackmist/csharp-storm-example).

Per altre informazioni sulla creazione delle topologie C#, visitare la pagina [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Per altre informazioni su come usare HDInsight o altri esempi su Storm in HDinsight, vedere le risorse seguenti:

**Apache Storm in HDInsight**

-	[Distribuzione e monitoraggio di topologie con Apache Storm in HDInsight](hdinsight-storm-deploy-monitor-topology.md)

-	[Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop in HDInsight**

-	[Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

-	[Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

-	[Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase in HDInsight**

-	[Introduzione a HBase in HDInsight](../hdinsight-hbase-get-started.md)

<!---HONumber=Sept15_HO4-->