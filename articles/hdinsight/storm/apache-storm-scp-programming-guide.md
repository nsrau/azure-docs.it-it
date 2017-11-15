---
title: Guida alla programmazione SCP.NET | Documentazione Microsoft
description: "Informazioni su come utilizzare SCP.NET per creare topologie Storm basate su .NET per l’utilizzo con Storm in HDInsight."
services: hdinsight
documentationcenter: 
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: a0ce92ba58fbcda812a3d4e5e275178b73400d6c
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="scp-programming-guide"></a>Guida alla programmazione SCP
SCP è una piattaforma per la compilazione di applicazioni di elaborazione dati dalle prestazioni elevate, affidabili, coerenti e in tempo reale. È basata su [Apache Storm](http://storm.incubator.apache.org/) , un sistema di elaborazione dei flussi progettato dalle community di sviluppo di software open source (OSS). Storm è stato progettato da Nathan Marz ed è stato reso open source da Twitter. Il sistema si avvale di [Apache Zookeeper](http://zookeeper.apache.org/), un altro progetto Apache per il coordinamento e la gestione dello stato altamente affidabili delle applicazioni distribuite. 

Il progetto SCP ha reso portabile in Windows non solo Storm, ma anche le estensioni e le personalizzazioni aggiunte per l'ecosistema Windows. Le estensioni includono l'esperienza di sviluppatore .NET e le relative librerie. Le personalizzazioni includono lo sviluppo basato su Windows. 

Le estensioni e le personalizzazioni sono compilate in modo tale che non sono necessari fork dei progetti OSS ed è possibile usare ecosistemi derivati basati su Storm.

## <a name="processing-model"></a>Modello di elaborazione
In dati in SCP vengono modellati come flussi continui di tuple. In genere le tuple vengono inviate a una coda, quindi prelevate e trasformate dalla logica di business ospitata in una topologia Storm. Infine, è possibile inoltrare l'output tramite pipe sotto forma di tuple o eseguirne il commit in archivi, come il file system distribuito, o database, come SQL Server.

![Diagramma di una coda che invia dati all'elaborazione, che a sua volta li invia a un archivio dati](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

In Storm, una topologia applicazione definisce un grafo di elaborazione. Ogni nodo in una topologia contiene logica di elaborazione e i collegamenti tra i nodi indicano il flusso dei dati. I nodi per l'inserimento dei dati di input nella topologia, denominati _spout_, possono essere usati per porre in sequenza i dati. I dati di input possono risiedere in log di file, database transazionali, contatore delle prestazioni del sistema e così via. I nodi con flussi di dati sia di input che di output, denominati _bolt_, eseguono il filtraggio, la selezione e l'aggregazione dei dati effettivi.

SCP supporta l'elaborazione dati di tipo best effort, at-least-once ed exactly-once. In un'applicazione di elaborazione di streaming distribuita, possono verificarsi diversi errori durante l'elaborazione di dati, ad esempio un'interruzione di rete, un errore del computer o del codice utente e così via. L'elaborazione at-least-once assicura che tutti i dati vengano elaborati almeno una volta, riproducendo automaticamente gli stessi dati in caso di errore. L'elaborazione at-least-once è semplice e affidabile e offre risultati positivi in molte applicazioni. Quando tuttavia un'applicazione richiede il conteggio esatto, l'elaborazione at-least-once non è sufficiente, perché potenzialmente nella topologia dell'applicazione potrebbero essere riprodotti gli stessi dati. In questi casi si può usare l'elaborazione exactly-once, progettata per garantire che i risultati siano corretti anche se è possibile che i dati vengano riprodotti ed elaborati più volte.

SCP consente agli sviluppatori .NET di sviluppare applicazioni di elaborazione dati in tempo reale mentre usano una JVM (Java Virtual Machine) con Storm in modo invisibile. .NET e la JVM comunicano tramite socket locali TCP. Essenzialmente, ogni Spout/Bolt è una coppia di processi .Net/Java, in cui la logica utente viene eseguita nel processo .Net come plug-in.

Per compilare un'applicazione di elaborazione dati in SCP sono necessari diversi passaggi:

* Progettare e implementare gli Spout per l'inserimento dei dati provenienti dalla coda.
* Progettare e implementare bolt per l'elaborazione dei dati di input e il salvataggio dei dati in archivi esterni, ad esempio un database.
* Progettare la topologia, quindi inviarla ed eseguirla. La topologia definisce i vertici e i flussi di dati tra i vertici. SCP recupererà le specifiche della topologia e le implementerà in un cluster Storm, in cui ogni vertice viene eseguito in un nodo logico. Il failover e la scalabilità saranno gestiti dall'utilità di pianificazione di Storm.

Questo documento illustra in dettaglio con alcuni semplici esempi come compilare un'applicazione di elaborazione dati con SCP.

## <a name="scp-plugin-interface"></a>Interfaccia del plug-in SCP
I plug-in SCP (o applicazioni) sono file eseguibili autonomi che possono essere eseguiti in Visual Studio nella fase di sviluppo e anche collegati alla pipeline di Storm dopo la distribuzione in produzione. Scrivere il plug-in SCP è come scrivere qualsiasi altra applicazione console Windows standard. La piattaforma SCP.NET dichiara le interfacce per gli Spout e i Bolt e il codice del plug-in utente dovrebbe implementare tali interfacce. Lo scopo principale di questo progetto è fare in modo che l'utente possa concentrarsi sulla propria logica di business, lasciando il resto della gestione alla piattaforma SCP.NET.

Il codice del plug-in utente deve implementare una delle interfacce seguenti, in base al tipo di topologia (transazionale o non transazionale) e al tipo di componente (spout o bolt).

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin è l'interfaccia comune per tutti i tipi di plug-in. Attualmente è un'interfaccia fittizia.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout è l'interfaccia per lo Spout non transazionale.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Quando viene chiamato `NextTuple()`, il codice utente C\# può emettere una o più tuple. Se non ci sono tuple da emettere, il metodo deve essere restituito senza emettere alcunché. Si noti che `NextTuple()`, `Ack()` e `Fail()` vengono chiamati in un ciclo ridotto in un singolo thread nel processo C\#. Quando non ci sono tuple da emettere, è utile una breve sospensione del metodo NextTuple (ad esempio 10 millisecondi), in modo da limitare il consumo di CPU.

`Ack()` e `Fail()` vengono chiamati solo se il meccanismo di acknowledgement è abilitato nel file delle specifiche. `seqId` identifica la tupla confermata o non confermata. Dunque, se l'acknowledgement è abilitato in una topologia non transazionale, nello Spout deve essere usata la funzione di emissione seguente:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Se l'acknowledgment non è supportato in una topologia non transazionale, `Ack()` e `Fail()` possono essere lasciati come funzione vuota.

Il parametro di input `parms` in queste funzioni è un dizionario vuoto ed è riservato per uso futuro.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt è l'interfaccia per il Bolt non transazionale.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Quando è disponibile una nuova tupla, viene chiamata la funzione `Execute()` per elaborarla.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout è l'interfaccia per lo Spout transazionale.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Come avviene per la controparte non transazionale, `NextTx()`, `Ack()` e `Fail()` vengono chiamati in un ciclo ridotto in un singolo thread nel processo C\#. Quando non ci sono dati da emettere, è utile una breve sospensione del metodo `NextTx` (ad esempio 10 millisecondi), in modo da limitare il consumo di CPU.

`NextTx()` viene chiamato per avviare una nuova transazione. Il parametro di output `seqId`, usato per identificare la transazione, viene usato anche in `Ack()` e `Fail()`. In `NextTx()`, l'utente può emettere dati verso il lato Java. I dati vengono archiviati in ZooKeeper a supporto della riproduzione. Poiché la capacità di ZooKeeper è limitata, nello spout transazionale l'utente deve emettere solo metadati e non dati in blocco.

Storm riprodurrà automaticamente una transazione in caso di errore, dunque in un caso normale `Fail()` non dovrebbe essere chiamato. Se SCP può verificare i metadati emessi dallo Spout transazionale, tuttavia, può chiamare `Fail()` quando i metadati non sono validi.

Il parametro di input `parms` in queste funzioni è un dizionario vuoto ed è riservato per uso futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt è l'interfaccia per il Bolt transazionale.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` viene chiamato quando al Bolt arriva una nuova tupla. `FinishBatch()` viene chiamato al termine di questa transazione. Il parametro di output `parms` è riservato per l'utilizzo futuro.

Per la topologia transazionale esiste un concetto importante, `StormTxAttempt`. Ha due campi, `TxId` e `AttemptId`. `TxId` viene usato per identificare una transazione specifica. In caso di errore e di nuova riproduzione di una transazione, possono essere eseguiti più tentativi. SCP.NET crea un nuovo oggetto ISCPBatchBolt per elaborare ogni `StormTxAttempt`, la stessa operazione eseguita da Storm in Java. Lo scopo di questo progetto è supportare l'elaborazione di transazioni parallele. Tenere presente che, se il tentativo di transazione viene completato, l'oggetto ISCPBatchBolt corrispondente viene distrutto e sottoposto a un'operazione di Garbage Collection.

## <a name="object-model"></a>Modello a oggetti
In SCP.NET è disponibile un semplice set di oggetti chiave che gli sviluppatori possono usare per la programmazione. Si tratta degli oggetti **Context**, **StateStore** e **SCPRuntime**, illustrati nella parte rimanente di questa sezione.

### <a name="context"></a>Context
Context fornisce un ambiente di esecuzione per l'applicazione. Ogni istanza di ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) dispone di un'istanza Context corrispondente. Le funzionalità fornite da Context possono essere suddivise in due parti: (1) la parte statica, disponibile nell'intero processo C\#, (2) la parte dinamica, disponibile solo per la specifica istanza Context.

### <a name="static-part"></a>Parte statica
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` è fornito a scopi di log.

`pluginType` viene usato per indicare il tipo di plug-in del processo C\#. Se il processo C\# viene eseguito in modalità di test locale (senza Java), il tipo di plug-in è `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` consente di ottenere i parametri di configurazione dal lato Java. I parametri vengono passati dal lato Java al momento dell'inizializzazione del plug-in C\#. I parametri `Config` sono divisi in due parti: `stormConf` e `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` corrisponde ai parametri definiti da Storm e `pluginConf` ai parametri definiti da SCP. ad esempio:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` consente di ottenere il contesto della topologia ed è particolarmente utile per i componenti con più operatori di parallelismo. Di seguito è fornito un esempio:

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

### <a name="dynamic-part"></a>Parte dinamica
Le interfacce seguenti sono pertinenti a una specifica istanza di Context. L'istanza di Context viene creata dalla piattaforma SCP.NET e passata al codice utente:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Per lo Spout non transazionale che supporta l'acknowledgement, viene fornito il metodo seguente:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Per il Bolt non transazionale che supporta l'acknowledgement, deve indicare esplicitamente `Ack()` o `Fail()` per la tupla ricevuta. Quando viene emessa una nuova tupla, deve inoltre specificarne gli ancoraggi. Sono forniti i metodi seguenti.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` fornisce servizi metadati, generazione di sequenze monotone e coordinamento senza attesa. `StateStore`consente di creare astrazioni di concorrenza distribuite di livello superiore, tra cui blocchi distribuiti, code distribuite e servizi di transazione.

Le applicazioni SCP possono usare l'oggetto `State` per rendere persistenti alcune informazioni in ZooKeeper, in particolare per la topologia transazionale. In questo modo, in caso di arresto anomalo e riavvio dello Spout, è possibile recuperare le informazioni necessarie da ZooKeeper e riavviare la pipeline.

L'oggetto `StateStore` fornisce principalmente i metodi seguenti:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommited States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Commited();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

L'oggetto `State` fornisce principalmente i metodi seguenti:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Per il metodo `Commit()`, se simpleMode è impostato su true, elimina l'elemento ZNode corrispondente in ZooKeeper. In caso contrario, elimina l'elemento ZNode corrente e aggiunge un nuovo nodo in COMMITTED\_PATH.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime rende disponibili i due metodi seguenti:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` viene usato per inizializzare l'ambiente di runtime SCP. In questo metodo, il processo C\# si connette al lato Java per ottenere i parametri di configurazione e il contesto della topologia.

`LaunchPlugin()` viene usato per avviare il ciclo di elaborazione dei messaggi. In questo ciclo, il plug-in C\# riceve messaggi dal lato Java (incluse tuple e segnali di controllo) e quindi li elabora, forse chiamando il metodo di interfaccia fornito dal codice utente. Il parametro di input per il metodo `LaunchPlugin()` è un delegato che può restituire un oggetto che implementa l'interfaccia ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Per ISCPBatchBolt, è possibile ottenere `StormTxAttempt` da `parms` e usarlo per verificare se si tratta di un tentativo riprodotto. Questa verifica, spesso eseguita nel bolt di commit, è illustrata nell'esempio `HelloWorldTx`.

In generale, i plug-in SCP possono essere eseguiti in due modalità:

1. Modalità test locale: in questa modalità i plug-in SCP (il codice utente C\#) vengono eseguiti in Visual Studio durante la fase di sviluppo. `LocalContext` , che fornisce metodi per serializzare le tuple emesse in file locali e rileggerle nella memoria.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Modalità normale: in questa modalità, i plug-in SCP vengono avviati dal processo Java Storm.
   
    Di seguito è riportato un esempio di avvio del plug-in SCP:
   
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

## <a name="topology-specification-language"></a>Linguaggio di specifica della topologia
SCP Topology Specification è un linguaggio specifico di dominio per la descrizione e la configurazione di topologie SCP. È basato sul DSL di Storm in linguaggio Clojure (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) ed esteso da SCP.

Le specifiche della topologia possono essere inviate direttamente al cluster Storm per l'esecuzione mediante il comando ***runspec***.

SCP.NET ha aggiunto le funzioni seguenti per la definizione delle topologie transazionali:

| **Nuove funzioni** | **Parametri** | **Descrizione** |
| --- | --- | --- |
| **tx-topolopy** |topology-name<br />spout-map<br />bolt-map |Consente di definire una topologia transazionale con il nome della topologia,&nbsp;la mappa di definizione degli spout e la mappa di definizione dei bolt. |
| **scp-tx-spout** |exec-name<br />args<br />fields |Consente di definire uno Spout transazionale. Esegue l'applicazione con ***exec-name*** usando ***args***.<br /><br />***fields*** corrisponde ai campi di output per lo Spout. |
| **scp-tx-batch-bolt** |exec-name<br />args<br />fields |Consente di definire un Bolt batch transazionale. Esegue l'applicazione con ***exec-name*** usando ***args***.<br /><br />Il parametro Fields corrisponde ai campi di output per il Bolt. |
| **scp-tx-commit-bolt** |exec-name<br />args<br />fields |Consente di definire un bolt di commit transazionale. Esegue l'applicazione con ***exec-name*** usando ***args***.<br /><br />***fields*** corrisponde ai campi di output per il Bolt. |
| **nontx-topolopy** |topology-name<br />spout-map<br />bolt-map |Consente di definire una topologia non transazionale con il nome della topologia,&nbsp;la mappa di definizione degli spout e la mappa di definizione dei bolt. |
| **scp-spout** |exec-name<br />args<br />fields<br />Parametri |Consente di definire uno Spout non transazionale. Esegue l'applicazione con ***exec-name*** usando ***args***.<br /><br />***fields*** corrisponde ai campi di output per lo Spout.<br /><br />***parameters*** è facoltativo e può essere usato per specificare parametri come ad esempio "nontransactional.ack.enabled". |
| **scp-bolt** |exec-name<br />args<br />fields<br />Parametri |Consente di definire un Bolt non transazionale. Esegue l'applicazione con ***exec-name*** usando ***args***.<br /><br />***fields*** corrisponde ai campi di output per il Bolt.<br /><br />***parameters*** è facoltativo e può essere usato per specificare parametri come ad esempio "nontransactional.ack.enabled". |

In SCP.NET sono definite le parole chiave seguenti:

| **Parole chiave** | **Descrizione** |
| --- | --- |
| **:name** |Consente di definire il nome della topologia. |
| **:topology** |Consente di definire la topologia usando le funzioni precedenti e quelle predefinite. |
| **:p** |Consente di definire l'hint di parallelismo per ogni Spout o Bolt. |
| **:config** |Consente di definire i parametri di configurazione o aggiornare quelli esistenti |
| **:schema** |Consente di definire lo schema del flusso. |

E parametri usati di frequente:

| **Parametro** | **Descrizione** |
| --- | --- |
| **"plugin.name"** |Nome del file eseguibile del plug-in C#. |
| **"plugin.args"** |Argomenti del plug-in. |
| **"output.schema"** |Schema di output. |
| **"nontransactional.ack.enabled"** |Abilitazione o meno dell'acknowledgement per la topologia non transazionale. |

Il comando runspec viene implementato insieme ai bit. L'utilizzo è simile al seguente:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Il parametro ***resource-dir*** è facoltativo. È necessario specificarlo quando si vuole collegare un'applicazione C\# e questa directory contiene l'applicazione, le dipendenze e le configurazioni.

Anche il parametro ***classpath*** è facoltativo. Si usa per specificare il classpath Java se il file delle specifiche contiene Spout o Bolt Java.

## <a name="miscellaneous-features"></a>Funzionalità varie
### <a name="input-and-output-schema-declaration"></a>Dichiarazione dello schema di input e di output
Gli utenti possono emettere tuple nei processi C\#, la piattaforma deve serializzare la tupla in byte[] e trasferirla sul lato Java, quindi Storm trasferirà la tupla alle destinazioni. Nel frattempo, nei componenti downstream i processi C\# riceveranno nuovamente le tuple dal lato Java e le convertiranno nei tipi originali in base alla piattaforma. Tutte queste operazioni sono nascoste dalla piattaforma.

Per supportare la serializzazione e la deserializzazione, il codice utente deve dichiarare lo schema degli input e degli output.

Lo schema di flusso di input/output viene definito come dizionario. La chiave è StreamId. Il valore corrisponde ai tipi delle colonne. Nel componente possono essere dichiarati più flussi.

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


Nell'oggetto Context è stata aggiunta l'API seguente:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Gli sviluppatori devono assicurarsi che le tuple emesse rispettino lo schema definito per il flusso. In caso contrario, il sistema genererà un'eccezione di runtime.

### <a name="multi-stream-support"></a>Supporto di più flussi
SCP supporta codice utente per l'emissione o la ricezione simultanea da più flussi distinti. Questo supporto è riflesso nell'oggetto Context dal fatto che il metodo Emit accetta un parametro StreamId facoltativo.

Nell'oggetto Context SCP.NET sono stati aggiunti due metodi. Vengono usati per l'emissione di una o più tuple per specificare StreamId. StreamId è una stringa e deve essere coerente in C\# e nelle specifiche di definizione della topologia.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

L'emissione in un flusso non esistente causa eccezioni di runtime.

### <a name="fields-grouping"></a>Raggruppamento dei campi
Il raggruppamento dei campi predefinito di Storm non funziona correttamente in SCP.NET. Sul lato Proxy Java tutti i dati dei campi sono in effetti di tipo byte[] e per il raggruppamento dei campi viene usato il codice hash dell'oggetto byte[]. Il codice hash dell'oggetto byte[] corrisponde all'indirizzo dell'oggetto in memoria. Per questo motivo, il raggruppamento di due oggetti byte[] che condividono lo stesso contenuto, ma non lo stesso indirizzo, sarà errato.

SCP.NET aggiunge un metodo di raggruppamento personalizzato e usa il contenuto di byte[] per eseguire il raggruppamento. Nel file **SPEC** la sintassi è come segue:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Qui

1. "scp-field-group" vuol dire "raggruppamento campi personalizzato implementato da SCP".
2. ":tx" o ":non-tx" indica se si tratta o meno di una topologia transazionale. Questa informazione è necessaria in quanto l'indice di inizio è diverso nei due tipi di topologie.
3. [0,1] indica un set di hash di ID campo, a partire da 0.

### <a name="hybrid-topology"></a>Topologia ibrida
Il sistema Storm nativo è scritto in Java. E SCP.Net lo ha migliorato per consentire agli sviluppatori C\# di scrivere codice C\# per gestire la logica di business. SCP supporta però anche le topologie ibride, che contengono non solo spout/bolt C\#, ma anche spout/bolt Java.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Specificare gli Spout e i Bolt Java nel file delle specifiche
Nel file delle specifiche è possibile usare "scp-spout" e "scp-bolt" anche per specificare gli Spout e i Bolt Java. Di seguito è riportato un esempio:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Qui `microsoft.scp.example.HybridTopology.Generator` è il nome della classe Spout Java.

### <a name="specify-java-classpath-in-runspec-command"></a>Specificare il classpath Java nel comando runSpec
Per inviare una topologia che contiene Spout o Bolt Java, è necessario prima compilare gli Spout o Bolt Java e ottenere i file Jar. Quindi, è necessario specificare il classpath Java che contiene i file Jar al momento dell'invio della topologia. Di seguito è fornito un esempio:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Qui **examples\\HybridTopology\\java\\target\\** è la cartella che contiene il file JAR degli spout/bolt Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializzazione e deserializzazione tra Java e C\#
Il componente SCP include il lato Java e il lato C\#. Per interagire con spout/bolt Java nativi, la serializzazione/deserializzazione deve essere effettuata tra il lato Java e il lato C\#, come illustrato nel grafico seguente.

![Diagramma del componente Java che invia al componente SCP che invia al componente Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serializzazione sul lato Java e deserializzazione sul lato C#\#**
   
   Prima di tutto è necessario fornire l'implementazione predefinita per la serializzazione sul lato Java e la deserializzazione sul lato C\#. Il metodo di serializzazione sul lato Java può essere specificato nel file SPEC:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Il metodo di deserializzazione sul lato C\# può essere specificato nel codice utente C\#:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Questa implementazione predefinita consente di gestire la maggior parte dei casi purché il tipo di dati non sia troppo complesso. Per alcuni casi, se il tipo di dati utente è troppo complesso o se le prestazioni dell'implementazione predefinita non soddisfano i requisiti dell'utente, gli utenti possono collegare la propria implementazione.
   
   L'interfaccia di serializzazione sul lato Java viene definita nel modo seguente:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   L'interfaccia di deserializzazione sul lato C\# viene definita nel modo seguente:
   
   interfaccia pubblica ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serializzazione sul lato C\# e deserializzazione sul lato Java**
   
   Il metodo di serializzazione sul lato C\# può essere specificato nel codice utente C\#:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Il metodo di deserializzazione sul lato Java può essere specificato nel file SPEC:
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Qui "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" è il nome del deserializzatore e "microsoft.scp.example.HybridTopology.Person" è la classe di destinazione dei dati deserializzati.
   
   L'utente può anche collegare una propria implementazione del serializzatore C\# e del deserializzatore Java. Questo codice costituisce l'interfaccia per il serializzatore C\#:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Questo codice costituisce l'interfaccia per il deserializzatore Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Modalità host di SCP
In questa modalità l'utente può compilare il suo codice in DLL e usare il file SCPHost.exe fornito da SCP per inviare la topologia. Il file delle specifiche ha un aspetto simile al codice seguente:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Qui `plugin.name` viene specificato come file `SCPHost.exe` fornito dall’SDK di SCP. SCPHost.exe accetta tre parametri:

1. Il primo è il nome della DLL, che in questo esempio è `"HelloWorld.dll"` .
2. Il secondo è il nome della classe, che in questo esempio è `"Scp.App.HelloWorld.Generator"` .
3. Il terzo è il nome di un metodo statico pubblico, che può essere richiamato per ottenere un'istanza di ISCPPlugin.

In modalità host il codice utente viene compilato come DLL e richiamato dalla piattaforma SCP. In questo modo, la piattaforma SCP può ottenere il controllo completo dell'intera logica di elaborazione. È quindi consigliabile inviare la topologia nella modalità host di SCP, poiché questo consente di semplificare l'esperienza di sviluppo e offre una maggiore flessibilità e una migliore compatibilità con le versioni precedenti per le versioni successive.

## <a name="scp-programming-examples"></a>Esempi di programmazione SCP
### <a name="helloworld"></a>HelloWorld
**HelloWorld** è un esempio semplice di utilizzo di SCP.Net. Usa una topologia non transazionale con uno spout denominato **generator** e due bolt denominati **splitter** e **counter**. Lo spout **generator** genera frasi casuali e le emette verso **splitter**. Il bolt **splitter** divide le frasi in parole ed emette le parole verso il bolt **counter**. Il Bolt “counter” usa un dizionario per registrare il numero di occorrenze di ogni parola.

Per questo esempio sono presenti due file di specifiche, **HelloWorld.spec** e **HelloWorld\_EnableAck.spec**. Nel codice C\# può determinare se il meccanismo di acknowledgement è abilitato ottenendo pluginConf dal lato Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Nello Spout, se l'acknowledgement è abilitato viene usato un dizionario per memorizzare nella cache le tuple non confermate. Se viene chiamato Fail(), la tupla in errore viene riprodotta:

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

### <a name="helloworldtx"></a>HelloWorldTx
L'esempio **HelloWorldTx** illustra come implementare la topologia transazionale. Contiene uno spout denominato **generator**, un bolt batch denominato **partial-count** e un bolt di commit denominato **count-sum**. Sono presenti anche tre file txt già creati: **DataSource0.txt**, **DataSource1.txt** e **DataSource2.txt**.

In ogni transazione lo spout **generator** seleziona casualmente due dei tre file già creati ed emette i nomi dei due file verso il bolt **partial-count**. Il bolt **partial-count** prima ottiene il nome del file dalla tupla ricevuta, quindi apre il file e conta il numero di parole al suo interno. Infine, emette il numero di parole verso il bolt **count-sum**. Il bolt **count-sum** riepiloga il conteggio totale.

Per ottenere una semantica di tipo **exactly once**, il bolt di commit **count-sum** deve stabilire se si tratta di una transazione riprodotta. In questo esempio, contiene una variabile membro statica:

    public static long lastCommittedTxId = -1; 

Quando viene creata un'istanza di ISCPBatchBolt, questa ottiene il valore `txAttempt` dai parametri di input:

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

Quando viene chiamato `FinishBatch()`, se non si tratta di una transazione riprodotta, il valore `lastCommittedTxId` verrà aggiornato.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
Questa topologia contiene uno spout Java e un bolt C\#. Usa l'implementazione di serializzazione e deserializzazione predefinita fornita dalla piattaforma SCP. Vedere il file **HybridTopology.spec** nella cartella **examples\\HybridTopology** per dettagli sul file delle specifiche e **SubmitTopology.bat** per informazioni su come specificare il classpath Java.

### <a name="scphostdemo"></a>SCPHostDemo
In sostanza, questo esempio è uguale a HelloWorld. L'unica differenza è che il codice utente viene compilato come DLL e la topologia viene inviata usando SCPHost.exe. Per una spiegazione più dettagliata, vedere la sezione "Modalità host di SCP".

## <a name="next-steps"></a>Passaggi successivi
Per esempi di topologie Storm create con SCP, vedere i documenti seguenti:

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Elaborare i dati del sensore veicolo dall'hub di eventi usando Storm in HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Estrarre, trasformare e caricare dagli hub eventi di Azure in HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
