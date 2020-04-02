---
title: Suggerimenti sulle prestazioni di Azure Cosmos DB per .NET
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni di Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: ee2743af2f8499aec04d8b6b733e1ba4c2a82083
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546070"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Suggerimenti sulle prestazioni per Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB è un database distribuito veloce e flessibile, facilmente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche importanti all'architettura o scrivere codice complesso per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API. Per altre informazioni, vedere [Effettuare il provisioning della velocità effettiva per un contenitore](how-to-provision-container-throughput.md) oppure [Effettuare il provisioning della velocità effettiva per un database](how-to-provision-database-throughput.md). Tuttavia, poiché il database Cosmos di Azure è accessibile tramite chiamate di rete, è possibile eseguire ottimizzazioni sul lato client per ottenere prestazioni ottimali quando si utilizza [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Pertanto, se si sta tentando di migliorare le prestazioni del database, considerare queste opzioni:So, if you're trying to improve your database performance, consider these options:

## <a name="hosting-recommendations"></a>Raccomandazioni di hosting

**Per carichi di lavoro che richiedono un utilizzo intensivo di query, usare Windows a 64 bit anziché l'elaborazione host Linux o Windows a 32 bit**

Si consiglia l'elaborazione host Windows a 64 bit per migliorare le prestazioni. SQL SDK include un file ServiceInterop.dll nativo per analizzare e ottimizzare le query in locale. ServiceInterop.dll è supportato solo sulla piattaforma Windows x64. Per Linux e altre piattaforme non supportate in cui ServiceInterop.dll non è disponibile, viene effettuata una chiamata di rete aggiuntiva al gateway per ottenere la query ottimizzata. Per impostazione predefinita, i seguenti tipi di applicazioni utilizzano l'elaborazione host a 32 bit. Per modificare l'elaborazione dell'host in elaborazione a 64 bit, attenersi alla seguente procedura, in base al tipo di applicazione:

- Per le applicazioni eseguibili, è possibile modificare l'elaborazione dell'host impostando la destinazione della piattaforma su x64 nella finestra **Proprietà progetto,** nella scheda Compila.For executable applications, you can change host processing by setting the [platform target](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) to **x64** in the Project Properties window, on the **Build** tab.

- Per i progetti di test basati su VSTest, è possibile modificare l'elaborazione dell'host selezionando **Test** > **Test Settings** > **Default Processor Architecture come X64** dal menu **Test** di Visual Studio.

- Per le applicazioni Web ASP.NET distribuite localmente, è possibile modificare l'elaborazione dell'host selezionando **Usa la versione a 64 bit di IIS Express per siti Web e progetti** in Progetti**opzioni** >  **strumenti** > e**progetti Web di****soluzioni** > .

- Ad ASP.NET applicazioni Web distribuite in Azure, è possibile modificare l'elaborazione dell'host selezionando la piattaforma a 64 bit in Impostazioni applicazione nel portale di Azure.For the Applications applications deployed on Azure, you can change host processing by selecting the **64-bit** platform in **Application settings** in the Azure portal.

> [!NOTE] 
> Per impostazione predefinita, i nuovi progetti di Visual Studio sono impostati su **Qualsiasi CPU**. Si consiglia di impostare il progetto su **x64** in modo che non passi a **x86**. Un progetto impostato su **Qualsiasi CPU** può passare facilmente a x86 se viene aggiunta una dipendenza solo x86.A project set to Any CPU can easily switch to **x86** if an x86-only dependency is added.<br/>
> ServiceInterop.dll deve trovarsi nella cartella da cui viene eseguita la DLL SDK. Questo dovrebbe essere un problema solo se si copiano manualmente DLL o si dispone di sistemi di compilazione/distribuzione personalizzati.
    
**Attivare Garbage Collection lato server (GC)**

La riduzione della frequenza di garbage collection può essere utile in alcuni casi. In .NET impostare `true` [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) su .

**Scalabilità orizzontale del carico di lavoro del clientScale out your client workload**

Se si sta testando a livelli di velocità effettiva elevata (più di 50.000 RU/s), l'applicazione client potrebbe diventare il collo di bottiglia dovuto al limite della CPU o della rete. In questo caso, è possibile continuare a effettuare il push dell'account Azure Cosmos DB aumentando il numero di istanze delle applicazioni client in più server.

> [!NOTE] 
> L'utilizzo elevato della CPU può causare un aumento della latenza e delle eccezioni di timeout delle richieste.

## <a name="networking"></a>Rete
<a id="direct-connection"></a>

**Criteri di connessione: usare la modalità di connessione diretta**

Il modo in cui un client si connette ad Azure Cosmos DB ha importanti implicazioni sulle prestazioni, in particolare per la latenza lato client osservata. Sono disponibili due impostazioni di configurazione chiave per la configurazione dei criteri di connessione client: la *modalità* di connessione e il *protocollo*di connessione .  Le due modalità disponibili sono:

   * Modalità gateway
      
     La modalità gateway è supportata in tutte le piattaforme SDK ed è l'impostazione predefinita configurata per [Microsoft.Azure.DocumentDB SDK.](sql-api-sdk-dotnet.md) Se l'applicazione viene eseguita all'interno di una rete aziendale con restrizioni del firewall rigorose, la modalità gateway è la scelta migliore perché utilizza la porta HTTPS standard e un singolo endpoint. Il compromesso sulle prestazioni, tuttavia, è che la modalità gateway comporta un hop di rete aggiuntivo ogni volta che i dati vengono letti o scritti in Database Cosmos di Azure.The performance tradeoff, however, is that gateway mode involves an additional network hop every time data is read from or written to Azure Cosmos DB. Quindi la modalità diretta offre prestazioni migliori perché ci sono meno hop di rete. Si consiglia inoltre la modalità di connessione del gateway quando si eseguono applicazioni in ambienti con un numero limitato di connessioni socket.

     Quando si usa l'SDK in Funzioni di Azure, in particolare nel [piano Consumo](../azure-functions/functions-scale.md#consumption-plan), tenere presente i [limiti correnti delle connessioni.](../azure-functions/manage-connections.md) In tal caso, la modalità gateway potrebbe essere migliore se si lavora anche con altri client basati su HTTP all'interno dell'applicazione Funzioni di Azure.In that case, gateway mode might be better if you're working with other HTTP-based clients within your Azure Functions application.

   * Modalità diretta

     La modalità diretta supporta la connettività tramite il protocollo TCP ed è la modalità di connettività predefinita se si usa [Microsoft.Azure.Cosmos/.NET V3 SDK.](sql-api-sdk-dotnet-standard.md)

In modalità gateway, il database Cosmos di Azure usa la porta 443 e le porte 10250, 10255 e 10256 quando si usa l'API Cosmos DB di Azure per MongoDB. La porta 10250 esegue il mapping a un'istanza MongoDB predefinita senza replica geografica. Le porte 10255 e 10256 eseguono il mapping all'istanza Di MongoDB con replica geografica.
     
Quando si utilizza TCP in modalità diretta, oltre alle porte gateway, è necessario assicurarsi che l'intervallo di porte tra 10000 e 20000 sia aperto perché Azure Cosmos DB usa porte TCP dinamiche. Se queste porte non sono aperte e si tenta di utilizzare TCP, viene visualizzato un errore 503 Servizio non disponibile. Questa tabella mostra le modalità di connettività disponibili per le varie API e le porte del servizio usate per ogni API:This table shows the connectivity modes available for various APIs and the service ports used for each API:

|Modalità di connessione  |Protocollo supportato  |SDK supportati  |API/porta servizio  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Tutti gli SDK    |   SQL (443), MongoDB (10250, 10255, 10256), Tabella (443), Cassandra (10350), Grafico (443)    |
|Connessione diretta    |     TCP    |  .NET SDK    | Porti della gamma 10000-20000 |

Azure Cosmos DB offre un modello di programmazione RESTful semplice e aperto su HTTPS. DocumentDB offre anche un protocollo TCP efficiente, con un modello di comunicazione di tipo RESTful disponibile tramite .NET SDK per client. Il protocollo TCP utilizza TLS per l'autenticazione iniziale e la crittografia del traffico. Per prestazioni ottimali, usare il protocollo TCP quando possibile.

Per SDK V3, configurare la modalità di connessione quando si crea l'istanza, `CosmosClient` in `CosmosClientOptions`. Ricorda che la modalità diretta è l'impostazione predefinita.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Per Microsoft.Azure.DocumentDB SDK, configurare la modalità `DocumentClient` di connessione `ConnectionPolicy` durante la costruzione dell'istanza utilizzando il parametro . Se si utilizza la modalità `Protocol` diretta, `ConnectionPolicy` è anche possibile impostare il utilizzando il parametro .

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Poiché TCP è supportato solo in modalità diretta, se si utilizza la `Protocol` modalità `ConnectionPolicy` gateway, il protocollo HTTPS viene sempre utilizzato per comunicare con il gateway e il valore in viene ignorato.

![Criteri di connessione di Azure Cosmos DBThe Azure Cosmos DB connection policy](./media/performance-tips/connection-policy.png)

**Chiamare OpenAsync per evitare la latenza di avvio alla prima richiesta**

Per impostazione predefinita, la prima richiesta ha una latenza maggiore perché deve recuperare la tabella di routing degli indirizzi. Quando si utilizza SDK `OpenAsync()` [V2](sql-api-sdk-dotnet.md), chiamare una volta durante l'inizializzazione per evitare questa latenza di avvio alla prima richiesta:

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`genererà richieste per ottenere la tabella di routing degli indirizzi per tutti i contenitori nell'account. Per gli account che dispongono di molti contenitori `OpenAsync` ma la cui applicazione accede a un sottoinsieme di essi, genererebbe una quantità non necessaria di traffico, il che renderebbe l'inizializzazione lenta. Pertanto, l'utilizzo `OpenAsync` potrebbe non essere utile in questo scenario perché rallenta l'avvio dell'applicazione.

   <a id="same-region"></a>
**Per le prestazioni, collocare i client nella stessa area di AzureFor performance, collocate clients in same Azure region**

Quando possibile, inserire tutte le applicazioni che chiamano Database Cosmos di Azure nella stessa area del database database cosmos di Azure.When possible, place any applications that call Azure Cosmos DB in the same region as the Azure Cosmos DB database. Ecco un confronto approssimativo: le chiamate a Azure Cosmos DB all'interno della stessa area vengono completate entro 1 ms a 2 ms, ma la latenza tra la costa occidentale e orientale degli Stati Uniti è superiore a 50 ms. Questa latenza può variare da richiesta a richiesta, a seconda della route eseguita dalla richiesta passata dal client al limite del data center di Azure.This latency can vary from request to request, depending on the route taken by the request as the pass by the client to the Azure datacenter boundary. È possibile ottenere la latenza più bassa possibile assicurandosi che l'applicazione chiamante si trovi all'interno della stessa area di Azure dell'endpoint del database Cosmos di Azure di cui è stato eseguito il provisioning. Per un elenco delle aree disponibili, vedere [Aree](https://azure.microsoft.com/regions/#services)di Azure .

![Criteri di connessione di](./media/performance-tips/same-region.png) Azure Cosmos DBThe Azure Cosmos DB connection policy<a id="increase-threads"></a>

**Aumentare il numero di thread/attività**

Poiché le chiamate a Azure Cosmos DB vengono effettuate in rete, potrebbe essere necessario variare il grado di parallelismo delle richieste in modo che l'applicazione client trascorri il tempo minimo di attesa tra le richieste. Ad esempio, se si usa .NET [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), creare nell'ordine di centinaia di attività che leggono o scrivono in Azure Cosmos DB.

**Abilitare la rete accelerataEnable accelerated networking**
 
 Per ridurre la latenza e il jitter della CPU, è consigliabile abilitare la rete accelerata nelle macchine virtuali client. Vedere [Creare una macchina virtuale Windows con rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md) o Creare una macchina virtuale Linux con rete [accelerata.](../virtual-network/create-vm-accelerated-networking-cli.md)

## <a name="sdk-usage"></a>Uso dell'SDK
**Installare l'SDK più recente**

Agli SDK di Azure Cosmos DB vengono apportati continui miglioramenti per offrire prestazioni ottimali. Per determinare la versione di SDK più recente e verificare i miglioramenti, vedere le pagine relative agli [SDK di Azure Cosmos DB](sql-api-sdk-dotnet-standard.md).

**Usare le API di flussoUse stream APIs**

[.NET SDK V3](sql-api-sdk-dotnet-standard.md) contiene API di flusso che possono ricevere e restituire dati senza serializzazione. 

Le applicazioni di livello intermedio che non utilizzano le risposte direttamente dall'SDK ma le inoltrano ad altri livelli dell'applicazione possono trarre vantaggio dalle API di flusso. Vedere gli esempi di [gestione degli articoli](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) per esempi di gestione del flusso.

**Usare un client database Cosmos di Azure singleton per la durata dell'applicazioneUse a singleton Azure Cosmos DB client for the lifetime of your application**

Ogni `DocumentClient` `CosmosClient` istanza è thread-safe ed esegue un'efficiente gestione delle connessioni e la memorizzazione nella cache degli indirizzi quando si opera in modalità diretta. Per consentire una gestione efficiente delle connessioni e migliori prestazioni `AppDomain` del client SDK, è consigliabile usare una singola istanza per tutta la durata dell'applicazione.

   <a id="max-connection"></a>

**Aumentare System.Net MaxConnections per host quando si utilizza la modalità gateway**

Le richieste di database Cosmos di Azure vengono effettuate tramite HTTPS/REST quando si usa la modalità gateway. Sono soggetti al limite di connessione predefinito per nome host o indirizzo IP. Potrebbe essere necessario `MaxConnections` impostare un valore più alto (da 100 a 1.000) in modo che la libreria client possa usare più connessioni simultanee a Azure Cosmos DB. In .NET SDK 1.8.0 e versioni successive, il valore predefinito per [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) è 50. Per modificare il valore, è possibile impostare [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) su un valore superiore.

**Ottimizzare le query parallele per le raccolte partizionate**

SQL .NET SDK 1.9.0 e versioni successive supportano query parallele, che consentono di eseguire query su una raccolta partizionata in parallelo. Per altre informazioni, vedere [esempi di codice](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) correlati all'utilizzo con gli SDK. Le query parallele sono progettate per fornire una latenza e una velocità effettiva delle query migliori rispetto alla controparte seriale. Le query parallele forniscono due parametri che è possibile ottimizzare in base alle proprie esigenze:Parallel queries provide two parameters that you can tune to your requirements: 
- `MaxDegreeOfParallelism`controlla il numero massimo di partizioni su cui è possibile eseguire query in parallelo. 
- `MaxBufferedItemCount`controlla il numero di risultati pre-fetch.

***Grado di regolazione del parallelismo***

La query parallela funziona eseguendo query su più partizioni in parallelo. Tuttavia, i dati di una singola partizione vengono recuperati in modo seriale rispetto alla query. L'impostazione `MaxDegreeOfParallelism` in `MaxConcurrency` SDK [V2](sql-api-sdk-dotnet.md) o in SDK [V3](sql-api-sdk-dotnet-standard.md) sul numero di partizioni ha le migliori possibilità di ottenere la query più performante, a condizione che tutte le altre condizioni di sistema rimangano invariate. Se non si conosce il numero di partizioni, è possibile impostare il grado di parallelismo su un numero elevato. Il sistema sceglierà il minimo (numero di partizioni, input fornito dall'utente) come grado di parallelismo.

Si noti che le query parallele producono il massimo vantaggio se i dati vengono distribuiti uniformemente tra tutte le partizioni rispetto alla query. Se la raccolta partizionata è partizionata in modo che tutti o la maggior parte dei dati restituiti da una query siano concentrati in alcune partizioni (una partizione è il caso peggiore), tali partizioni colloficheranno le prestazioni della query.

***Ottimizzazione di MaxBufferedItemCount***
    
La query parallela è progettata per eseguire il prelettura dei risultati durante l'elaborazione da parte del client del batch corrente di risultati. Questa prelettura consente di migliorare la latenza complessiva di una query. Il `MaxBufferedItemCount` parametro limita il numero di risultati di prelettura. Impostare `MaxBufferedItemCount` sul numero previsto di risultati restituiti (o un numero superiore) per consentire alla query di ottenere il massimo vantaggio dalla prelettura.

La prelettura funziona allo stesso modo indipendentemente dal grado di parallelismo ed è presente un singolo buffer per i dati da tutte le partizioni.  

**Implementare il backoff in corrispondenza di intervalli RetryAfter**

Durante il test delle prestazioni, è necessario aumentare il carico fino a quando non viene limitata una piccola frequenza di richieste. Se le richieste vengono limitate, l'applicazione client deve eseguire il back off sulla limitazione per l'intervallo tra tentativi specificato dal server. Rispettare il backoff garantisce una quantità minima di tempo di attesa tra un tentativo e l'altro. 

Il supporto dei criteri di ripetizione dei tentativi è incluso in questi SDK:Retry policy support is included in these SDKs:
- Versione 1.8.0 e successive di [.NET SDK per SQL](sql-api-sdk-dotnet.md) e [Java SDK per SQL](sql-api-sdk-java.md)
- Versione 1.9.0 e successive dell'SDK di [Node.js per SQL](sql-api-sdk-node.md) e [Python SDK per SQL](sql-api-sdk-python.md)
- Tutte le versioni supportate degli SDK di [.NET Core](sql-api-sdk-dotnet-core.md) 

Per ulteriori informazioni, vedere [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
Nella versione 1.19 e successive di .NET SDK, è disponibile un meccanismo per la registrazione di informazioni diagnostiche aggiuntive e la risoluzione dei problemi di latenza, come illustrato nell'esempio seguente. È possibile registrare la stringa di diagnostica per le richieste che hanno una latenza di lettura più elevata. La stringa di diagnostica acquisita ti aiuterà a capire quante volte hai ricevuto 429 errori per una determinata richiesta.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Memorizzare nella cache gli URI dei documenti per una minore latenza di lettura**

Memorizzare nella cache gli URI dei documenti quando possibile per ottenere prestazioni di lettura ottimali. È necessario definire la logica per memorizzare nella cache l'ID risorsa quando si crea una risorsa. Le ricerche basate sugli ID di risorsa sono più veloci delle ricerche basate sui nomi, pertanto la memorizzazione nella cache di questi valori migliora le prestazioni.

   <a id="tune-page-size"></a>
**Ottimizzare le dimensioni della pagina per le query o i feed di lettura per ottenere prestazioni migliori**

Quando si esegue una lettura in blocco dei documenti `ReadDocumentFeedAsync`utilizzando la funzionalità di feed di lettura (ad esempio, ) o quando si esegue una query SQL, i risultati vengono restituiti in modo segmentato se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

Per ridurre il numero di round trip di rete necessari per recuperare tutti i risultati applicabili, è possibile aumentare le dimensioni della pagina utilizzando [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) per richiedere fino a 1.000 intestazioni. Quando è necessario visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre le dimensioni della pagina a 10 per ridurre la velocità effettiva utilizzata per le letture e le query.

> [!NOTE] 
> La `maxItemCount` proprietà non deve essere utilizzata solo per l'impaginazione. Il suo utilizzo principale è quello di migliorare le prestazioni delle query riducendo il numero massimo di elementi restituiti in una singola pagina.  

È anche possibile impostare le dimensioni della pagina usando gli SDK di Database Cosmos di Azure disponibili. La proprietà [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) in `FeedOptions` consente di impostare il numero massimo di elementi da restituire nell'operazione di enumerazione. Quando `maxItemCount` è impostato su -1, l'SDK trova automaticamente il valore ottimale, a seconda delle dimensioni del documento. Ad esempio:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Quando viene eseguita una query, i dati risultanti vengono inviati all'interno di un pacchetto TCP. Se si specifica un valore `maxItemCount`troppo basso per , il numero di viaggi necessari per inviare i dati all'interno del pacchetto TCP è elevato, il che influisce sulle prestazioni. Pertanto, se non si è certi `maxItemCount` del valore da impostare per la proprietà, è consigliabile impostarla su -1 e lasciare che l'SDK scelga il valore predefinito.

**Aumentare il numero di thread/attività**

Vedere [Aumentare il numero di thread/attività](#increase-threads) nella sezione Rete di questo articolo.

## <a name="indexing-policy"></a>Criterio di indicizzazione
 
**Escludere i percorsi inutilizzati dall'indicizzazione per scritture più velociExclude unused paths from indexing for faster writes**

I criteri di indicizzazione del database Cosmos di Azure consentono inoltre di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione usando i percorsi di indicizzazione (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludedPaths). I percorsi di indicizzazione possono migliorare le prestazioni di scrittura e ridurre l'archiviazione degli indici per gli scenari in cui i modelli di query sono noti in anticipo. Ciò è dovuto al fatto che i costi di indicizzazione sono direttamente correlati al numero di percorsi univoci indicizzati. Ad esempio, in questo codice viene illustrato come escludere un'intera sezione dei documenti (un sottoalbero) dall'indicizzazione utilizzando il carattere jolly "":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Velocità effettiva
<a id="measure-rus"></a>

**Misurare e ottimizzare per un utilizzo inferiore delle unità di richiesta/secondo**

Azure Cosmos DB offre un set completo di operazioni di database. Queste operazioni includono query relazionali e gerarchiche con funzioni definite dall'utente, stored procedure e trigger, tutte operazioni sui documenti all'interno di una raccolta di database. Il costo associato a ciascuna di queste operazioni varia a seconda della CPU, I/O e memoria necessari per completare l'operazione. Anziché pensare e gestire le risorse hardware, è possibile considerare un'unità di richiesta (RU) come una singola misura per le risorse necessarie per eseguire varie operazioni di database e soddisfare una richiesta di applicazione.

Il provisioning della velocità effettiva viene eseguito in base al numero di [unità](request-units.md) richiesta impostato per ogni contenitore. Richiesta Il consumo unitario viene valutato come una frequenza al secondo. Le applicazioni che superano la frequenza di unità di richiesta di cui è stato eseguito il provisioning per il contenitore sono limitate fino a quando la velocità non scende al di sotto del livello di provisioning per il contenitore. Se l'applicazione richiede un livello superiore di velocità effettiva, è possibile aumentare la velocità effettiva eseguendo il provisioning di unità richieste aggiuntive.

La complessità di una query influisce sul numero di unità richiesta utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine influenzano tutti il costo delle operazioni di query.

Per misurare l'overhead di qualsiasi operazione (creazione, aggiornamento o eliminazione), esaminare `RequestCharge` l'intestazione [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (o la proprietà equivalente in `ResourceResponse\<T>` o `FeedResponse\<T>` in .NET SDK) per misurare il numero di unità di richiesta utilizzate dalle operazioni:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

L'addebito della richiesta restituito in questa intestazione è una frazione della velocità effettiva di cui è stato eseguito il provisioning, ovvero 2.000 RU al secondo. Ad esempio, se la query precedente restituisce 1.000 documenti da 1 KB, il costo dell'operazione è 1.000. Pertanto, entro un secondo, il server rispetta solo due richieste di questo tipo prima di limitare le richieste successive. Per ulteriori informazioni, consultate [Unità richiesta](request-units.md) e [Calcolatore unità richiesta](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

Quando un client tenta di superare la velocità effettiva riservata per un account, non si verifica alcuna riduzione delle prestazioni nel server e nessun utilizzo della capacità effettiva oltre il livello riservato. Il server terminerà preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429). Verrà restituita [un'intestazione x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di tentare nuovamente la richiesta.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

Se si dispone di più client che operano cumulativamente in modo coerente al di sopra della frequenza delle richieste, il numero di tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. In questo caso, il client genera un DocumentClientException con codice di stato 429 per l'applicazione. 

È possibile modificare il numero di `RetryOptions` tentativi predefinito impostando il valore di nell'istanza. `ConnectionPolicy` Per impostazione predefinita, l'eccezione DocumentClientException con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Questo errore restituisce anche quando il numero di tentativi corrente è inferiore al numero massimo di tentativi, se il valore corrente è il valore predefinito di 9 o un valore definito dall'utente.

Il comportamento di ripetizione automatica dei tentativi consente di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni. Ma potrebbe non essere il comportamento migliore quando si esegue nozioni di riferimento sulle prestazioni, soprattutto quando si misura la latenza. La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per ulteriori informazioni, consultate [Unità richiesta](request-units.md).

**Per una maggiore produttività, progettare per documenti più piccoli**

L'addebito della richiesta (ovvero il costo di elaborazione della richiesta) di una determinata operazione è correlato direttamente alle dimensioni del documento. Le operazioni su documenti di grandi dimensioni costano più delle operazioni su documenti di piccole dimensioni.

## <a name="next-steps"></a>Passaggi successivi
Per un'applicazione di esempio usata per valutare Azure Cosmos DB per scenari ad alte prestazioni in alcuni computer client, vedere [Test delle prestazioni e scalabilità con Azure Cosmos DB.](performance-testing.md)

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere l'articolo relativo a [partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
