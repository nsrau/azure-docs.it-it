---
title: Topologie Apache Storm con Visual Studio e Visual C# - Azure HDInsight
description: Informazioni su come creare topologie Storm in C#. Creare una topologia di conteggio parole in Visual Studio usando gli strumenti di Hadoop per Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f59328c5894a53b6337ecc04e3daebb2ef180c59
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927887"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Sviluppare topologie C# per Apache Storm tramite gli strumenti Data Lake per Visual Studio

Informazioni su come creare una topologia Apache Storm C# usando gli strumenti Azure Data Lake (Apache Hadoop) per Visual Studio. Questo documento illustra il processo di creazione di un progetto Storm in Visual Studio, l'esecuzione dei test in locale e la distribuzione del progetto in un cluster Apache Storm in Azure HDInsight.

Viene anche spiegato come creare topologie ibride che usano componenti C# e Java.

> [!NOTE]  
> Anche se i passaggi illustrati in questo documento si basano su un ambiente di sviluppo Windows con Visual Studio, il progetto compilato può essere inviato a un cluster HDInsight basato su Linux o su Windows. Solo i cluster basati su Linux creati dopo il 28 ottobre 2016 supportano le topologie SCP.NET.

Per usare una C# topologia con un cluster basato su Linux, è necessario aggiornare il pacchetto NuGet `Microsoft.SCP.Net.SDK` usato dal progetto alla versione 0.10.0.6 o successiva. La versione del pacchetto deve anche corrispondere alla versione principale di Storm installata in HDInsight.

| Versione HDInsight | Versione di Apache Storm | Versione di SCP.NET | Versione Mono predefinita |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(solo in HDInsight per Windows) | ND |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> Le topologie C# nei cluster basati su Linux devono usare .NET 4.5 e devono usare Mono per l'esecuzione nel cluster HDInsight. Per informazioni sulle potenziali incompatibilità, vedere [compatibilità mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="install-visual-studio"></a>Installazione di Visual Studio

È possibile sviluppare C# topologie con SCP.NET usando [Visual Studio](https://visualstudio.microsoft.com/downloads/). Le istruzioni riportate in questo articolo usano Visual Studio 2019, ma è possibile usare anche le versioni precedenti di Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Installare gli strumenti Data Lake per Visual Studio

Per installare gli strumenti Data Lake per Visual Studio, seguire la procedura in [Introduzione all'uso degli strumenti Data Lake per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Installare Java

Quando si invia una topologia Storm da Visual Studio, SCP.NET genera un file con estensione zip che contiene la topologia e le dipendenze. Java viene usato per creare i file con estensione zip perché usa un formato più compatibile con i cluster basati su Linux.

1. Installare Java Developer Kit (JDK) 7 o versione successiva nell'ambiente di sviluppo. È possibile ottenere Oracle JDK da [Oracle](https://openjdk.java.net/). È inoltre possibile usare [altre distribuzioni Java](/java/azure/jdk/).

2. Impostare la variabile di ambiente `JAVA_HOME` sulla directory che contiene Java.

3. Impostare la variabile di ambiente `PATH` per includere la directory *% JAVA_HOME% \ bin* .

È possibile compilare ed eseguire l'applicazione C# console seguente per verificare che Java e JDK siano installati correttamente:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Modelli Apache Storm

Gli strumenti Data Lake per Visual Studio includono i modelli seguenti:

| Tipo di progetto | Dimostra |
| --- | --- |
| Applicazione Storm |Un progetto di topologia Storm vuoto |
| Esempio di writer SQL di Azure per Storm |Come scrivere nel database SQL di Azure |
| Esempio di reader Storm per Azure Cosmos DB |Come leggere da Azure Cosmos DB |
| Esempio di writer Storm per Azure Cosmos DB |Come scrivere in Azure Cosmos DB |
| Esempio di lettore EventHub per Storm |Come leggere da Hub eventi di Azure |
| Esempio di writer EventHub per Storm |Come scrivere in Hub eventi di Azure |
| Esempio di lettore HBase per Storm |Come leggere da cluster HBase in HDInsight |
| Esempio di writer HBase per Storm |Come scrivere in cluster HBase in HDInsight |
| Esempio ibrido di Storm |Come usare un componente Java |
| Esempio di Storm |Topologia di conteggio parole di base |

> [!WARNING]  
> Non tutti i modelli funzionano con HDInsight basato su Linux. È possibile che i pacchetti NuGet usati dai modelli non siano compatibili con Mono. Per identificare i potenziali problemi, vedere [compatibilità mono](https://www.mono-project.com/docs/about-mono/compatibility/) e usare [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

Nelle procedure di questo documento viene usato il tipo di progetto Applicazione Storm di base per creare una topologia.

### <a name="apache-hbase-templates"></a>Modelli di Apache HBase

I modelli di lettore e writer HBase usano l'API REST HBase al posto dell'API Java HBase per comunicare con un cluster HBase in HDInsight.

### <a name="eventhub-templates"></a>Modelli EventHub

> [!IMPORTANT]  
> Il componente spout EventHub basato su Java incluso nel modello di lettore EventHub non funziona con Storm in HDInsight versione 3.5 o successiva. Una versione aggiornata di questo componente è disponibile in [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Per una topologia di esempio che usa questo componente e funziona con Storm in HDInsight 3.5, vedere [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Creare una topologia C#

Per creare un C# progetto di topologia in Visual Studio:

1. Aprire Visual Studio.

1. Nella finestra **Start** selezionare **Crea un nuovo progetto**.

1. Nella finestra **Crea un nuovo progetto** scorrere fino a selezionare l' **applicazione Storm**, quindi fare clic su **Avanti**.

1. Nella finestra **Configura nuovo progetto** immettere il nome di un **progetto** *WordCount*, passare a o creare un percorso di **Directory per** il progetto e quindi selezionare **Crea**.

    ![Applicazione Storm, configurare la finestra di dialogo nuovo progetto, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

Dopo aver creato il progetto, è necessario disporre dei seguenti file:

* *Program.cs*: definizione della topologia per il progetto. Per impostazione predefinita viene creata una topologia predefinita costituita da uno spout e da un bolt.

* *Spout.cs*: spout di esempio che genera numeri casuali.

* *Bolt.cs*: bolt di esempio che tiene conto dei numeri generati dallo spout.

Quando si crea il progetto, NuGet scarica la versione più recente del [pacchetto SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

[!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementare lo spout

Aggiungere quindi il codice per il beccuccio, che viene usato per leggere i dati in una topologia da un'origine esterna. Lo spout genera in modo casuale una frase nella topologia.

1. Aprire *Spout.cs*. Di seguito sono indicati i componenti principali di uno spout:

   * `NextTuple`: chiamato da Storm quando il beccuccio è autorizzato a emettere nuove Tuple.

   * `Ack` (solo topologia transazionale): gestisce i riconoscimenti avviati da altri componenti della topologia per le tuple inviate dal beccuccio. Il riconoscimento di una tupla consente allo spout di sapere che tale tupla è stata elaborata correttamente dai componenti downstream.

   * `Fail` (solo topologia transazionale): gestisce le tuple che non elaborano altri componenti nella topologia. L'implementazione di un metodo di `Fail` consente di riemettere la tupla in modo che possa essere nuovamente elaborata.

2. Sostituire il contenuto della classe `Spout` con il testo seguente:

    ```csharp
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

### <a name="implement-the-bolts"></a>Implementare i bolt

A questo punto, creare due Bolt Storm in questo esempio:

1. Eliminare dal progetto il file *Bolt.cs* esistente.

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e selezionare **Aggiungi** > **Nuovo elemento**. Selezionare **Storm Bolt** dall'elenco e immettere *Splitter.cs* come nome. Nel codice del nuovo file modificare il nome dello spazio dei nomi in `WordCount`. Ripetere quindi questo processo per creare un secondo Bolt denominato *Counter.cs*.

   * *Splitter.cs*: implementa un bolt che divide le frasi in singole parole e crea un nuovo flusso di parole.

   * *Counter.cs*: implementa un bolt che conta ogni parola e genera un nuovo flusso di parole con il numero di occorrenze di ciascuna di esse.

     > [!NOTE]  
     > I bolt sopra descritti eseguono operazioni di lettura e scrittura nei flussi. È tuttavia possibile usare un bolt per comunicare con origini quali un database o un servizio.

3. Aprire *Splitter.cs*. Per impostazione predefinita, ha un solo metodo: `Execute`. Il metodo `Execute` viene chiamato quando il Bolt riceve una tupla per l'elaborazione. È possibile leggere ed elaborare tuple in ingresso e generare tuple in uscita.

4. Sostituire il contenuto della classe `Splitter` con il codice seguente:

    ```csharp
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

5. Aprire *Counter.cs* e sostituire il contenuto della classe con il codice seguente:

    ```csharp
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

### <a name="define-the-topology"></a>Definire la topologia

Gli spout e i bolt vengono disposti in un grafico che definisce i flussi di dati tra i componenti. Di seguito è riportato il grafico relativo alla topologia in oggetto:

![Diagramma di disposizione del componente beccuccio e Bolt, topologia Storm](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Il beccuccio emette le frasi distribuite a istanze del Bolt Splitter. Il bolt Splitter suddivide le frasi in parole, che vengono quindi distribuite al bolt Counter.

Poiché l'istanza del contatore include il conteggio delle parole in locale, è necessario assicurarsi che le parole specifiche vengano propagate alla stessa istanza del Bolt del contatore. Ogni istanza tiene traccia di una parola specifica. Poiché il bolt Splitter non gestisce lo stato, non è importante stabilire quale istanza dello splitter riceve quale frase.

Aprire *Program.cs*. Il metodo importante è `GetTopologyBuilder`, che viene usato per definire la topologia inviata a Storm. Sostituire il contenuto di `GetTopologyBuilder` con il codice seguente per implementare la topologia descritta in precedenza:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Inviare la topologia

A questo punto è possibile inviare la topologia al cluster HDInsight.

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Invia a Storm in HDInsight**.

    > [!NOTE]  
    > Se richiesto, immettere le credenziali per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

2. Nella finestra di dialogo **Invia topologia** , nell'elenco a discesa **cluster Storm** , scegliere il cluster Storm in HDInsight e quindi selezionare **Invia**. È possibile verificare se l'invio è riuscito visualizzando il riquadro di **output** .

    Quando la topologia è stata inviata correttamente, verrà visualizzata la finestra di **visualizzazione delle topologie Storm** per il cluster. Scegliere la topologia **WordCount** dall'elenco per visualizzare le informazioni sulla topologia in esecuzione.

    ![Finestra visualizzazione topologia Storm, cluster HDInsight, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > È inoltre possibile visualizzare le **topologie Storm**  da **Esplora Server**. Espandere **Azure** > **HDInsight**, fare clic con il pulsante destro del mouse su un cluster Storm in HDInsight e scegliere **Visualizza topologie Storm**.

    Per visualizzare informazioni sui componenti della topologia, selezionare un componente nel diagramma.

3. Nella sezione **Riepilogo topologia** selezionare **Kill** per arrestare la topologia.

    > [!NOTE]  
    > Le topologie Storm continuano l'esecuzione fino a quando non vengono disattivate o il cluster non viene eliminato.

## <a name="transactional-topology"></a>Topologia transazionale

La topologia descritta in precedenza è di tipo non transazionale. I componenti della topologia non implementano la funzionalità per la riproduzione dei messaggi. Per un esempio di topologia transazionale, creare un progetto e selezionare **Storm Sample** come tipo di progetto.

Le topologie transazionali implementano le operazioni seguenti per supportare la ripetizione dei dati:

* **Memorizzazione dei metadati nella cache**: lo spout deve memorizzare i metadati relativi ai dati generati, in modo che, in caso di errore, questi ultimi possano essere recuperati e generati nuovamente. Poiché l'esempio genera una quantità limitata di dati, i dati non elaborati di ogni tupla vengono archiviati in un dizionario per la ripetizione.

* **Ack**: ogni bolt della topologia può chiamare `this.ctx.Ack(tuple)` per confermare che ha elaborato correttamente la tupla. Quando tutti i Bolt hanno riconosciuto la tupla, viene richiamato il metodo `Ack` del beccuccio. Il metodo `Ack` consente allo spout di rimuovere i dati memorizzati nella cache per la ripetizione.

* **Errore**: ciascun bolt può chiamare `this.ctx.Fail(tuple)` per indicare che l'elaborazione di una tupla non è riuscita. L'errore viene propagato al metodo `Fail` dello spout, dove la tupla può essere ripetuta usando i metadati memorizzati nella cache.

* **ID sequenza**: quando si genera una tupla, è possibile specificare un ID sequenza univoco. Questo valore identifica la tupla per l'elaborazione della riproduzione (`Ack` e `Fail`). Ad esempio, il beccuccio nel progetto **Storm Sample** usa la chiamata al metodo seguente per la creazione di dati:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Questo codice genera una tupla contenente una frase nel flusso predefinito, con il valore di ID sequenza contenuto in `lastSeqId`. Per questo esempio, `lastSeqId` viene incrementato per ogni tupla generata.

Come dimostrato nel progetto **Storm Sample**, è possibile specificare in fase di esecuzione, in base alla configurazione, se un componente è transazionale o meno.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia ibrida con C# e Java

Gli strumenti Data Lake per Visual Studio possono essere usati anche per creare topologie ibride, dove alcuni componenti sono in C# e altri in Java.

Per un esempio di topologia ibrida, creare un progetto e selezionare **Storm Hybrid Sample**. Questo tipo di esempio illustra i concetti seguenti:

* **Beccuccio** e  **C# Bolt**Java: definiti nella classe `HybridTopology_javaSpout_csharpBolt`.

  Una versione transazionale è definita nella classe `HybridTopologyTx_javaSpout_csharpBolt`.

* beccuccio e **Bolt Java**: definiti nella classe `HybridTopology_csharpSpout_javaBolt`. **C#**

  Una versione transazionale è definita nella classe `HybridTopologyTx_csharpSpout_javaBolt`.

  > [!NOTE]  
  > Questa versione illustra anche come usare codice Clojure da un file di testo come componente Java.

Quando si invia il progetto, per passare da una topologia all'altra spostare l'istruzione `[Active(true)]` nella topologia che si desidera usare prima di effettuare l'invio al cluster.

> [!NOTE]  
> Tutti i file Java necessari vengono forniti come parte di questo progetto nella cartella *JavaDependency* .

Quando si crea e si invia una topologia ibrida, tenere presente quanto segue:

* Usare `JavaComponentConstructor` per creare un'istanza della classe Java per un beccuccio o un Bolt.

* Usare `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` per serializzare i dati all'interno o all'esterno di componenti Java da oggetti Java a JSON.

* Durante l'invio della topologia al server, è necessario usare l'opzione **Configurazioni aggiuntive** per specificare i **percorsi dei file Java**. Il percorso specificato deve essere la directory che contiene i file JAR contenenti le classi Java.

### <a name="azure-event-hubs"></a>Hub eventi di Azure

La versione 0.9.4.203 di SCP.Net introduce una nuova classe e un nuovo metodo appositamente da usare con lo spout dell'hub eventi (uno spout Java che legge dall'hub eventi). Quando si crea una topologia che usa un becco dell'hub eventi (ad esempio, usando il modello di **esempio Storm EventHub Reader** ), usare le API seguenti:

* `EventHubSpoutConfig` Class: crea un oggetto che contiene la configurazione per il componente del beccuccio.

* Metodo `TopologyBuilder.SetEventHubSpout`: aggiunge il componente del beccuccio dell'hub eventi alla topologia.

> [!NOTE]  
> È comunque necessario usare il `CustomizedInteropJSONSerializer` per serializzare i dati prodotti dal beccuccio.

## <a id="configurationmanager"></a>Usare ConfigurationManager

Non usare **ConfigurationManager** per recuperare i valori di configurazione dai componenti bolt e spout. Questa operazione può generare un'eccezione del puntatore null. Al contrario, passare la configurazione per il progetto alla topologia Storm come coppia chiave/valore nel contesto della topologia. Ogni componente basato sui valori di configurazione deve recuperarli dal contesto durante l'inizializzazione.

Il codice seguente illustra come recuperare questi valori:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Se si usa un metodo `Get` per restituire un'istanza del componente, è necessario assicurarsi che sia il parametro `Context` che il parametro `Dictionary<string, Object>` vengano passati al costruttore. L'esempio seguente illustra un metodo `Get` di base che passa in modo corretto questi valori:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Come aggiornare SCP.NET

Le versioni recenti di SCP.NET supportano l'aggiornamento del pacchetto tramite NuGet. Quando è disponibile un nuovo aggiornamento, si riceve una notifica. Per controllare manualmente la disponibilità di un aggiornamento, seguire questa procedura:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.

2. In Gestione pacchetti selezionare **Aggiornamenti**. Se è disponibile un aggiornamento per il pacchetto per il supporto SCP.NET, viene elencato. Selezionare **Aggiorna** per il pacchetto e quindi nella finestra di dialogo **Anteprima modifiche** Selezionare **OK** per installarlo.

> [!IMPORTANT]  
> Se il progetto è stato creato con una versione precedente di SCP.NET che non usa NuGet, è necessario seguire questa procedura per effettuare l'aggiornamento a una nuova versione:
>
> 1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
> 2. Usando il campo di **ricerca** , cercare, quindi aggiungere `Microsoft.SCP.Net.SDK` al progetto.

## <a name="troubleshoot-common-issues-with-topologies"></a>Risoluzione di problemi comuni con le topologie

### <a name="null-pointer-exceptions"></a>Eccezioni del puntatore null

Quando si usa una C# topologia con un cluster HDInsight basato su Linux, i componenti Bolt e beccucci che usano **ConfigurationManager** per leggere le impostazioni di configurazione in fase di esecuzione possono restituire eccezioni del puntatore null.

La configurazione per il progetto viene passata nella topologia Storm come coppia chiave/valore nel contesto della topologia Può essere recuperato dall'oggetto dizionario passato ai componenti quando vengono inizializzati.

Per ulteriori informazioni, vedere la sezione [utilizzare ConfigurationManager](#configurationmanager) di questo documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Quando si usa una C# topologia con un cluster HDInsight basato su Linux, è possibile che si verifichi l'errore seguente:

`System.TypeLoadException: Failure has occurred while loading a type.`

Questo errore si verifica quando si usa un file binario non compatibile con la versione di .NET supportata da mono.

Per i cluster HDInsight basati su Linux, è necessario verificare che il progetto usi file binari compilati per .NET 4.5.

### <a name="test-a-topology-locally"></a>Testare la topologia in locale

Sebbene sia facile distribuire una topologia in un cluster, in alcuni casi potrebbe essere necessario testare una topologia in locale. Usare la procedura seguente per eseguire e testare la topologia di esempio in questo articolo in locale nell'ambiente di sviluppo.

> [!WARNING]  
> I test locali funzionano solo per topologie di base di tipo C#. Non è possibile usare il test locale per topologie ibride o topologie che impiegano più flussi.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**. Nelle proprietà del progetto. Modificare quindi il **tipo di output** nell' **applicazione console**.

   ![Applicazione HDInsight Storm, proprietà del progetto, tipo di output](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Ricordarsi di reimpostare **Tipo di output** su **Libreria di classi** prima di distribuire la topologia a un cluster.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e quindi selezionare **Aggiungi** > **Nuovo elemento**. Selezionare **Classe** e immettere *LocalTest.cs* come nome della classe. Infine, selezionare **Aggiungi**.

1. Aprire *LocalTest.cs*e aggiungere l'istruzione `using` seguente nella parte superiore:

    ```csharp
    using Microsoft.SCP;
    ```

1. Usare il codice seguente come contenuto della classe `LocalTest`:

    ```csharp
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

    Leggere con attenzione i commenti del codice. Questo codice USA `LocalContext` per eseguire i componenti nell'ambiente di sviluppo. Il flusso di dati viene reso permanente tra i componenti in file di testo nell'unità locale.

1. Aprire *Program.cs*e aggiungere il codice seguente al metodo `Main`:

    ```csharp
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

1. Salvare le modifiche e quindi premere **F5** oppure scegliere **debug** > **Avvia debug** per avviare il progetto. Viene visualizzata una finestra della console, con lo stato del log aggiornato in base all'avanzamento dei test. Quando `Tests finished` viene visualizzato, selezionare un tasto qualsiasi per chiudere la finestra.

1. Usare **Esplora risorse** per individuare la directory contenente il progetto. Ad esempio: *C:\\utenti\\\<your_user_name >\\\\\\repository\\WordCount WordCount*). Quindi, in questa directory aprire *bin*, quindi selezionare *debug*. Verranno visualizzati i file di testo prodotti durante l'esecuzione dei test: *Frases. txt*, *Counter. txt*e *splitter. txt*. Aprire ogni file di testo e verificare i dati.

   > [!NOTE]  
   > In questi file i dati stringa vengono resi permanenti come matrice di valori decimali. Ad esempio, `[[97,103,111]]` nel file **splitter. txt** rappresenta la parola *fa*.

> [!NOTE]  
> Assicurarsi di impostare di nuovo il **tipo di progetto** su libreria di **classi** nelle proprietà del progetto prima della distribuzione in un cluster Storm in HDInsight.

### <a name="log-information"></a>Informazioni di log

È possibile registrare facilmente le informazioni provenienti dai componenti della topologia usando `Context.Logger`. Il comando seguente, ad esempio, crea una voce di log informativa:

`Context.Logger.Info("Component started");`

È possibile visualizzare le informazioni registrate da **Hadoop Service Log**, disponibile in **Esplora Server**. Espandere la voce relativa al cluster Storm in HDInsight, quindi espandere **Hadoop Service Log**. Al termine, selezionare il file di log da visualizzare.

> [!NOTE]  
> I log vengono memorizzati nell'account del servizio Archiviazione di Azure usato dal cluster. Per visualizzare i log in Visual Studio, è necessario accedere alla sottoscrizione di Azure proprietaria dell'account di archiviazione.

### <a name="view-error-information"></a>Visualizzare le informazioni sugli errori

Per visualizzare gli errori che si sono verificati in una topologia in esecuzione, seguire questa procedura:

1. Da **Esplora server**, fare clic con il pulsante destro del mouse sul cluster Storm in HDInsight e selezionare **Visualizza topologie Storm**.

   Per quanto riguarda **Spout** e **Bolt**, nella colonna **Ultimo errore** vengono visualizzate informazioni sull'ultimo errore verificatosi.

2. Selezionare l'ID del **beccuccio** o l' **ID del Bolt** per il componente in cui è elencato un errore. Nella pagina dei dettagli vengono visualizzate informazioni aggiuntive sull'errore nella sezione **errori** nella parte inferiore della pagina.

3. Per ottenere altre informazioni, selezionare una **porta** nella sezione **Executors** (Esecutori) della pagina. Verrà visualizzato il log di lavoro di Storm relativo agli ultimi minuti.

### <a name="errors-submitting-topologies"></a>Errori durante l'invio delle topologie

Se si verificano errori durante l'invio di una topologia a HDInsight, è possibile trovare i log per i componenti lato server che gestiscono l'invio della topologia nel cluster HDInsight. Per scaricare questi log, usare il comando seguente da una riga di comando:

```shell
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Sostituire *sshuser* con l'account utente SSH per il cluster. Sostituire *clustername* con il nome del cluster HDInsight. Per altre informazioni sull'uso di `scp` e `ssh` con HDInsight, vedere l'articolo [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Gli invii possono non riuscire per diversi motivi:

* Il pacchetto JDK non è installato o non è presente nel percorso.
* Le dipendenze Java richieste non sono incluse nell'invio.
* Le dipendenze non sono compatibili.
* I nomi delle topologie sono duplicati.

Se il file di log *HDInsight-scpwebapi. out* contiene una `FileNotFoundException`, l'eccezione potrebbe essere causata dalle condizioni seguenti:

* Il pacchetto JDK non si trovi nel percorso dell'ambiente di sviluppo. Verificare che il pacchetto JDK sia installato nell'ambiente di sviluppo e che `%JAVA_HOME%/bin` sia presente nel percorso.
* Manca una dipendenza Java. Assicurarsi di includere tutti i file con estensione jar necessari come parte dell'invio.

## <a name="next-steps"></a>Passaggi successivi

Per un esempio di elaborazione dei dati dall'hub eventi, vedere [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Per un esempio di una topologia di C# che divide il flusso di dati in più flussi, vedere [csharp-storm-example](https://github.com/Blackmist/csharp-storm-example).

Per altre informazioni sulla creazione delle topologie C#, visitare [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Per altre informazioni su come usare HDInsight o altri esempi su Storm in HDinsight, vedere i documenti seguenti:

**Microsoft SCP.NET**

* [Guida alla programmazione SCP per Apache Storm in Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm in HDInsight**

* [Distribuire e gestire le topologie Apache Storm su Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Esempio di topologie di Apache Storm in Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop in HDInsight**

* [Che cos'è Apache Hive e HiveQL in Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Usare MapReduce in Apache Hadoop in HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase in HDInsight**

* [Usare Apache HBase in Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
