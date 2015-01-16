<properties title="Develop streaming data processing applications with SCP.NET and C# on Storm in HDInsight" pageTitle="Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET in Storm | Azure" description="Informazioni su come sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm in HDInsight." services="hdinsight" solutions="" documentationCenter="" authors="Qianlin Xia" videoId="" scriptId="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/15/2014" ms.author="qixia" />

#Sviluppare applicazioni di elaborazione di flussi di dati in C# con Stream Computing Platform e Storm in HDInsight

Stream Computing Platform (SCP) è una piattaforma per la compilazione di applicazioni di elaborazione dati dalle prestazioni elevate, affidabili, distribuite, coerenti e in tempo reale mediante .NET. È basata su [Apache Storm](http://storm.incubator.apache.org/), un sistema open source di elaborazione dei flussi in tempo reale disponibile con HDInsight.

Contenuto dell'articolo:

* Cos'è SCP e come funziona con Storm

* Come creare una soluzione SCP

* Come testare una soluzione SCP

* Come distribuire una soluzione SCP in un cluster HDInsight Storm

##Prerequisiti

* Una sottoscrizione di Azure

* Un cluster HDInsight Storm

* Visual Studio 2010 o 2013

##Sommario

* [SCP e Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP e Storm

Apache Storm è un sistema di calcolo distribuito che viene eseguito su cluster Hadoop e consente di elaborare dati in tempo reale. Sebbene venga eseguito in Java Virtual Machine (JVM), Storm è stato progettato in modo da consentire l'implementazione di soluzioni (dette **topologie**) in una serie di linguaggi di programmazione. È persino possibile creare una topologia con una combinazione di componenti scritti in più linguaggi.

SCP fornisce le librerie che semplificano la creazione delle soluzioni Storm mediante .NET. I cluster Azure HDInsight Storm includono i componenti necessari sul lato server per eseguire le soluzioni SCP create.

Per altre informazioni su HDInsight Storm, vedere [Panoramica di HDInsight Storm](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-storm-overview/).

###Progettare una soluzione SCP

SCP fornisce interfacce che consentono di creare i componenti Storm seguenti:

* Spout: usa i dati da un'origine (file, database, API e così via) ed emette uno o più flussi di tuple (elenco ordinato di elementi).
* Bolt: usa uno o più flussi e può emettere uno o più flussi.
* Topologia: definisce il flusso di dati tra Spout e Bolt, il parallelismo di Spout e Bolt e informazioni di configurazione.

> [AZURE.NOTE] Spout e Bolt implementano anche l'elaborazione generica. Ad esempio, uno Spout può suddividere un messaggio in arrivo in più tuple oppure emettere semplicemente una tupla e lasciare che un Bolt estragga i valori di cui necessita. Analogamente, se è necessario scrivere dati in un archivio dati, è possibile implementarlo in un Bolt.

Per progettare una soluzione occorre tenere in considerazione quanto segue:

* Quali origini dati verranno usate? È necessario uno Spout per l'implementazione.
* Quale elaborazione deve essere effettuata? È necessario implementarla in Spout e/o Bolt.
* Se l'elaborazione è suddivisa in più Bolt, qual è il flusso di dati tra di essi? La descrizione deve essere effettuata mediante la topologia.
* In che modo l'elaborazione verrà distribuita nei nodi nel cluster HDInsight? La descrizione deve essere effettuata mediante la topologia.

##Installare l'SDK di SCP

L'SDK di SCP è fornito nel cluster HDInsight Storm. Dopo la [creazione di un cluster HDInsight Storm](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-storm-getting-started/), attenersi alla procedura seguente per scaricare l'SDK nel computer di sviluppo locale.

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).

2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.

3. Fare clic sul cluster HDInsight a cui connettersi.

4. Fare clic su **CONFIGURAZIONE** nella parte superiore della pagina.

5. Nella parte inferiore della pagina fare clic su **ABILITA MODALITÀ REMOTA**.

6. Nella procedura guidata Configura desktop remoto immettere **nome utente** e **password** di Desktop remoto. Immettere una data di scadenza nella casella **DATA DI SCADENZA**, quindi fare clic sull'icona con il segno di spunta.

7. Dopo avere abilitato Desktop remoto, fare clic su **CONNETTI** in fondo alla pagina e seguire le istruzioni.

8. Dopo avere effettuato la connessione al cluster tramite Desktop remoto, aprire **Esplora file** e immettere **%storm_home%\examples** nella barra degli indirizzi.

9. Fare clic con il pulsante destro del mouse sulla cartella **SDK** e scegliere **Copia**.

10. Nell'ambiente di sviluppo locale aprire **Esplora file** e individuare il percorso in cui archiviare l'SDK. Fare clic con il pulsante destro del mouse e scegliere **Incolla**.

##Creare una soluzione SCP

1. In Visual Studio creare un nuovo progetto di **Applicazione console**. Assegnare il nome **WordCount** alla soluzione.

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere **Aggiungi riferimento**.

3. Fare clic sul pulsante **Sfoglia** nella parte inferiore di **Gestione riferimenti**, quindi individuare la cartella SDK scaricata in precedenza. Selezionare **Microsoft.SCP.dll** e **Microsoft.SCPLogger.dll**, quindi fare clic su **Aggiungi**. Fare clic su **OK** per chiudere la finestra Gestione riferimenti.

###Creare lo Spout

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WordCount**, quindi scegliere **Aggiungi | Nuovo elemento**. Selezionare **Classe** e immettere il nome **WordSpout.cs**. Infine fare clic su **Aggiungi**.

2. Aprire il file **WordSpout.cs** e sostituire il codice esistente con quello seguente. Assicurarsi di leggere i commenti per comprendere il funzionamento di questo codice.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###Creare i test

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WordCount**, quindi scegliere **Aggiungi | Nuovo elemento**. Selezionare **Classe** e immettere il nome **LocalTest.cs**. Infine fare clic su **Aggiungi**.

2. Aprire il file **LocalTest.cs** e sostituire il codice esistente con quello seguente. Il codice usato per testare lo Spout è molto simile a quello che verrà usato per testare i Bolt.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. Aprire **Program.cs** e sostituire il codice esistente con quello seguente.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. Eseguire l'applicazione. Al termine, osservare la directory **WordCount\bin\debug** nel progetto di Visual Studio. Dovrebbe essere presente un file denominato **spout.txt**, che conterrà i dati emessi dallo Spout durante il test. Il contenuto dovrebbe essere simile al testo seguente:

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] Nelle righe riportate sopra, "Data" è la stringa emessa dallo Spout, ma archiviata come matrice di byte. Ad esempio, `[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]` corrisponde a "an apple a day keeps the doctor away".

###Creare i Bolt

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WordCount**, quindi scegliere **Aggiungi | Nuovo elemento**. Selezionare **Classe** e immettere il nome **SplitterBolt.cs**. Infine fare clic su **Aggiungi**. Ripetere il passaggio per aggiungere una classe denominata **CounterBolt.cs**.

2. Aprire il file **SplitterBolt.cs** e sostituire il codice esistente con quello seguente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. Aprire il file **CounterBolt.cs** e sostituire il contenuto esistente con quello seguente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] Questo Bolt emette un flusso, che è utile per i test. In una situazione reale i dati verrebbero archiviati in un database, in una coda o in un altro archivio persistente alla fine dell'elaborazione.

3. Aprire **LocalTest.cs** e aggiungere i test seguenti per SplitterBolt e CounterBolt dopo il blocco di test Spout precedente.

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. Eseguire la soluzione. Al termine, nella directory **WordCount\bin\debug** dovrebbero essere presenti i file seguenti:

    * **spout.txt**, che contiene i dati emessi da WordSpout
    * **splitter.txt**, che contiene i dati emessi da SplitterBolt
    * **counter.txt**, che contiene i dati emessi da CounterBolt

###Aggiungere il codice da eseguire in un cluster

1. Aprire **Program.cs** e sostituire la riga `//Code to run on HDInsight cluster will go here` con quanto segue, quindi ricompilare il progetto.

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. Creare un nuovo file denominato **WordCount.spec** e usare quanto segue come contenuto. In questo modo viene definita la topologia, con i componenti, il numero di istanze da creare nei nodi del cluster e la modalità di flusso dei dati tra di essi. La topologia è scritta usando il [Linguaggio di specifica della topologia](#spec).

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. Connettersi al cluster HDInsight Storm tramite Desktop remoto e copiare la cartella **bin\debug** del progetto WordCount locale nel cluster HDInsight Storm. Ad esempio, copiarla nella cartella **%storm_home%\examples** e rinominarla **WordCount**.

3. Copiare anche **WordCount.spec** nel server HDInsight. Inserirlo nella directory **%storm_home%\examples**.

4. Nel cluster HDInsight Storm usare l'icona della **riga di comando di Storm** sul desktop per aprire una riga di comando, quindi usare il comando seguente per avviare la topologia WordCount.

        bin\runspec examples\WordCount.spec temp examples\WordCount

    In questo modo verrà creata una nuova cartella denominata **temp** e le specifiche e i file per la soluzione WordCount verranno usati per creare un file **WordCount.jar**, che viene quindi inviato a Storm.

5. Quando termina l'esecuzione del comando, aprire l'**interfaccia utente di Storm** dal desktop. La topologia **WordCount** dovrebbe essere elencata. È possibile selezionare la topologia dall'**interfaccia utente di Storm** per visualizzare le statistiche.

6. Per arrestare la topologia, nell'**interfaccia utente di Storm** selezionare **WordCount**, quindi **Termina** in **Azioni topologia**.

##Riepilogo

Nella procedura riportata sopra è stato illustrato come creare, testare e distribuire una topologia di conteggio parole di base mediante Stream Computing Platform. Per altri esempi, vedere la directory **%storm_home%\examples** nel cluster HDInsight. Per maggiori dettagli su SCP, vedere le informazioni di riferimento seguenti.

##Riferimento a SCP

###Interfaccia del plug-in SCP

Le applicazioni SCP (o plug-in) vengono inserite nella pipeline Storm. Un plug-in è un'applicazione console .NET che implementa una o più delle interfacce seguenti.  

-	**ISCPSpout**: da usare quando si implementa uno Spout non transazionale
- 	**ISCPTxSpout**: da usare quando si implementa uno Spout transazionale
-	**ISCPBolt**: da usare quando si implementa un Bolt non transazionale
-	**ISCPBatchBolt**: da usare quando si implementa un Bolt transazionale

####ISCPPlugin

ISCPPlugin è l'interfaccia di base da cui ereditano tutte le altre interfacce SCP. Attualmente è un'interfaccia fittizia.  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout è l'interfaccia per uno Spout non transazionale.  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Quando viene chiamato `NextTuple()`, il codice può emettere una o più tuple. Se non ci sono tuple da emettere, il metodo deve essere restituito senza emettere alcunché.

`Ack()` e `Fail()` verranno chiamati solo se il meccanismo di acknowledgment è abilitato nel file delle specifiche. `seqId` identifica la tupla confermata o non confermata. Se l'acknowledgment è abilitato in una topologia non transazionale, nello Spout deve essere usata la funzione di emissione seguente:  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

Se l'acknowledgment non è supportato in una topologia non transazionale, `Ack()` e `Fail()` possono essere lasciati come funzioni vuote.  

Il parametro di input `parms` per queste funzioni è semplicemente un oggetto Dictionary vuoto ed è riservato per l'uso futuro.  

> [AZURE.NOTE] `NextTuple()`, `Ack()` e `Fail()` vengono chiamati in un ciclo ridotto in un singolo thread. Se non ci sono tuple da emettere, è utile usare `sleep` per una breve sospensione (ad esempio 10 millisecondi), in modo da risparmiare cicli della CPU.

####ISCPTxSpout

ISCPTxSpout è l'interfaccia per uno Spout transazionale.  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTx()` viene chiamato per avviare una nuova transazione. `seqId` usato per identificare la transazione, viene usato anche in `Ack()` e `Fail()`. I dati emessi da `NextTx()` verranno archiviati in ZooKeeper per supportare il la riproduzione. Poiché la capacità di archiviazione di ZooKeeper è molto limitata, nello Spout transazionale l'utente deve emettere solo metadati e non dati in blocco.  

Storm riprodurrà automaticamente una transazione in caso di errore, dunque in un caso normale Fail() non dovrebbe essere chiamato. Se però SCP può verificare i metadati emessi da uno Spout transazionale, può chiamare Fail() se i metadati non sono validi.

Il parametro di input `parms` per queste funzioni è semplicemente un oggetto Dictionary vuoto ed è riservato per l'uso futuro.

> [AZURE.NOTE] `NextTx()`, `Ack()` e `Fail()` vengono chiamati in un ciclo ridotto in un singolo thread. Se non ci sono tuple da emettere, è utile usare `sleep` per una breve sospensione (ad esempio 10 millisecondi), in modo da risparmiare cicli della CPU.

####ISCPBolt

ISCPBolt è l'interfaccia per un Bolt non transazionale.  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

Quando sarà disponibile una nuova tupla, verrà chiamata la funzione `Execute()` elaborarla.

####ISCPBatchBolt

ISCPBatchBolt è l'interfaccia per il Bolt transazionale.  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

Quando sarà disponibile una nuova tupla, verrà chiamata la funzione `Execute()` elaborarla. `FinishBatch()` verrà chiamato al termine di questa transazione.

Il parametro di input `parms` per queste funzioni è semplicemente un oggetto Dictionary vuoto ed è riservato per l'uso futuro.

> [AZURE.NOTE] I Bolt che implementano `ISCPBatchBolt` possono ottenere `StormTxAttempt` da `parms`. `StormTxAttempt` può essere usato per stabilire se una tupla è l'originale o un tentativo di riproduzione. Questa operazione viene in genere eseguita nel Bolt di commit ed è dimostrata nell'esempio **HelloWorldTx**.  

###Modello a oggetti

In SCP.NET è disponibile un semplice set di oggetti chiave che gli sviluppatori possono usare per la programmazione.

* `Context` fornisce informazioni sull'ambiente di esecuzione all'applicazione.

* `StateStore` fornisce servizi metadati, generazione di sequenze monotone e coordinamento senza attesa. `StateStore` consente di creare astrazioni di concorrenza distribuite di livello superiore, tra cui blocchi distribuiti, code distribuite e servizi di transazione.

* `SCPRuntime` inizializza un ambiente di runtime e avvia i plug-in durante l'esecuzione di una soluzione in Storm.

* `LocalContext` fornisce metodi per serializzare e deserializzare le tuple emesse nei file locali durante il test locale di un'applicazione in Visual Studio.

####Context

Ogni istanza di ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) dispone di un'istanza Context corrispondente. Le funzionalità fornite da Context possono essere suddivise in due parti:

* **Static**, disponibile nell'intero processo C#
* **Dynamic**, disponibile per le istanze specifiche  

**Contesto static**

* `public static ILogger Logger = null;` è fornito per scopi di registrazione.  

* `public static SCPPluginType pluginType;` ottiene il tipo di plug-in del processo C#. Se il processo C# viene eseguito in modalità di test locale (senza Java), il tipo di plug-in è "SCP_NET_LOCAL".

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }` ottiene i parametri di configurazione da JVM. I parametri vengono passati da JVM al momento dell'inizializzazione del plug-in. `Config` contiene due dizionari:

    * `public Dictionary<string, Object> stormConf { get; set; }` contiene i parametri definiti da Storm.

    * `public Dictionary<string, Object> pluginConf { get; set; }` contiene i parametri definiti da SCP.

* `public static TopologyContext TopologyContext { get; set; }` ottiene il contesto della topologia ed è particolarmente utile per i componenti con più operatori di parallelismo. Il codice seguente illustra come accedere al contesto della topologia.

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**Contesto dynamic**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);` dichiara lo schema di output e input per i flussi. L'esempio seguente dichiara uno schema di input costituito da un'unica tupla e uno schema di output costituito da una tupla di stringhe e da una tupla di numeri interi.

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);` emette una o più tuple nel flusso predefinito. L'esempio seguente emette due tuple nel flusso predefinito.

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);` emette una o più tuple nel flusso specificato. L'esempio seguente emette due tuple in un flusso denominato "mystream".

        this.ctx.Emit("mystream", new Values(word, count));

Quando si usano Spout e Bolt non transazionali in cui è abilitato l'acknowledgment, usare quanto segue.

* `public abstract void Emit(string streamId, List<object> values, long seqId);` emette una o più tuple e un identificatore di sequenza **da uno Spout** nel flusso specificato. L'esempio seguente emette una tupla e un identificatore di sequenza nel flusso predefinito.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);` emette una o più tuple e un identificatore di sequenza **da un Bolt** nel flusso specificato. Le tuple emesse sono ancorate alle tuple in ingresso specificate come `anchors`. In questo modo, gli acknowledgment possono risalire nella pipeline seguendo la concatenazione di tuple di ingresso/uscita. L'esempio seguente emette una tupla e un identificatore di sequenza nel flusso predefinito e ancora le tuple emesse alle tuple in ingresso contenute in `tuple`.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);` esegue l'acknowledgment di una tupla. Richiama la funzione `ISCPSpout.Ack` dello Spout che ha prodotto la tupla in origine.

* `public abstract void Fail(SCPTuple tuple);` determina l'esito negativo di una tupla. Richiama la funzione `ISCPSpout.Fail` dello Spout che ha generato la tupla in origine.

####StateStore

Le applicazioni SCP possono usare l'oggetto `State` per rendere persistenti le informazioni in ZooKeeper, in particolare per una topologia transazionale. Questo consente agli Spout transazionali di recuperare lo stato da ZooKeeper e riavviare la pipeline in caso di arresto anomalo.  

L'oggetto `StateStore` fornisce i metodi seguenti:

* `public static StateStore Get(string storePath, string connStr);` ottiene un `StateStore` per un percorso e una stringa di connessione specificati.

* `public State Create();` crea un nuovo oggetto `State` nell'istanza dell'archivio stati.

* `public IEnumerable<State> GetUnCommitted();` ottiene tutti gli oggetti `State` di cui non è stato eseguito il commit, esclusi gli stati di interruzione.

* `public IEnumerable<State> States();` ottiene tutti gli oggetti 'State' in `StateStore`.


* `public T Get<T>(string info = null);` ottiene un oggetto `State` o `Registry`.

    * `info` è il nome `Registry` da ottenere. Viene usato solo durante il recupero di un oggetto `Registry`.

    * `T` è il tipo di oggetto `State` o `Registry`.

* `public IEnumerable<Registry> Commited();` ottiene gli oggetti `Registry` con `State` che indica che è stato eseguito il commit.

* `public IEnumerable<Registry> Aborted();` ottiene gli oggetti `Registry` con `State` interrotto.

* `public State GetState(long stateId)` ottiene l'oggetto `State` per l'ID stato specificato.

L'oggetto **State** fornisce i metodi seguenti:

* `public void Commit(bool simpleMode = true);` imposta lo stato dell'oggetto `State` su commit.

    > [AZURE.NOTE] Quando `simpleMode` è impostato su true verrà semplicemente eliminato il ZNode corrispondente in ZooKeeper. In caso contrario, verrà eliminato il ZNode corrente e verrà aggiunto un nuovo nodo in `COMMITTED_PATH`.

* `public void Abort();` imposta lo stato dell'oggetto `State` su interruzione.

* `public void PutAttribute<T>(string key, T attribute);` imposta un valore di attributo per la chiave specificata.

    * `key`: chiave impostata per l'attributo

    * `attribute`: valore di attributo

* `public T GetAttribute<T>(string key);` ottiene il valore di attributo per la chiave specificata.

####SCPRuntime

SCPRuntime fornisce i metodi seguenti:

* `public static void Initialize();` inizializza la variabile di ambiente SCP. Quando viene chiamato, il processo C# si connette alla JVM per ottenere i parametri di configurazione e il contesto della topologia.

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);` avvia il ciclo di elaborazione dei messaggi. In questo ciclo, il plug-in C# riceve messaggi dalla JVM (incluse tuple e segnali di controllo) e quindi li elabora.

    * `pluginDelegate` è un delegato che può restituire un oggetto che implementa ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();` riceve tuple dalla coda

* `void WriteMsgQueueToFile(string filepath, bool append = false);` rende persistenti le tuple in un file.

* `void ReadFromFileToMsgQueue(string filepath);` legge le tuple da un file.


###<a id="spec"></a>Linguaggio di specifica della topologia

Il linguaggio di specifica della topologia SCP è un linguaggio specifico di dominio per la descrizione e la configurazione di topologie SCP. È basato sul linguaggio [Clojure DSL](http://storm.incubator.apache.org/documentation/Clojure-DSL.html) di Storm.  

Per definire una topologia, usare quanto segue.

|Nuove funzioni|	Parametri|	Descrizione
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	 Consente di definire una topologia transazionale con il nome della topologia, la mappa di definizione degli Spout e la mappa di definizione dei Bolt.
|**scp-tx-spout**|	exec-name<br> args<br> fields|	Consente di definire uno Spout transazionale. Eseguirà l'applicazione con ***exec-name*** usando ***args***.<br><br>Il parametro ***fields*** corrisponde ai campi di output per lo Spout.
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	Consente di definire un Bolt batch transazionale. Eseguirà l'applicazione con ***exec-name*** usando ***args***.<br><br>Il parametro fields corrisponde ai campi di output per il Bolt.
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	Consente di definire un Bolt di commit transazionale. Eseguirà l'applicazione con ***exec-name*** usando args.<br><br>Il parametro ***fields*** corrisponde ai campi di output per il Bolt.
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	Consente di definire una topologia non transazionale con il nome della topologia, la mappa di definizione degli Spout e la mappa di definizione dei Bolt.
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	Consente di definire uno Spout non transazionale. Eseguirà l'applicazione con ***exec-name*** usando ***args***.<br><br>Il parametro ***fields*** corrisponde ai campi di output per lo Spout.<br><br>***parameters*** è facoltativo e può essere usato per specificare parametri come "nontransactional.ack.enabled".
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	Consente di definire un Bolt non transazionale. Eseguirà l'applicazione con ***exec-name*** usando ***args***.<br><br>Il parametro ***fields*** corrisponde ai campi di output per il Bolt.<br><br>***parameters*** è facoltativo e può essere usato per specificare parametri come "nontransactional.ack.enabled".

Di seguito sono riportate le parole chiave che possono essere usate nella definizione di una topologia.

|Parole chiave|	Descrizione
|---------|------------
|**:name**|	Consente di definire il nome della topologia.
|**:topology**|	Consente di definire la topologia usando le funzioni precedenti e quelle predefinite.
|**:p**|	Consente di definire l'hint di parallelismo per ogni Spout o Bolt.
|**:config**|	Consente di definire i parametri di configurazione o aggiornare quelli esistenti.
|**:schema**|	Consente di definire lo schema del flusso.

Parametri di uso frequente

|Parametro|	Descrizione
|---------|------------
|**"plugin.name"**|	Nome del file eseguibile del plug-in C#.
|**"plugin.args"**|	Argomenti del plug-in.
|**"output.schema"**|	Schema di output.
|**"nontransactional.ack.enabled"**|	Abilitazione o meno dell'acknowledgment per la topologia non transazionale.

Le specifiche della topologia possono essere inviate direttamente al cluster Storm per l'esecuzione mediante il comando ***runspec***, situato nella directory **%storm_home%\bin** nei cluster HDInsight Storm.  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE] Il parametro ***resource-dir*** è facoltativo, è necessario specificarlo quando si vuole eseguire un'applicazione C# e questa directory conterrà l'applicazione, le dipendenze e le configurazioni.  

Anche il parametro ***classpath*** è facoltativo. Si usa per specificare il classpath Java se il file delle specifiche contiene uno Spout Java o un Bolt.  

###Funzionalità varie

####Dichiarazione dello schema di input e di output

Quando si chiama `Emit()`, la piattaforma serializza la tupla in una matrice di byte e la trasferisce alla JVM. Storm trasferirà quindi la tupla alle destinazioni. I Bolt riceveranno quindi le tuple. Per i Bolt C#, la tupla viene ricevuta dalla JVM e riconvertita nel tipo originale.

Per supportare questa serializzazione e deserializzazione, è necessario dichiarare lo schema degli input e degli output. Sono definiti come oggetti `Dictionary<string, List<Type>`, in cui la chiave è l'ID flusso e il valore corrisponde ai valori `Type` delle tuple che verranno emesse. Il componente può dichiarare più flussi.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

L'oggetto `Context` fornisce `DeclareComponentSchema`, che può essere usato per dichiarare lo schema per la serializzazione/deserializzazione.

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] Assicurarsi che le tuple emesse rispettino lo schema definito per il flusso. In caso contrario, verrà generata un'eccezione di runtime.  

####Supporto di più flussi

È possibile emettere in più flussi eseguendo più chiamate a `Emit()`, specificando il parametro `streamId` per ogni flusso in cui scrivere.

> [AZURE.NOTE] L'emissione in un flusso non esistente causerà eccezioni di runtime.

####Raggruppamento dei campi

Il raggruppamento dei campi predefinito di Storm non funziona correttamente in SCP.NET. Quando si inviano dati tramite proxy alla JVM, tutti i dati dei campi sono in effetti di tipo `byte[]` e per il raggruppamento dei campi viene usato il codice hash dell'oggetto `byte[]`. Il codice hash dell'oggetto `byte[]` è l'indirizzo dell'oggetto in memoria, per cui il raggruppamento di due oggetti `byte[]` che condividono lo stesso contenuto, ma non lo stesso indirizzo, sarà errato.

Per risolvere il problema, usare `scp-field-group` nella specifica. In questo modo verrà usato il contenuto di byte[] per eseguire il raggruppamento. Di seguito è riportato un esempio di uso in una specifica.  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

In questo esempio:

* `scp-field-group` indica l'uso del raggruppamento personalizzato.  
* `:tx` o `:non-tx` indica se si tratta di una topologia transazionale o non transazionale.
* `[0,1]` indica l'uso di un hashset di ID campo, che parte da 0.  

####Topologia ibrida

La maggior parte degli Spout, dei Bolt e delle topologie Storm native viene implementata in Java. Per supportare il riutilizzo di questi componenti in soluzioni che usano componenti C#, SCP consente di definire topologie ibride nella specifica.

* **Spout Java o Bolt**: nel file delle specifiche è possibile usare `scp-spout` e `scp-bolt` anche per specificare gli Spout Java e i Bolt. Di seguito è illustrato come specificare uno Spout Java con il nome di classe `microsoft.scp.example.HybridTopology.Generator`.

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Classpath Java **: quando si usano Spout Java o Bolt, è necessario prima compilare lo Spout o il Bolt in un file JAR. Aggiungere quindi il classpath Java con il parametro `-cp` quando si usa il comando **runSpec**. L'esempio seguente include i file JAR situati nella directory **examples\HybridTopology\java\target**.  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Serializzazione e deserializzazione tra Java e C#**: per serializzare oggetti tra Java e C#, usare `CustomizedInteropJSONSerializer`.

    > [WACOM.NOTE] Attualmente `CustomizedInteropJSONSerializer` supporta solo gli **Spout Java** e i **Bolt C#**.

    * Specificare il serializzatore per i componenti Java nel file delle specifiche.

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * Usare il serializzatore nei componenti C#.

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    L'implementazione predefinita consente di gestire la maggior parte dei casi, se il tipo di dati non è troppo complesso.  Se il tipo di dati è troppo complesso o se le prestazioni dell'implementazione predefinita non soddisfano i requisiti, è possibile creare un'implementazione personalizzata adatta alle proprie esigenze mediante le interfacce seguenti.

    **Interfaccia di serializzazione in Java** 

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **Interfaccia di deserializzazione in C#**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###Modalità host di SCP

La modalità host consente di compilare un progetto in una DLL e ospitarlo mediante **SCPHost.exe**. Si tratta di una modalità di hosting consigliata per la soluzione in un ambiente di produzione. Quando si usa la modalità host, il file delle specifiche includerà `SCPHost.exe` come plug-in.

Di seguito è riportato un esempio di file delle specifiche in cui è usata la modalità host per l'esempio HelloWorld.

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll** è l'assembly che contiene gli Spout e i Bolt.
* **Scp.App.HelloWorld.Generator** è il nome di classe per uno Spout contenuto in **HelloWorld.dll**.
* **Get** è il metodo da chiamare per ottenere un'istanza dello Spout.


##Esempi di programmazione SCP

Le seguenti applicazioni di esempio scritte con SCP sono reperibili nel cluster HDInsight Storm nel percorso **%storm_home%\examples**.

* **HelloWorld** è un semplicissimo esempio relativo a SCP.Net ed è simile all'esempio del conteggio di parole usato in precedenza in questo articolo. Usa una topologia non transazionale con uno Spout denominato **generator** e due Bolt denominati **splitter** e **counter**. Lo Spout **generator** genererà delle frasi casuali e le emetterà verso il Bolt **splitter**. Il Bolt **splitter** dividerà le frasi in parole ed emetterà le parole verso il Bolt **counter**. Il Bolt **counter** usa un dizionario per registrare il numero di occorrenze di ogni parola.

    Per questo esempio sono presenti due file di specifiche, **HelloWorld.spec** e **HelloWorld_EnableAck.spec**. Quando si usa **HelloWorld_EnableAck.spec**, l'esempio eseguirà l'acknowledgment delle tuple che attraversano i Bolt, mentre **splitter** è progettato per generare l'esito negativo casuale di alcuni Bolt per dimostrare la gestione degli errori nelle topologie non transazionali.

* **HelloWorldTx** è un esempio di implementazione di una topologia transazionale. Contiene uno Spout transazionale denominato **generator**, un Bolt batch denominato **partial-count** e un Bolt di commit denominato **count-sum**. Sono presenti anche tre file TXT creati in precedenza per l'uso con questa topologia: **DataSource0.txt**, **DataSource1.txt** e **DataSource2.txt**.

    In ogni transazione, lo Spout sceglierà casualmente due file ed emetterà i nomi dei due file verso il Bolt **partial-count**. Il Bolt otterrà il nome del file dalla tupla ricevuta, quindi aprirà il file e conterà il numero di parole al suo interno. Infine, emetterà le parole e i numeri verso il Bolt **count-sum**. Il Bolt **count-sum** riepilogherà il conteggio totale.  

    Per ottenere una semantica di tipo "exactly once", il Bolt di commit **count-sum** deve stabilire se quella che sta elaborando è una transazione riprodotta. In questo esempio, contiene una variabile membro statica:  

        public static long lastCommittedTxId = -1;  

    Quando viene creata un'istanza del Bolt, ottiene il tentativo di transazione (`txAttempt`,) dai parametri di input:  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

    Quando viene chiamato `FinishBatch()`, se non si tratta di una transazione riprodotta il valore `lastCommittedTxId` viene aggiornato.  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology** è una topologia che contiene uno Spout Java e un Bolt C#. Usa l'implementazione di serializzazione e deserializzazione predefinita fornita da SCP. Vedere **%storm_home%examples\HybridTopology\HybridTopology.spec** per maggiori dettagli e **SubmitTopology.bat** per informazioni su come specificare il classpath Java durante l'invio della topologia.  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png

<!--HONumber=35.1-->
