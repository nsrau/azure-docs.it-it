---
title: Guida alla programmazione SCP.NET per Storm in Azure HDInsight
description: Modalità d'uso di SCP.NET per creare topologie Storm basate su .NET da usare con Storm in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186870"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Guida alla programmazione SCP per Apache Storm in Azure HDInsight

SCP è una piattaforma per la compilazione di applicazioni di elaborazione dati in tempo reale, affidabili, coerenti e a elevate prestazioni. Si basa su [Apache Storm](https://storm.incubator.apache.org/), ovvero un sistema di elaborazione dei flussi progettato da community software open source. Nathan Marz ha creato Storm. È stato pubblicato come open source da Twitter. Storm USA [Apache ZooKeeper](https://zookeeper.apache.org/), un altro progetto Apache che consente il coordinamento distribuito altamente affidabile e la gestione dello stato.

Il progetto SCP è stato trasferito non solo a Storm in Windows, ma anche alle estensioni e alla personalizzazione del progetto per l'ambiente Windows. Le estensioni includono .NET Developer Experience e le librerie .NET. La personalizzazione include la distribuzione basata su Windows.

Con le estensioni e la personalizzazione, non è necessario creare un fork dei progetti software open source. È possibile utilizzare ambienti derivati basati su Storm.

## <a name="processing-model"></a>Modello di elaborazione

In dati in SCP vengono modellati come flussi continui di tuple. In genere, le tuple:

1. Flusso in una coda.
1. Vengono prelevati e trasformati dalla logica di business ospitata in una topologia Storm.
1. L'output viene inviato tramite pipe come tuple a un altro sistema SCP o viene eseguito il commit in archivi come file system distribuiti e database come SQL Server.

![Diagramma di una coda che invia dati all'elaborazione, che a sua volta inserisce un archivio dati](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

In Storm una topologia dell'applicazione definisce un grafico di calcolo. Ogni nodo in una topologia contiene la logica di elaborazione. I collegamenti tra i nodi indicano il flusso di dati.

I nodi che inseriscono i dati di input nella topologia sono detti _beccucci_. È possibile usarli per sequenziare i dati. I dati di input possono provenire da un'origine, ad esempio i log di file, un database transazionale o un contatore delle prestazioni di sistema.

I nodi che dispongono di flussi di dati di input e di output sono detti _Bolt_. Eseguono le operazioni effettive di filtro, selezione e aggregazione dei dati.

SCP supporta il massimo sforzo, almeno una volta, e esattamente una volta l'elaborazione dei dati.

In un'applicazione di elaborazione del flusso distribuita, è possibile che si verifichino errori durante l'elaborazione dei dati. Tali errori includono un'interruzione di rete, un errore del computer o un errore nel codice. L'elaborazione at-least-once garantisce che tutti i dati vengano elaborati almeno una volta, riproducendo automaticamente gli stessi dati quando si verifica un errore.

L'elaborazione at-least-once è semplice e affidabile e si adatta a molte applicazioni. Tuttavia, quando un'applicazione richiede il conteggio esatto, l'elaborazione at-least-once non è sufficiente perché gli stessi dati potrebbero essere riprodotti nella topologia dell'applicazione. In tal caso, una volta che l'elaborazione garantisce che il risultato sia corretto anche quando i dati vengono riprodotti ed elaborati più volte.

SCP consente agli sviluppatori .NET di creare applicazioni di elaborazione dati in tempo reale usando un Java Virtual Machine (JVM) con Storm. JVM e .NET comunicano tramite socket locali TCP. Ogni beccuccio/Bolt è una coppia di processi .NET/Java, in cui la logica utente viene eseguita in un processo .NET come plug-in.

Per creare un'applicazione di elaborazione dati in SCP, attenersi alla procedura seguente:

1. Progettare e implementare beccucci per il pull dei dati dalle code.
1. Progettare e implementare Bolt che elaborano i dati di input e li salvano in archivi esterni, ad esempio un database.
1. Progettare la topologia, quindi inviarla ed eseguirla.

La topologia definisce i vertici e i dati che passano tra di essi. SCP accetta una specifica della topologia e la distribuisce in un cluster Storm, in cui ogni vertice viene eseguito in un nodo logico. L'utilità di pianificazione delle attività Storm gestisce il failover e il ridimensionamento.

Questo articolo usa alcuni semplici esempi per illustrare come compilare applicazioni di elaborazione dati con SCP.

## <a name="scp-plug-in-interface"></a>Interfaccia del plug-in SCP

I plug-in SCP sono applicazioni autonome. Possono essere eseguiti all'interno di Visual Studio durante lo sviluppo e collegati alla pipeline Storm dopo la distribuzione di produzione.

La scrittura di un plug-in SCP equivale alla scrittura di qualsiasi altra applicazione console di Windows. La piattaforma SCP.NET dichiara alcune interfacce per il beccuccio/Bolt. Il codice del plug-in implementa tali interfacce. Lo scopo principale di questa progettazione è quello di consentire all'utente di concentrarsi sulla logica di business, consentendo al contempo alla piattaforma SCP.NET di gestire altre operazioni.

Il codice del plug-in implementa una delle interfacce seguenti. Quale interfaccia varia a seconda che la topologia sia transazionale o non transazionale e che il componente sia un becco o un Bolt.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** è l'interfaccia comune per molti plug-in. attualmente si tratta di un'interfaccia fittizia.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** è l'interfaccia per un beccuccio non transazionale.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Quando viene chiamato **NextTuple** , il C# codice può generare una o più Tuple. Se non c'è nulla da emettere, questo metodo deve restituire senza emettere alcun elemento.

I metodi **NextTuple**, **ACK**e **Fail** vengono chiamati in un ciclo ridotto in un singolo thread di un C# processo. Quando non sono presenti tuple da emettere, **NextTuple** sospensione per un breve periodo di tempo, ad esempio 10 millisecondi. Questa sospensione consente di evitare la perdita della disponibilità della CPU.

I metodi **ACK** e **Fail** vengono chiamati solo quando un file di specifica Abilita il meccanismo di riconoscimento. Il parametro *seqId* identifica la tupla riconosciuta o non riuscita. Se il riconoscimento è abilitato in una topologia non transazionale, è necessario usare la funzione **Emit** seguente in un oggetto beccuccio:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Se una topologia non transazionale non supporta il riconoscimento, **ACK** e **Fail** possono essere lasciati come funzioni vuote.

Il parametro di input *parametri* in queste funzioni specifica un dizionario vuoto ed è riservato per un utilizzo futuro.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** è l'interfaccia per un Bolt non transazionale.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Quando è disponibile una nuova tupla, la funzione **Execute** viene chiamata per elaborarla.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** è l'interfaccia per un beccuccio transazionale.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Così come le controparti non transazionali, **NextTx**, **ACK**e **Fail** vengono tutti chiamati in un ciclo limitato in un singolo thread di un C# processo. Quando non sono presenti tuple da emettere, **NextTx** sospensione per un breve periodo di tempo, ad esempio 10 millisecondi. Questa sospensione consente di evitare la perdita della disponibilità della CPU.

Quando **NextTx** viene chiamato per avviare una nuova transazione, il parametro di output *seqId* identifica la transazione. La transazione viene utilizzata anche in **ACK** e **non riesce**. Il metodo **NextTx** può emettere dati sul lato Java. I dati vengono archiviati in ZooKeeper a supporto della riproduzione. Poiché ZooKeeper ha una capacità limitata, il codice deve emettere solo metadati e non dati in blocco in un beccuccio transazionale.

Poiché Storm riproduce automaticamente una **transazione non riuscita, non** è in genere possibile chiamare. Tuttavia, se SCP è in grado di controllare i metadati emessi da un beccuccio transazionale, può chiamare **Fail** quando i metadati non sono validi.

Il parametro di input *parametri* in queste funzioni specifica un dizionario vuoto ed è riservato per un utilizzo futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** è l'interfaccia per un Bolt transazionale.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Il metodo **Execute** viene chiamato quando una nuova tupla arriva al Bolt. Il metodo **FinishBatch** viene chiamato al termine della transazione. Il parametro di input *parametri* è riservato per un utilizzo futuro.

Per una topologia transazionale, **StormTxAttempt** è una classe importante. Sono presenti due membri: **ID transazione** e **AttemptId**. Il membro **ID transazione** identifica una transazione specifica. Una transazione può essere tentata più volte in caso di errore e di riproduzione.

SCP.NET crea un nuovo oggetto **ISCPBatchBolt** per elaborare ciascun oggetto **StormTxAttempt** , esattamente come accade con Storm in Java. Lo scopo di questa progettazione è supportare l'elaborazione parallela delle transazioni. Al termine di un tentativo di transazione, l'oggetto **ISCPBatchBolt** corrispondente viene eliminato e sottoposta a Garbage Collection.

## <a name="object-model"></a>Modello a oggetti

In SCP.NET è disponibile un semplice set di oggetti chiave che gli sviluppatori possono usare per la programmazione. Gli oggetti sono **context**, **StateStore**e **SCPRuntime**. Sono illustrati in questa sezione.

### <a name="context"></a>Context

L'oggetto **context** fornisce un ambiente in esecuzione a un'applicazione. Ogni istanza di **ISCPPlugin** di **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**o **ISCPBatchBolt** dispone di un'istanza del **contesto** corrispondente. La funzionalità fornita dal **contesto** è divisa in queste due parti:

* Parte statica, disponibile nell'intero C# processo
* La parte dinamica, disponibile solo per l'istanza di **contesto** specifica

### <a name="static-part"></a>Parte statica

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

L'oggetto **logger** viene fornito a scopo di registrazione.

L'oggetto **plug-** indica il tipo di plug-in del C# processo. Se il processo viene eseguito in modalità di test locale senza Java, il tipo di plug-in viene **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

La proprietà **config** ottiene i parametri di configurazione dal lato Java, che li passa quando C# viene inizializzato un plug-in. I parametri di **configurazione** sono divisi in due parti: **stormConf** e **ottenendo pluginconf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

La parte **stormConf** è relativa ai parametri definiti da Storm e la parte **ottenendo pluginconf** è relativa ai parametri definiti da SCP. Ad esempio:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

Il tipo **TopologyContext** ottiene il contesto della topologia. È particolarmente utile per più componenti paralleli. Ad esempio:

```csharp
//demo how to get TopologyContext info
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
```

### <a name="dynamic-part"></a>Parte dinamica

Le interfacce seguenti sono attinenti a una determinata istanza del **contesto** , creata dalla piattaforma SCP.NET e passata al codice:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Per un beccuccio non transazionale che supporta il riconoscimento, viene fornito il seguente metodo:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Un Bolt non transazionale che supporta il riconoscimento deve chiamare in modo esplicito **ACK** o **avere esito negativo** con la tupla ricevuta. Quando si emette una nuova tupla, il Bolt deve anche specificare gli ancoraggi della tupla. Sono disponibili i metodi seguenti:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

L'oggetto **StateStore** fornisce servizi di metadati, generazione di sequenze monotone e coordinamento senza attesa. È possibile creare astrazioni di concorrenza distribuite di livello superiore in **StateStore**. Queste astrazioni includono blocchi distribuiti, code distribuite, barriere e servizi di transazione.

Le applicazioni SCP possono usare l'oggetto **stato** per serializzare le informazioni in [Apache ZooKeeper](https://zookeeper.apache.org/). Questa possibilità è particolarmente utile per una topologia transazionale. Se un becco transazionale smette di rispondere e viene riavviato, lo **stato** può recuperare le informazioni necessarie da ZooKeeper e riavviare la pipeline.

L'oggetto **StateStore** dispone di questi metodi principali:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

Per l'oggetto **state** sono disponibili i metodi principali seguenti:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Quando **simpleMode** è impostato su **true**, il metodo **commit** Elimina il elemento znode corrispondente in ZooKeeper. In caso contrario, il metodo elimina il elemento znode corrente e aggiunge un nuovo nodo nel percorso di\_di cui è stato eseguito il commit.

### <a name="scpruntime"></a>SCPRuntime

La classe **SCPRuntime** fornisce i due metodi seguenti:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Il metodo **Initialize** Inizializza l'ambiente di runtime SCP. In questo metodo, il C# processo si connette al lato Java per ottenere i parametri di configurazione e il contesto della topologia.

Il metodo **LaunchPlugin** avvia il ciclo di elaborazione dei messaggi. In questo ciclo, il C# plug-in riceve messaggi dal lato Java. Questi messaggi includono Tuple e segnali di controllo. Il plug-in elabora quindi i messaggi, probabilmente chiamando il metodo di interfaccia fornito dal codice.

Il parametro di input per **LaunchPlugin** è un delegato. Il metodo può restituire un oggetto che implementa l'interfaccia **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**o **ISCPBatchBolt** .

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Per **ISCPBatchBolt**, è possibile ottenere un oggetto **StormTxAttempt** dal parametro *parametri* e utilizzarlo per valutare se il tentativo è un tentativo di riproduzione. Il controllo di un tentativo di riproduzione viene spesso eseguito nel Bolt di commit. Nell'esempio HelloWorldTx più avanti in questo articolo viene illustrato questo controllo.

I plug-in SCP possono in genere essere eseguiti in due modalità: la modalità test locale e la modalità normale.

#### <a name="local-test-mode"></a>Modalità di test locale

In questa modalità, i plug-in SCP nel C# codice vengono eseguiti in Visual Studio durante la fase di sviluppo. In questa modalità è possibile utilizzare l'interfaccia **ILocalContext** . L'interfaccia fornisce metodi per serializzare le tuple emesse in file locali e leggerle nuovamente nella RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Modalità normale

In questa modalità, il processo di Storm Java esegue i plug-in SCP. Ecco un esempio:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Lingua specifica della topologia

SCP topologia Specification è un linguaggio specifico di dominio (DSL) per la descrizione e la configurazione di topologie SCP. Si basa sul [linguaggio DSL Clojure di Storm](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) ed è esteso da SCP.

È possibile inviare le specifiche della topologia direttamente a un cluster Storm per l'esecuzione tramite il comando **runSpec** .

SCP.NET ha aggiunto le funzioni seguenti per definire le topologie transazionali:

| Nuova funzione | Parametri | Descrizione |
| --- | --- | --- |
| **tx-topolopy** |*nome topologia*<br />*beccuccio-mappa*<br />*Mappa Bolt* |Definisce una topologia transazionale con il nome della topologia, la mappa di definizione dei beccucci e la mappa di definizione dei Bolt. |
| **scp-tx-spout** |*Exec-Name*<br />*args*<br />*fields* |Definisce un beccuccio transazionale. La funzione esegue l'applicazione specificata da *Exec-Name* e USA *args*.<br /><br />Il parametro *Fields* specifica i campi di output per il beccuccio. |
| **scp-tx-batch-bolt** |*Exec-Name*<br />*args*<br />*fields* |Definisce un Bolt di batch transazionale. La funzione esegue l'applicazione specificata da *Exec-Name* e USA *args.*<br /><br />Il parametro *Fields* specifica i campi di output per il Bolt. |
| **scp-tx-commit-bolt** |*Exec-Name*<br />*args*<br />*fields* |Definisce un Bolt di commit transazionale. La funzione esegue l'applicazione specificata da *Exec-Name* e USA *args*.<br /><br />Il parametro *Fields* specifica i campi di output per il Bolt. |
| **nontx-topologia** |*nome topologia*<br />*beccuccio-mappa*<br />*Mappa Bolt* |Definisce una topologia non transazionale con il nome della topologia, la mappa di definizione dei beccucci e la mappa di definizione dei Bolt. |
| **scp-spout** |*Exec-Name*<br />*args*<br />*fields*<br />*parameters* |Definisce un beccuccio non transazionale. La funzione esegue l'applicazione specificata da *Exec-Name* e USA *args*.<br /><br />Il parametro *Fields* specifica i campi di output per il beccuccio.<br /><br />Il parametro *Parameters* è facoltativo. Usarlo per specificare parametri come "non transazionale. ACK. Enabled". |
| **scp-bolt** |*Exec-Name*<br />*args*<br />*fields*<br />*parameters* |Definisce un Bolt non transazionale. La funzione esegue l'applicazione specificata da *Exec-Name* e USA *args*.<br /><br />Il parametro *Fields* specifica i campi di output per il Bolt<br /><br />Il parametro *Parameters* è facoltativo. Usarlo per specificare parametri come "non transazionale. ACK. Enabled". |

SCP.NET definisce le parole chiave seguenti:

| Parola chiave | Descrizione |
| --- | --- |
| **:name** |Nome della topologia |
| **:topology** |Topologia che usa le funzioni della tabella precedente e delle funzioni predefinite |
| **:p** |Hint di parallelismo per ogni beccuccio o Bolt |
| **:config** |Indica se configurare i parametri o aggiornare quelli esistenti |
| **:schema** |Schema del flusso |

SCP.NET definisce anche questi parametri usati di frequente:

| Parametro | Descrizione |
| --- | --- |
| "plugin.name" |Nome del file exe del C# plug-in |
| "plugin. args" |Argomenti del plug-in |
| "output. Schema" |Schema di output |
| "non transazionale. ACK. Enabled" |Indica se il riconoscimento è abilitato per una topologia non transazionale |

Il comando **runSpec** viene distribuito insieme ai bit. Ecco l'utilizzo del comando:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Il parametro *Resource-dir* è facoltativo. Specificarlo quando si vuole collegare un' C# applicazione. La directory specificata contiene l'applicazione, le dipendenze e le configurazioni.

Anche il parametro *classpath* è facoltativo. Specifica il CLASSPATH Java se il file di specifica contiene un beccuccio o un Bolt Java.

## <a name="miscellaneous-features"></a>Funzionalità varie

### <a name="input-and-output-schema-declarations"></a>Dichiarazioni dello schema di input e di output

I C# processi possono generare Tuple. A tale scopo, la piattaforma serializza le tuple in oggetti **byte []** e trasferisce gli oggetti sul lato Java. Storm trasferisce quindi queste Tuple alle destinazioni.

Nei componenti downstream, C# i processi ricevono le tuple dal lato Java e le convertono nei tipi originali della piattaforma. Tutte queste operazioni sono nascoste dalla piattaforma.

Per supportare la serializzazione e la deserializzazione, il codice deve dichiarare lo schema dell'input e dell'output. Lo schema è definito come dizionario. L'ID del flusso è la chiave del dizionario. Il valore della chiave è costituito dai tipi delle colonne. Un componente può dichiarare più flussi.

```csharp
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
```

La funzione seguente viene aggiunta a un oggetto **context** :

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Gli sviluppatori devono assicurarsi che le tuple emesse rispettino lo schema definito per un flusso. In caso contrario, il sistema genererà un'eccezione in fase di esecuzione.

### <a name="multistream-support"></a>Supporto di più flussi

SCP consente l'emissione o la ricezione di più flussi distinti contemporaneamente da parte del codice. L'oggetto **context** riflette questo supporto come parametro dell'ID di flusso facoltativo del metodo **Emit** .

Sono stati aggiunti due metodi nell'oggetto **contesto** SCP.NET. Emettono una o più tuple in flussi specifici. Il parametro *StreamId* è una stringa. Il valore deve essere lo stesso sia C# nel codice che nella specifica della definizione della topologia.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

L'emissione in un flusso inesistente genera eccezioni di Runtime.

### <a name="fields-grouping"></a>Raggruppamento di campi

Il raggruppamento dei campi predefiniti in Storm non funziona correttamente in SCP.NET. Sul lato proxy Java, il tipo di dati di tutti i campi è effettivamente **byte []** . Il raggruppamento dei campi utilizza il codice hash dell'oggetto **byte []** per eseguire il raggruppamento. Il codice hash è l'indirizzo di questo oggetto in RAM. Pertanto, il raggruppamento non sarà corretto per gli oggetti multibyte che condividono lo stesso contenuto ma non lo stesso indirizzo.

SCP.NET aggiunge un metodo di raggruppamento personalizzato e usa il contenuto dell'oggetto **byte []** per eseguire il raggruppamento. In un file di specifica, la sintassi è simile a questo esempio:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

Nel file di specifica precedente:

* `scp-field-group` specifica che il raggruppamento è un raggruppamento di campi personalizzato implementato da SCP.
* `:tx` o `:non-tx` specifica se la topologia è transazionale. Queste informazioni sono necessarie perché l'indice iniziale è diverso tra le topologie transazionali e non transazionali.
* `[0,1]` specifica un set di hash di ID campo che iniziano con zero.

### <a name="hybrid-topology"></a>Topologia ibrida

Il codice Storm nativo è scritto in Java. SCP.NET ha avanzato Storm per consentire di scrivere C# codice per gestire la logica di business. Tuttavia, SCP.NET supporta anche le topologie ibride, che C# contengono non solo bocchette/Bolt, ma anche beccucci Java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Specifica il beccuccio Java/Bolt in un file di specifica

È possibile usare **SCP-beccuccio** e **SCP-Bolt** in un file di specifica per specificare i beccucci e i Bolt Java. Ad esempio:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Qui `microsoft.scp.example.HybridTopology.Generator` è il nome della classe del beccuccio Java.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Specificare il CLASSPATH Java in un comando runSpec

Se si vuole inviare la topologia che contiene i beccucci o i Bolt Java, compilarli prima per produrre file con estensione JAR. Specificare quindi il CLASSPATH Java che contiene i file JAR quando si invia la topologia. Ad esempio:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Qui, `examples\HybridTopology\java\target\` è la cartella che contiene il file JAR di Java beccucci/Bolt.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializzazione e deserializzazione tra Java eC#

Un componente SCP include il lato Java e il C# lato. Per interagire con i beccucci/Bolt Java nativi, la serializzazione e la deserializzazione devono essere eseguite tra il lato C# Java e il lato, come illustrato nel grafico seguente:

![Diagramma del componente Java che invia il componente SCP, che quindi invia a un altro componente Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serializzazione sul lato Java e deserializzazione sul C# lato

Per prima cosa, fornire l'implementazione predefinita per la serializzazione sul lato Java e la C# deserializzazione sul lato.

Specificare il metodo di serializzazione del lato Java in un file di specifica.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Specificare il C# metodo di deserializzazione del lato nel C# codice.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Se il tipo di dati non è troppo complesso, questa implementazione predefinita deve gestire la maggior parte dei casi. Di seguito sono riportati i casi in cui è possibile inserire un'implementazione personalizzata:

* Il tipo di dati è troppo complesso per l'implementazione predefinita.
* Le prestazioni dell'implementazione predefinita non soddisfano i requisiti.

L'interfaccia di serializzazione sul lato Java è definita come segue:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

L'interfaccia di deserializzazione sul C# lato è definita come segue:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serializzazione sul C# lato e deserializzazione sul lato Java

Specificare il C# metodo di serializzazione del lato C# nel codice.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Specificare il metodo di deserializzazione del lato Java in un file di specifica.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Qui, `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` è il nome del deserializzatore e `"microsoft.scp.example.HybridTopology.Person"` è la classe di destinazione in cui vengono deserializzati i dati.

È anche possibile collegare l'implementazione di un C# serializzatore e un deserializzatore Java.

Questo codice è l'interfaccia per il C# serializzatore:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Questo codice è l'interfaccia per il deserializzatore Java:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Modalità host SCP

In questa modalità è possibile compilare il codice come DLL e usare SCPHost. exe come fornito da SCP per inviare una topologia. Un file di specifica ha un aspetto simile al seguente:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Qui, `"plugin.name"` viene specificato come `"SCPHost.exe"`, fornito da SCP SDK. SCPHost. exe accetta tre parametri nell'ordine seguente:

1. Nome della DLL, `"HelloWorld.dll"` in questo esempio.
1. Il nome della classe, che è `"Scp.App.HelloWorld.Generator"` in questo esempio.
1. Nome di un metodo statico pubblico, che può essere richiamato per ottenere un'istanza di **ISCPPlugin**.

In modalità host compilare il codice come DLL per la chiamata della piattaforma SCP. Poiché la piattaforma può quindi ottenere il controllo completo dell'intera logica di elaborazione, si consiglia di inviare la topologia in modalità host SCP. In questo modo viene semplificata l'esperienza di sviluppo. Offre inoltre maggiore flessibilità e una migliore compatibilità con le versioni precedenti.

## <a name="scp-programming-examples"></a>Esempi di programmazione SCP

### <a name="helloworld"></a>HelloWorld

Nell'esempio HelloWorld semplice riportato di seguito viene illustrato un SCP.NET. Usa una topologia non transazionale con un beccuccio denominato **Generator** e due Bolt chiamati **splitter** e **Counter**. Il beccuccio del **Generatore** genera in modo casuale le frasi ed emette le frasi per la **barra di divisione**. Il Bolt **splitter** divide le frasi in parole ed emette queste parole nel Bolt del **contatore** . Il Bolt del **contatore** usa un dizionario per registrare l'occorrenza di ogni parola.

Questo esempio include due file di specifica: HelloWorld. spec e HelloWorld\_EnableAck. spec. Il C# codice può determinare se il riconoscimento è abilitato ottenendo l'oggetto `pluginConf` dal lato Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Se il riconoscimento è abilitato nel beccuccio, un dizionario memorizza nella cache le tuple che non sono state riconosciute. Se `Fail` viene chiamato, viene rieseguita la tupla non riuscita.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

Nell'esempio HelloWorldTx seguente viene illustrato come implementare la topologia transazionale. Nell'esempio è presente un beccuccio denominato **Generator**, un Bolt batch denominato **partial-count**e un Bolt di commit denominato **count-Sum**. L'esempio include anche tre file di testo esistenti: DataSource0. txt, DataSource1. txt e DataSource2. txt.

In ogni transazione, il beccuccio del **Generatore** seleziona in modo casuale due file dai tre file esistenti e genera i due nomi di file al Bolt del **conteggio parziale** . Bolt **conteggio parziale** :

1. Ottiene un nome file dalla tupla ricevuta.
1. Apre il file corrispondente.
1. Conta il numero di parole nel file.
1. Emette il conteggio delle parole per il Bolt **count-Sum** .

Il bolt **count-sum** riepiloga il conteggio totale.

Per ottenere una semantica esattamente una volta, il Bolt di commit **count-Sum** deve valutare se si tratta di una transazione rieseguita. In questo esempio è presente la variabile membro statica seguente:

```csharp
public static long lastCommittedTxId = -1; 
```

Quando viene creata un'istanza di **ISCPBatchBolt** , ottiene il valore dell'oggetto `txAttempt` dai parametri di input.

```csharp
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
```

Quando viene chiamato `FinishBatch`, `lastCommittedTxId` viene aggiornato se non si tratta di una transazione rieseguita.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Questa topologia contiene un beccuccio Java C# e un Bolt. Usa l'implementazione di serializzazione e deserializzazione predefinita fornita dalla piattaforma SCP. Vedere il file HybridTopology. spec nell'esempio\\cartella HybridTopology per i dettagli del file di specifica. Vedere anche SubmitTopology. bat per informazioni su come specificare il CLASSPATH Java.

### <a name="scphostdemo"></a>SCPHostDemo

Questo esempio è in sostanza identico a HelloWorld. L'unica differenza è che il codice viene compilato come una DLL e la topologia viene inviata tramite SCPHost. exe. Per una spiegazione più dettagliata, vedere la sezione modalità host SCP.

## <a name="next-steps"></a>Passaggi successivi

Per esempi di Apache Storm topologie create con SCP, vedere gli articoli seguenti:

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Elaborare eventi di Hub eventi di Azure con Apache Storm in HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Elaborare i dati del sensore veicolo da Hub eventi di Azure usando Apache Storm in HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Estrarre, trasformare e caricare (ETL) da Hub eventi di Azure ad Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
