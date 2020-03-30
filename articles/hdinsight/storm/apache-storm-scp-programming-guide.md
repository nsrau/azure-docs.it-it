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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186870"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Guida alla programmazione SCP per Apache Storm in Azure HDInsight

SCP è una piattaforma per la creazione di applicazioni di elaborazione dati in tempo reale, affidabili, coerenti e ad alte prestazioni. E 'costruito sulla cima di [Apache Storm](https://storm.incubator.apache.org/), che è un sistema di elaborazione del flusso progettato da comunità di software open-source. Nathan Marz creò Storm. È stato pubblicato come open source da Twitter. Storm utilizza [Apache zooKeeper](https://zookeeper.apache.org/), che è un altro progetto Apache che consente un coordinamento distribuito altamente affidabile e la gestione dello stato.

Il progetto SCP ha portato non solo Storm su Windows, ma anche le estensioni aggiunte dal progetto e la personalizzazione per l'ambiente Windows. Le estensioni includono l'esperienza di sviluppo .NET e le librerie .NET. La personalizzazione include la distribuzione basata su Windows.The customization includes Windows-based deployment.

Con le estensioni e la personalizzazione, non è necessario fork i progetti software open-source. È possibile utilizzare ambienti derivati che sono costruiti sulla parte superiore di Tempesta.

## <a name="processing-model"></a>Modello di elaborazione

In dati in SCP vengono modellati come flussi continui di tuple. In genere, le tuple:

1. Flusso in una coda.
1. Vengono prelevati e trasformati dalla logica di business ospitata all'interno di una topologia Storm.
1. L'output viene reindirizzato come tuple in un altro sistema SCP oppure viene eseguito il commit in archivi come file system distribuiti e database come SQL Server.

![Diagramma di una coda che alimenta i dati all'elaborazione, che a sua volta alimenta un archivio dati](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

In Storm, una topologia di applicazione definisce un grafico di calcolo. Ogni nodo in una topologia contiene la logica di elaborazione. I collegamenti tra i nodi indicano il flusso di dati.

I nodi che inseriscono dati di input nella topologia sono _denominati spout_. È possibile utilizzarli per sequenziare i dati. I dati di input possono provenire da un'origine come i log dei file, un database transazionale o un contatore delle prestazioni del sistema.

I nodi con flussi di dati di input e di output sono _denominati bulloni_. Eseguire il filtraggio dei dati effettivi, le selezioni e l'aggregazione.

SCP supporta i migliori sforzi, almeno una volta, ed esattamente una volta l'elaborazione dei dati.

In un'applicazione di elaborazione di flussi distribuita, potrebbero verificarsi errori durante l'elaborazione dei dati. Tali errori includono un'interruzione della rete, un errore del computer o un errore nel codice. L'elaborazione at-least-once garantisce che tutti i dati vengano elaborati almeno una volta riproducendo automaticamente gli stessi dati quando si verifica un errore.

L'elaborazione at-least-once è semplice e affidabile e si adatta a molte applicazioni. Tuttavia, quando un'applicazione richiede il conteggio esatto, l'elaborazione di almeno una volta è insufficiente perché gli stessi dati potrebbero essere riprodotti nella topologia dell'applicazione. In tal caso, esattamente una volta che l'elaborazione assicura che il risultato sia corretto anche quando i dati vengono riprodotti ed elaborati più volte.

SCP consente agli sviluppatori .NET di creare applicazioni di elaborazione dati in tempo reale durante l'utilizzo di una Java Virtual Machine (JVM) con Storm. Una JVM e .NET comunicano tramite socket locali TCP. Ogni spout/bolt è una coppia di processi .NET/Java, in cui la logica utente viene eseguita in un processo .NET come plug-in.

Per compilare un'applicazione di elaborazione dati su SCP, attenersi alla seguente procedura:

1. Progettare e implementare spout per eseguire il pull dei dati dalle code.
1. Progettare e implementare bulloni che elaborano i dati di input e li salvano in archivi esterni come un database.
1. Progettare la topologia, quindi inviarla ed eseguirla.

La topologia definisce i vertici e i dati che passano tra di essi. SCP accetta una specifica di topologia e la distribuisce in un cluster Storm, in cui ogni vertice viene eseguito su un nodo logico. L'utilità di pianificazione Storm si occupa di failover e ridimensionamento.

Questo articolo usa alcuni semplici esempi per illustrare come creare applicazioni di elaborazione dati con SCP.

## <a name="scp-plug-in-interface"></a>Interfaccia plug-in SCP

I plug-in SCP sono applicazioni autonome. Possono essere eseguiti all'interno di Visual Studio durante lo sviluppo ed essere collegati alla pipeline Storm dopo la distribuzione di produzione.

La scrittura di un plug-in SCP equivale a scrivere qualsiasi altra applicazione console di Windows. La piattaforma SCP.NET dichiara alcune interfacce per spout/bolt. Il codice del plug-in implementa queste interfacce. Lo scopo principale di questa progettazione è quello di consentire di concentrarsi sulla logica di business lasciando che la piattaforma SCP.NET gestire altre cose.

Il codice del plug-in implementa una delle interfacce seguenti. L'interfaccia dipende dal fatto che la topologia sia transazionale o non transazionale e che il componente sia un beccuccio o un bullone.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** è l'interfaccia comune per molti plug-in. Attualmente, è un'interfaccia fittizia.

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

Quando **NextTuple** viene chiamato, il codice C , può generare una o più tuple. Se non c'è nulla da emettere, questo metodo dovrebbe tornare senza emettere nulla.

I metodi **NextTuple**, **Ack**e **Fail** vengono tutti chiamati in un ciclo stretto in un singolo thread di un processo in C. Quando non sono presenti tuple da generare, avere **NextTuple** sospensione per un breve periodo di tempo come 10 millisecondi. Questa sospensione consente di evitare di sprecare la disponibilità della CPU.

I metodi **Ack** e **Fail** vengono chiamati solo quando un file di specifica abilita il meccanismo di riconoscimento. Il parametro *seqId* identifica la tupla riconosciuta o non riuscita. Se il riconoscimento è abilitato in una topologia non transazionale, la seguente funzione **Emit** deve essere utilizzata in un beccuccio:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Se una topologia non transazionale non supporta il riconoscimento, **Ack** e **Fail** possono essere lasciati come funzioni vuote.

Il parametro di input *parms* in queste funzioni specifica un dizionario vuoto ed è riservato per un utilizzo futuro.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** è l'interfaccia per un bullone non transazionale.

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

Proprio come le loro controparti non transazionali, **NextTx**, **Ack**e **Fail** vengono tutte chiamate in un ciclo stretto in un unico thread di un processo in C. Quando non sono presenti tuple da emettere, avere **sospensione NextTx** per un breve periodo di tempo come 10 millisecondi. Questa sospensione consente di evitare di sprecare la disponibilità della CPU.

Quando **NextTx** viene chiamato per avviare una nuova transazione, il parametro di output *seqId* identifica la transazione. La transazione viene utilizzata anche in **Ack** e **Fail**. Il metodo **NextTx** può generare dati sul lato Java. I dati vengono archiviati in ZooKeeper a supporto della riproduzione. Dal caso in cui la capacità è limitata, il codice deve generare solo metadati e non dati in blocco in uno spout transazionale.

Poiché Storm riproduce automaticamente una transazione non riuscita, in genere non viene chiamato **Fail.** Ma se SCP può controllare i metadati generati da un spout transazionale, può chiamare **Fail** quando i metadati non sono validi.

Il parametro di input *parms* in queste funzioni specifica un dizionario vuoto ed è riservato per un utilizzo futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** è l'interfaccia per un bullone transazionale.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Il **Execute** metodo viene chiamato quando una nuova tupla arriva al bullone. Il **FinishBatch** metodo viene chiamato al termine della transazione. Il parametro di input *dei parms* è riservato per un utilizzo futuro.

Per una topologia transazionale, **StormTxAttempt** è una classe importante. Dispone di due membri: **TxId** e **AttemptId**. Il membro **TxId** identifica una transazione specifica. Una transazione potrebbe essere tentata più volte se ha esito negativo e viene riprodotta.

SCP.NET crea un nuovo oggetto **ISCPBatchBolt** per elaborare ogni oggetto **StormTxAttempt,** proprio come fa Storm in Java. Lo scopo di questa progettazione è supportare l'elaborazione delle transazioni parallele. Al termine di un tentativo di transazione, l'oggetto **ISCPBatchBolt** corrispondente viene eliminato e viene sottoposto a Garbage Collection.

## <a name="object-model"></a>Modello a oggetti

In SCP.NET è disponibile un semplice set di oggetti chiave che gli sviluppatori possono usare per la programmazione. Gli oggetti sono **Context**, **StateStore**e **SCPRuntime**. Sono discussi in questa sezione.

### <a name="context"></a>Context

Il **Context** oggetto fornisce un ambiente di esecuzione a un'applicazione. Ogni istanza **ISCPPlugin** di **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**o **ISCPBatchBolt** dispone di un'istanza **di contesto** corrispondente. La funzionalità fornita da **Context** è suddivisa in queste due parti:

* La parte statica, che è disponibile nell'intero processo in C
* La parte dinamica, disponibile solo per l'istanza **Di contesto** specifica

### <a name="static-part"></a>Parte statica

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

L'oggetto **Logger** viene fornito a scopo di registrazione.

L'oggetto **pluginType** indica il tipo di plug-in del processo in C. Se il processo viene eseguito in modalità di test locale senza Java, il tipo di plug-in viene **SCP_NET_LOCAL**.

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

La proprietà **Config** ottiene i parametri di configurazione dal lato Java, che li passa quando viene inizializzato un plug-in di C. I parametri **Config** sono divisi in due parti: **stormConf** e **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

La parte **stormConf** è parametri definiti da Storm, e la parte **pluginConf** è parametri definiti da SCP. Ad esempio:

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

Le interfacce seguenti sono pertinenti a una determinata istanza di **Context,** che viene creata dalla piattaforma SCP.NET e passata al codice:The following interfaces aretinent to a certain Context instance, which is created by the SCP.NET platform and passed to your code:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Per uno spout non transazionale che supporta il riconoscimento, viene fornito il metodo seguente:For a nontransactional spout that supports acknowledgment, the following method is provided:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Un bullone non transazionale che supporta il riconoscimento deve chiamare in modo esplicito **Ack** o **Fail** con la tupla ricevuta. Quando si genera una nuova tupla, il bullone deve specificare anche gli ancoraggi della tupla. Sono disponibili i seguenti metodi:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**L'oggetto StateStore** fornisce servizi di metadati, generazione di sequenze monotoniche e coordinamento senza attesa. È possibile creare astrazioni di concorrenza distribuita di livello superiore in **StateStore**. Queste astrazioni includono blocchi distribuiti, code distribuite, barriere e servizi di transazione.

Le applicazioni SCP possono utilizzare l'oggetto **State** per serializzare le informazioni in [Apache .](https://zookeeper.apache.org/) Questa funzionalità è particolarmente utile per una topologia transazionale. Se un beccuccio transazionale smette di rispondere e si riavvia, **lo stato** può recuperare le informazioni necessarie da e riavviare la pipeline.

**L'oggetto StateStore** dispone di questi metodi principali:The StateStore object has these principal methods:

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

**L'oggetto State** dispone di questi metodi principali:The State object has these principal methods:

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

Quando **simpleMode** è impostato su **true**, il metodo **Commit** consente di eliminare l'oggetto corrispondente di oggetto in . In caso contrario, il metodo elimina l'oggetto\_corrente di Nodo e aggiunge un nuovo nodo nel percorso COMMITTED.

### <a name="scpruntime"></a>SCPRuntime

La classe **SCPRuntime** fornisce i due metodi seguenti:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Il **Initialize** metodo inizializza l'ambiente di runtime SCP. In questo metodo, il processo di C ' si connette al lato Java per ottenere i parametri di configurazione e il contesto della topologia.

Il **LaunchPlugin** metodo avvia il ciclo di elaborazione dei messaggi. In questo ciclo, il plug-in di C ' riceve messaggi dal lato Java. Questi messaggi includono tuple e segnali di controllo. Il plug-in elabora quindi i messaggi, ad esempio chiamando il metodo di interfaccia fornito dal codice.

Il parametro di input per **LaunchPlugin** è un delegato. Il metodo può restituire un oggetto che implementa l'interfaccia **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**o **ISCPBatchBolt** .

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Per **ISCPBatchBolt**, è possibile ottenere un oggetto **StormTxAttempt** dal parametro *parms* e utilizzarlo per valutare se il tentativo è un tentativo di riproduzione. Il controllo per un tentativo di riproduzione viene spesso eseguito al bullone di commit. Nell'esempio HelloWorldTx riportato più avanti in questo articolo viene illustrato questo controllo.

I plug-in SCP possono in genere essere eseguiti in due modalità: modalità di test locale e modalità normale.

#### <a name="local-test-mode"></a>Modalità test locale

In questa modalità, i plug-in SCP nel codice C , vengono eseguiti all'interno di Visual Studio durante la fase di sviluppo. È possibile utilizzare l'interfaccia **ILocalContext** in questa modalità. L'interfaccia fornisce metodi per serializzare le tuple generate in file locali e leggerle nuovamente nella RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Modalità normale

In questa modalità, il processo Di Storm Java esegue i plug-in SCP. Di seguito è riportato un esempio:Here's an example:

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

## <a name="topology-specification-language"></a>Linguaggio di specifica della topologia

SCP Topology Specification è un linguaggio specifico di dominio (DSL) per la descrizione e la configurazione delle topologie SCP. Si basa sul [DSL Clojure di Storm](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) ed è esteso da SCP.

È possibile inviare le specifiche della topologia direttamente a un cluster Storm per l'esecuzione tramite il comando **runSpec.**

SCP.NET sono state aggiunte le seguenti funzioni per definire le topologie transazionali:

| Nuova funzione | Parametri | Descrizione |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |Definisce una topologia transazionale con il nome della topologia, la mappa di definizione dei spout e la mappa di definizione dei bulloni. |
| **scp-tx-spout** |*exec-name*<br />*Args*<br />*campi* |Definisce un beccuccio transazionale. La funzione esegue l'applicazione specificata da *exec-name* e utilizza *args*.<br /><br />Il parametro *fields* consente di specificare i campi di output per lo spout. |
| **scp-tx-batch-bolt** |*exec-name*<br />*Args*<br />*campi* |Definisce un bullone batch transazionale. La funzione esegue l'applicazione specificata da *exec-name* e utilizza *args.*<br /><br />Il parametro *fields* consente di specificare i campi di output per il bullone. |
| **scp-tx-commit-bolt** |*exec-name*<br />*Args*<br />*campi* |Definisce un bullone di commit transazionale. La funzione esegue l'applicazione specificata da *exec-name* e utilizza *args*.<br /><br />Il parametro *fields* consente di specificare i campi di output per il bullone. |
| **nontx-topology (topologia non tx)** |*topology-name*<br />*spout-map*<br />*bolt-map* |Definisce una topologia non transazionale con il nome della topologia, la mappa di definizione dei beccucci e la mappa di definizione dei bulloni. |
| **scp-spout** |*exec-name*<br />*Args*<br />*campi*<br />*Parametri* |Definisce uno spout non transazionale. La funzione esegue l'applicazione specificata da *exec-name* e utilizza *args*.<br /><br />Il parametro *fields* consente di specificare i campi di output per lo spout.<br /><br />Il parametro *parameters* è facoltativo. Utilizzarlo per specificare parametri come "nontransactional.ack.enabled". |
| **scp-bolt** |*exec-name*<br />*Args*<br />*campi*<br />*Parametri* |Definisce un bullone non transazionale. La funzione esegue l'applicazione specificata da *exec-name* e utilizza *args*.<br /><br />Il parametro *fields* consente di specificare i campi di output per il bullone<br /><br />Il parametro *parameters* è facoltativo. Utilizzarlo per specificare parametri come "nontransactional.ack.enabled". |

SCP.NET definisce le seguenti parole chiave:

| Parola chiave | Descrizione |
| --- | --- |
| **:nome** |Il nome della topologia |
| **:topology (topologia)** |La topologia che utilizza le funzioni della tabella precedente e le funzioni incorporate |
| **:p** |L'hint di parallelismo per ogni beccuccio o bullone |
| **:config** |Se configurare i parametri o aggiornare quelli esistenti |
| **:schema (schema)** |Lo schema del flusso |

SCP.NET definisce anche questi parametri utilizzati di frequente:

| Parametro | Descrizione |
| --- | --- |
| "plugin.name" |Il nome del file con estensione exe del plug-in Di C |
| "plugin.args" |Gli argomenti del plug-in |
| "output.schema" |Lo schema di output |
| "nontransactional.ack.enabled" |Se il riconoscimento è abilitato per una topologia non transazionale |

Il comando **runSpec** viene distribuito insieme ai bit. Ecco l'utilizzo del comando:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Il parametro *resource-dir* è facoltativo. Specificarlo quando si desidera collegare un'applicazione in C. La directory specificata contiene l'applicazione, le dipendenze e le configurazioni.

Anche il parametro *classpath* è facoltativo. Specifica il percorso di classe Java se il file di specifica contiene un beccuccio o un bullone Java.

## <a name="miscellaneous-features"></a>Caratteristiche varie

### <a name="input-and-output-schema-declarations"></a>Dichiarazioni dello schema di input e outputInput and output schema declarations

I processi di C'è possono generare tuple. A tale scopo, la piattaforma serializza le tuple in **byte[]** oggetti e trasferisce gli oggetti al lato Java. Storm trasferisce quindi queste tuple agli obiettivi.

Nei componenti downstream, i processi di C , ricevono tuple dal lato Java e li convertono nei tipi originali della piattaforma. Tutte queste operazioni sono nascoste dalla piattaforma.

Per supportare la serializzazione e la deserializzazione, il codice deve dichiarare lo schema dell'input e dell'output. Lo schema è definito come dizionario. L'ID flusso è la chiave del dizionario. Il valore della chiave è i tipi delle colonne. Un componente può dichiarare più flussi.

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

La funzione seguente viene aggiunta a un oggetto **Context:The** following function is added to a Context object:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Gli sviluppatori devono assicurarsi che le tuple generate rispettino lo schema definito per un flusso. In caso contrario, il sistema genererà un'eccezione di runtime.

### <a name="multistream-support"></a>Supporto multistream

SCP consente al codice di generare o ricevere da più flussi distinti contemporaneamente. **L'oggetto Context** riflette questo supporto come parametro ID di flusso facoltativo del metodo **Emit.**

Sono stati aggiunti due metodi nell'oggetto **SCP.NET Context.** Generano una o più tuple in flussi specifici. Il parametro *streamId* è una stringa. Il valore deve essere lo stesso sia nel codice C , che nella specifica di definizione della topologia.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

L'emissione di un flusso inesistente causa eccezioni di runtime.

### <a name="fields-grouping"></a>Raggruppamento dei campi

Il raggruppamento dei campi predefiniti in Tempesta non funziona correttamente in SCP.NET. Sul lato proxy Java, il tipo di dati di tutti i campi è in realtà **byte[]**. Il raggruppamento dei campi utilizza il codice hash dell'oggetto **byte[]** per eseguire il raggruppamento. Il codice hash è l'indirizzo di questo oggetto nella RAM. Pertanto, il raggruppamento sarà errato per gli oggetti multibyte che condividono lo stesso contenuto ma non lo stesso indirizzo.

SCP.NET aggiunge un metodo di raggruppamento personalizzato e utilizza il contenuto dell'oggetto **byte[]** per eseguire il raggruppamento. In un file di specifica, la sintassi è simile a questa:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

Nel file di specifica precedente:

* `scp-field-group`specifica che il raggruppamento è un raggruppamento di campi personalizzato implementato da SCP.
* `:tx`o `:non-tx` specifica se la topologia è transazionale. Queste informazioni sono necessarie perché l'indice iniziale è diverso tra topologie transazionali e non transazionali.
* `[0,1]`specifica un set hash di ID di campo che iniziano con zero.

### <a name="hybrid-topology"></a>Topologia ibrida

Il codice Native Storm è scritto in Java. SCP.NET ha migliorato Storm per consentire di scrivere il codice c'è per gestire la logica di business. Ma SCP.NET supporta anche topologie ibride, che contengono non solo spout/bolt di C, ma anche spout/bolt Java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Specificare spout Java/bolt in un file di specifiche

È possibile utilizzare **scp-spout** e **scp-bolt** in un file di specifica per specificare spout java e bulloni. Ad esempio:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Ecco `microsoft.scp.example.HybridTopology.Generator` il nome della classe di beccuccio Java.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Specificare il percorso di classe Java in un comando runSpecSpecify the Java classpath in a runSpec command

Se si desidera inviare una topologia contenente beccucci o bulloni Java, compilarli prima per produrre file JAR. Specificare quindi il percorso di classe java che contiene i file JAR quando si invia la topologia. Ad esempio:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Qui, `examples\HybridTopology\java\target\` è la cartella contenente il file JAR Java beccuccio/bolt.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializzazione e deserializzazione tra Java e C #

Un componente SCP include il lato Java e il lato C. Per interagire con i beccucci/bolt Java nativi, la serializzazione e la deserializzazione devono essere eseguite tra il lato Java e il lato C, come illustrato nel grafico seguente:

![Diagramma dell'invio di componenti Java al componente SCP, che viene quindi inviato a un componente Java diverso](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serializzazione sul lato Java e deserializzazione nel lato C

Specificare innanzitutto l'implementazione predefinita per la serializzazione sul lato Java e la deserializzazione nel lato C.

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

Specificare il metodo di deserializzazione del lato C, nel codice c'è.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Se il tipo di dati non è troppo complesso, questa implementazione predefinita deve gestire la maggior parte dei casi. Ecco i casi in cui è possibile collegare la propria implementazione:

* Il tipo di dati è troppo complesso per l'implementazione predefinita.
* Le prestazioni dell'implementazione predefinita non soddisfano i requisiti.

L'interfaccia di serializzazione sul lato Java è definita come:The serialization interface in the Java side is defined as:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

L'interfaccia di deserializzazione nel lato C , è definita come:The deserialization interface in the C' side is defined as:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serializzazione sul lato C , e deserializzazione nel lato Java

Specificare il metodo di serializzazione del lato C, nel codice C.

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

In `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` questo caso, è il `"microsoft.scp.example.HybridTopology.Person"` nome del deserializzatore ed è la classe di destinazione in cui vengono deserializzati i dati.

È anche possibile collegare la propria implementazione di un serializzatore C , e un deserializzatore Java.You can also plug in your own implementation of a C' serializer and a Java deserializer.

Questo codice è l'interfaccia per il serializzatore di C:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Questo codice è l'interfaccia per il deserializzatore Java:This code is the interface for the Java deserializer:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Modalità host SCP

In questa modalità, è possibile compilare il codice come DLL e utilizzare SCPHost.exe come fornito da SCP per inviare una topologia. Un file di specifica è simile al codice seguente:A specification file looks like this code:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

In `"plugin.name"` questo caso, viene specificato come `"SCPHost.exe"`, fornito dall'SDK SCP. SCPHost.exe accetta tre parametri nel seguente ordine:

1. Il nome della `"HelloWorld.dll"` DLL, che è in questo esempio.
1. Il nome della `"Scp.App.HelloWorld.Generator"` classe, che è in questo esempio.
1. Nome di un metodo statico pubblico, che può essere richiamato per ottenere un'istanza di **ISCPPlugin**.

In modalità host, compilare il codice come DLL per la chiamata da parte della piattaforma SCP. Poiché la piattaforma può quindi ottenere il controllo completo dell'intera logica di elaborazione, è consigliabile inviare la topologia in modalità host SCP. In questo modo semplifica l'esperienza di sviluppo. Offre inoltre maggiore flessibilità e migliore compatibilità con le versioni precedenti per le versioni successive.

## <a name="scp-programming-examples"></a>Esempi di programmazione SCP

### <a name="helloworld"></a>HelloWorld

Il seguente semplice esempio HelloWorld mostra un'ora di SCP.NET. Utilizza una topologia non transazionale con un beccuccio chiamato **generatore** e due bulloni chiamati **splitter** e **counter**. Il **generatore** beccuccio genera casualmente frasi ed emette queste frasi a **splitter**. Il bullone **splitter** divide le frasi in parole ed emette queste parole al **bullone contatore.** Il **bullone contatore** utilizza un dizionario per registrare l'occorrenza di ogni parola.

Questo esempio ha due file di specifica:\_HelloWorld.spec e HelloWorld EnableAck.spec. Il codice di C'è in grado di `pluginConf` scoprire se il riconoscimento è abilitato ottenendo l'oggetto dal lato Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Se il riconoscimento è abilitato nello spout, un dizionario memorizza nella cache le tuple che non sono state riconosciute. Se `Fail` viene chiamato, la tupla non riuscita viene riprodotta.

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

Nell'esempio HelloWorldTx seguente viene illustrato come implementare la topologia transazionale. L'esempio include un beccuccio denominato **generator**, un bullone batch denominato **partial-count**e un bullone di commit denominato **count-sum**. L'esempio include anche tre file di testo esistenti: DataSource0.txt, DataSource1.txt e DataSource2.txt.

In ogni transazione, il **generatore** seleziona casualmente due file dai tre file esistenti e genera i due nomi di file al bullone **di conteggio parziale.** Il bullone di **conteggio parziale:**

1. Ottiene un nome file dalla tupla ricevuta.
1. Apre il file corrispondente.
1. Conta il numero di parole nel file.
1. Genera il conteggio delle parole per il bullone **count-sum.**

Il bolt **count-sum** riepiloga il conteggio totale.

Per ottenere una semantica esattamente una volta, il bullone di commit **count-sum** deve valutare se si tratta di una transazione riprodotta. In questo esempio, ha la seguente variabile membro statica:In this example, it has the following static member variable:

```csharp
public static long lastCommittedTxId = -1; 
```

Quando viene creata un'istanza **ISCPBatchBolt,** `txAttempt` ottiene il valore dell'oggetto dai parametri di input.

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

Quando `FinishBatch` viene `lastCommittedTxId` chiamato, viene aggiornato se non si tratta di una transazione riprodotta.

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

Questa topologia contiene un beccuccio Java e un bullone di C. Utilizza l'implementazione di serializzazione e deserializzazione predefinita fornita dalla piattaforma SCP. Per informazioni dettagliate sui dettagli\\del file di specifica, vedere il file HybridTopology.spec negli esempi della cartella HybridTopology. Vedere anche SubmitTopology.bat per informazioni su come specificare il percorso di classe Java.

### <a name="scphostdemo"></a>SCPHostDemo

Questo esempio è in sostanza lo stesso di HelloWorld.This example is in essence the same as HelloWorld. L'unica differenza è che il codice viene compilato come DLL e la topologia viene inviata utilizzando SCPHost.exe. Vedere la sezione Modalità host SCP per una spiegazione più dettagliata.

## <a name="next-steps"></a>Passaggi successivi

Per esempi di topologie Apache Storm create utilizzando SCP, vedere i seguenti articoli:

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Elaborare eventi di Hub eventi di Azure con Apache Storm in HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Elaborare i dati del sensore veicolo da Hub eventi di Azure usando Apache Storm in HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Estrarre, trasformare e caricare (ETL) da Hub eventi di Azure ad Apache HBaseExtract, transform, and load (ETL) from Azure Event Hubs to Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
