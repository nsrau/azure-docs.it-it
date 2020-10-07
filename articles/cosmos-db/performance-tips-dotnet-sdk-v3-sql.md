---
title: Suggerimenti per le prestazioni di Azure Cosmos DB per .NET SDK V3
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni di Azure Cosmos DB .NET V3 SDK.
author: j82w
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: jawilley
ms.custom: devx-track-dotnet
ms.openlocfilehash: f8e610531eaf3e7e5dbee9c40c88683a05029303
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802991"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Suggerimenti sulle prestazioni per Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB è un database distribuito veloce e flessibile con scalabilità uniforme con livelli di velocità effettiva e latenza garantiti. Non è necessario apportare modifiche sostanziali all'architettura o scrivere codice complesso per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API. Per altre informazioni, vedere [provisioning della velocità effettiva del contenitore](how-to-provision-container-throughput.md) o [provisioning del database](how-to-provision-database-throughput.md). 

Poiché Azure Cosmos DB si accede tramite chiamate di rete, è possibile effettuare ottimizzazioni lato client per ottenere prestazioni ottimali quando si usa [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Se si sta provando a migliorare le prestazioni del database, prendere in considerazione le opzioni illustrate nelle sezioni seguenti.

## <a name="hosting-recommendations"></a>Suggerimenti per l'hosting

**Per i carichi di lavoro a elevato utilizzo di query, usare Windows a 64 bit anziché l'elaborazione host Linux o Windows a 32 bit**

Per migliorare le prestazioni, è consigliabile l'elaborazione host Windows a 64 bit. L'SDK SQL include un file ServiceInterop.dll nativo che consente di analizzare e ottimizzare le query in locale. Il file ServiceInterop.dll è supportato solo nella piattaforma Windows x64. 

Per Linux e altre piattaforme non supportate in cui ServiceInterop.dll non è disponibile, viene effettuata una chiamata di rete aggiuntiva al gateway per ottenere la query ottimizzata. 

Per impostazione predefinita, i quattro tipi di applicazione elencati di seguito utilizzano l'elaborazione host a 32 bit. Per modificare l'elaborazione dell'host nell'elaborazione a 64 bit per il tipo di applicazione, eseguire le operazioni seguenti:

- **Per le applicazioni eseguibili**: nella finestra delle **proprietà del progetto** , nel riquadro **Compila** , impostare la [destinazione della piattaforma](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019&preserve-view=true) su **x64**.

- **Per i progetti di test basati su VSTest**: nel menu **test** di Visual Studio **selezionare**  >  **impostazioni test**test, quindi impostare **architettura del processore predefinita** su **x64**.

- **Per le applicazioni Web ASP.NET distribuite localmente**: selezionare **strumenti**  >  **Opzioni**  >  **progetti e soluzioni**progetti  >  **Web**, quindi selezionare **Usa la versione a 64 bit di IIS Express per i siti Web e i progetti**.

- **Per le applicazioni web ASP.NET distribuite in Azure**: nella portale di Azure, in **Impostazioni applicazione**, selezionare la piattaforma a **64 bit** .

> [!NOTE] 
> Per impostazione predefinita, i nuovi progetti di Visual Studio sono impostati su **qualsiasi CPU**. Si consiglia di impostare il progetto su **x64** in modo che non passi a **x86**. Un progetto impostato su **qualsiasi CPU** può passare facilmente a **x86** se viene aggiunta una dipendenza solo x86.<br/>
> Il file di ServiceInterop.dll deve trovarsi nella cartella da cui viene eseguita la DLL SDK. Questo dovrebbe essere un problema solo se si copiano le dll manualmente o si dispone di sistemi di compilazione o distribuzione personalizzati.
    
**Attivare Garbage Collection lato server**

In alcuni casi, la riduzione della frequenza di Garbage Collection può risultare utile. In .NET impostare [gcserver](https://docs.microsoft.com/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) su `true` .

**Scalabilità orizzontale del carico di lavoro del client**

Se si sta eseguendo il test a livelli di velocità effettiva elevata o a tariffe maggiori di 50.000 unità richiesta al secondo (UR/sec), l'applicazione client potrebbe diventare un collo di bottiglia del carico di lavoro. Questo è dovuto al fatto che la macchina virtuale potrebbe limitare l'utilizzo della CPU o della rete. In questo caso, è possibile continuare a effettuare il push dell'account Azure Cosmos DB aumentando il numero di istanze delle applicazioni client in più server.

> [!NOTE] 
> Un utilizzo elevato della CPU può causare un aumento della latenza e delle eccezioni di timeout richieste.

## <a name="networking"></a>Rete
<a id="direct-connection"></a>

**Criteri di connessione: usare la modalità di connessione diretta**

Il modo in cui un client si connette a Azure Cosmos DB ha implicazioni importanti sulle prestazioni, specialmente per la latenza lato client osservata. Per la configurazione dei criteri di connessione client sono disponibili due impostazioni di configurazione chiave, ovvero la *modalità* di connessione e il *protocollo*di connessione. Le due modalità di connessione disponibili sono:

   * Modalità diretta (impostazione predefinita)

     La modalità diretta supporta la connettività tramite il protocollo TCP ed è la modalità di connettività predefinita se si usa [Microsoft. Azure. Cosmos/. NET V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3). La modalità diretta garantisce prestazioni migliori e richiede un minor numero di hop di rete rispetto alla modalità gateway.

   * Modalità gateway
      
     Se l'applicazione viene eseguita all'interno di una rete aziendale con restrizioni rigide del firewall, la modalità Gateway è la scelta migliore, perché usa la porta HTTPS standard e un singolo endpoint. 
     
     Il compromesso in termini di prestazioni, tuttavia, è che la modalità Gateway prevede un hop di rete aggiuntivo ogni volta che i dati vengono letti o scritti in Azure Cosmos DB. In modo che la modalità diretta offra prestazioni migliori in quanto sono presenti meno hop di rete. Si consiglia anche la modalità di connessione del gateway quando si eseguono applicazioni in ambienti con un numero limitato di connessioni socket.

     Quando si usa l'SDK in funzioni di Azure, in particolare nel [piano a consumo](../azure-functions/functions-scale.md#consumption-plan), tenere presenti i [limiti correnti per le connessioni](../azure-functions/manage-connections.md). In tal caso, la modalità gateway potrebbe essere migliore se si lavora anche con altri client basati su HTTP all'interno dell'applicazione funzioni di Azure.
     
Quando si usa il protocollo TCP in modalità diretta, oltre alle porte del gateway, è necessario assicurarsi che l'intervallo di porte compreso tra 10000 e 20000 sia aperto, perché Azure Cosmos DB usa porte TCP dinamiche. Quando si usa la modalità diretta negli [endpoint privati](./how-to-configure-private-endpoints.md), è necessario aprire l'intera gamma di porte TCP da 0 a 65535. Per impostazione predefinita, le porte sono aperte per la configurazione della macchina virtuale di Azure standard. Se queste porte non sono aperte e si tenta di usare TCP, si riceverà un errore "503 servizio non disponibile". 

La tabella seguente illustra le modalità di connettività disponibili per le varie API e le porte del servizio usate per ogni API:

|Modalità di connessione  |Protocollo supportato  |SDK supportati  |API/porta servizio  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Tutti gli SDK    |   SQL (443), MongoDB (10250, 10255, 10256), tabella (443), Cassandra (10350), Graph (443) <br><br> La porta 10250 esegue il mapping a un'API Azure Cosmos DB predefinita per l'istanza di MongoDB senza replica geografica e le porte 10255 e 10256 sono mappate all'istanza con la replica geografica.   |
|Connessione diretta    |     TCP    |  .NET SDK    | Quando si usano gli endpoint pubblici/di servizio: porte nell'intervallo da 10000 a 20000<br><br>Quando si usano gli endpoint privati: porte nell'intervallo da 0 a 65535 |

Azure Cosmos DB offre un modello di programmazione RESTful semplice e aperto su HTTPS. DocumentDB offre anche un protocollo TCP efficiente, con un modello di comunicazione di tipo RESTful disponibile tramite .NET SDK per client. Il protocollo TCP USA Transport Layer Security (TLS) per l'autenticazione iniziale e la crittografia del traffico. Per prestazioni ottimali, usare il protocollo TCP quando possibile.

Per SDK V3, configurare la modalità di connessione quando si crea l' `CosmosClient` istanza di in `CosmosClientOptions` . Tenere presente che la modalità diretta è quella predefinita.

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Poiché TCP è supportato solo in modalità diretta, se si usa la modalità Gateway, il protocollo HTTPS viene sempre usato per comunicare con il gateway.

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Stabilire una connessione a Azure Cosmos DB con diverse modalità di connessione e protocolli." border="false":::

**Esaurimento della porta temporanea**

Se viene visualizzato un volume di connessione elevato o un utilizzo elevato delle porte nelle istanze, verificare innanzitutto che le istanze client siano singleton. In altre parole, le istanze del client devono essere univoche per la durata dell'applicazione.

Quando è in esecuzione sul protocollo TCP, il client ottimizza la latenza usando le connessioni di lunga durata. Questo si differenzia dal protocollo HTTPS, che termina le connessioni dopo due minuti di inattività.

Negli scenari in cui si dispone dell'accesso sparse e si nota un numero di connessioni superiore rispetto all'accesso in modalità Gateway, è possibile:

* Configurare la proprietà [CosmosClientOptions. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) su `PrivatePortPool` (efficace con le versioni di Framework 4.6.1 e versioni successive e .NET Core 2,0 e versioni successive). Questa proprietà consente all'SDK di usare un piccolo pool di porte temporanee per diversi endpoint di destinazione Azure Cosmos DB.
* Configurare la proprietà [CosmosClientOptions. idleconnectiontimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) su un valore maggiore o uguale a 10 minuti. I valori consigliati sono compresi tra 20 minuti e 24 ore.

<a id="same-region"></a>

**Per le prestazioni, collocare i client nella stessa area di Azure**

Quando possibile, inserire tutte le applicazioni che chiamano Azure Cosmos DB nella stessa area del database di Azure Cosmos DB. Ecco un confronto approssimativo: le chiamate a Azure Cosmos DB all'interno della stessa area terminano entro 1 millisecondi (MS) a 2 ms, ma la latenza tra la costa occidentale e quella orientale degli Stati Uniti è superiore a 50 ms. Questa latenza può variare dalla richiesta alla richiesta, a seconda della route eseguita dalla richiesta mentre passa dal client al limite del Data Center di Azure. 

Per ottenere la latenza più bassa possibile, è possibile verificare che l'applicazione chiamante si trovi nella stessa area di Azure dell'endpoint di Azure Cosmos DB di cui è stato effettuato il provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Stabilire una connessione a Azure Cosmos DB con diverse modalità di connessione e protocolli." border="false":::

   <a id="increase-threads"></a>

**Aumentare il numero di thread/attività**

Poiché le chiamate a Azure Cosmos DB vengono effettuate sulla rete, potrebbe essere necessario variare il grado di concorrenza delle richieste in modo che l'applicazione client spenda tempo minimo di attesa tra le richieste. Se ad esempio si usa il [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx).NET, creare in base all'ordine di centinaia di attività che leggono o scrivono Azure Cosmos DB.

**Abilita rete accelerata**
 
Per ridurre la latenza e la jitter della CPU, è consigliabile abilitare la rete accelerata nelle macchine virtuali client. Per altre informazioni, vedere [creare una macchina virtuale Windows con rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md) o [creare una macchina virtuale Linux con rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Uso dell'SDK

**Installare l'SDK più recente**

Agli SDK di Azure Cosmos DB vengono apportati continui miglioramenti per offrire prestazioni ottimali. Per determinare l'SDK più recente e verificare i miglioramenti, vedere [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md).

**Usare le API di flusso**

[.NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) contiene API di flusso che possono ricevere e restituire dati senza serializzazione. 

Le applicazioni di livello intermedio che non utilizzano risposte direttamente dall'SDK, ma le inviano ad altri livelli applicazione possono trarre vantaggio dalle API del flusso. Per esempi di gestione del flusso, vedere gli esempi di [gestione degli elementi](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) .

**Usare un client Azure Cosmos DB singleton per la durata dell'applicazione**

Ogni `CosmosClient` istanza è thread-safe ed esegue una gestione efficiente delle connessioni e la memorizzazione nella cache degli indirizzi quando opera in modalità diretta. Per consentire una gestione efficiente delle connessioni e prestazioni migliori del client SDK, è consigliabile usare una singola istanza per `AppDomain` per la durata dell'applicazione.

Quando si lavora con funzioni di Azure, è necessario che le istanze seguano anche le [linee guida](../azure-functions/manage-connections.md#static-clients) esistenti e mantengano una sola istanza.

<a id="max-connection"></a>

**Disabilitare la risposta al contenuto durante le operazioni di scrittura**

Per i carichi di lavoro con payload di creazione pesanti, impostare l' `EnableContentResponseOnWrite` opzione Request su `false` . Il servizio non restituirà più la risorsa creata o aggiornata all'SDK. In genere, poiché l'applicazione dispone dell'oggetto che viene creato, non è necessario che il servizio lo restituisca. I valori dell'intestazione sono ancora accessibili, ad esempio un addebito per la richiesta. La disabilitazione della risposta al contenuto può contribuire a migliorare le prestazioni, perché l'SDK non deve più allocare memoria o serializzare il corpo della risposta. Consente inoltre di ridurre l'utilizzo della larghezza di banda di rete per migliorare le prestazioni.  

```csharp
ItemRequestOption requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Abilitare l'ottimizzazione bulk per la velocità effettiva anziché la latenza**

Per *gli scenari in cui* il carico di lavoro richiede una quantità elevata di velocità effettiva e la latenza non è altrettanto importante. Per ulteriori informazioni su come abilitare la funzionalità bulk e per informazioni sugli scenari per cui deve essere utilizzata, vedere [Introduzione al supporto bulk](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk).

**Aumentare System.Net MaxConnections per host quando si usa la modalità gateway**

Quando si usa la modalità Gateway, le richieste di Azure Cosmos DB vengono eseguite su HTTPS/REST. Esse sono soggette al limite di connessione predefinito per nome host o indirizzo IP. Potrebbe essere necessario impostare `MaxConnections` su un valore più alto (da 100 a 1.000) in modo che la libreria client possa usare più connessioni simultanee per Azure Cosmos DB. In .NET SDK 1.8.0 e versioni successive il valore predefinito per [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) è 50. Per modificare il valore, è possibile impostare [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) su un valore più alto.

**Ottimizzare le query parallele per le raccolte partizionate**

SQL .NET SDK supporta le query parallele, che consentono di eseguire query in un contenitore partizionato in parallelo. Per altre informazioni, vedere [esempi di codice](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) correlati all'utilizzo con gli SDK. Le query parallele sono progettate per offrire una migliore latenza e velocità effettiva delle query rispetto alla relativa controparte seriale. 

Le query parallele forniscono due parametri che è possibile ottimizzare per soddisfare i requisiti: 

- **MaxConcurrency**: controlla il numero massimo di partizioni su cui è possibile eseguire query in parallelo.

   La query parallela funziona eseguendo query su più partizioni in parallelo. Tuttavia, i dati di una singola partizione vengono recuperati in modo seriale rispetto alla query. L'impostazione di `MaxConcurrency` [SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) sul numero di partizioni ha la possibilità di ottenere la query più efficiente, purché tutte le altre condizioni del sistema rimangano invariate. Se non si conosce il numero di partizioni, è possibile impostare il grado di parallelismo su un numero elevato. Il sistema sceglierà il numero minimo di partizioni, ovvero l'input fornito dall'utente, come grado di parallelismo.

    Le query parallele producono i maggiori vantaggi se i dati vengono distribuiti uniformemente tra tutte le partizioni rispetto alla query. Se la raccolta partizionata è partizionata in modo che tutti o la maggior parte dei dati restituiti da una query siano concentrati in poche partizioni (una partizione è il caso peggiore), tali partizioni faranno un collo di bottiglia delle prestazioni della query.
   
- **MaxBufferedItemCount**: controlla il numero di risultati prerecuperati.

   La query parallela è progettata per la prelettura dei risultati mentre il client elabora il batch di risultati corrente. Il recupero preliminare consente di migliorare la latenza complessiva di una query. Il `MaxBufferedItemCount` parametro limita il numero di risultati prerecuperati. Impostare sul `MaxBufferedItemCount` numero previsto di risultati restituiti (o un numero più elevato) per consentire alla query di ricevere il vantaggio massimo dal recupero preliminare.

   Il recupero preliminare funziona allo stesso modo, indipendentemente dal grado di parallelismo, ed esiste un solo buffer per i dati di tutte le partizioni.  

**Implementare il backoff in corrispondenza di intervalli RetryAfter**

Durante i test delle prestazioni, è necessario aumentare il carico fino a quando non viene limitata una piccola frequenza di richieste. Se le richieste sono limitate, l'applicazione client deve disattivare la limitazione per l'intervallo tra tentativi specificato dal server. Il rispetto dei backoff contribuisce a garantire una quantità minima di tempo di attesa tra i tentativi. 

Per ulteriori informazioni, vedere [RetryAfter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
È disponibile un meccanismo per la registrazione di ulteriori informazioni di diagnostica e la risoluzione dei problemi di latenza, come illustrato nell'esempio seguente. È possibile registrare la stringa di diagnostica per le richieste con una latenza di lettura più elevata. La stringa di diagnostica acquisita consentirà di comprendere il numero di volte in cui si è ricevuto un errore *429* per una determinata richiesta.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**Aumentare il numero di thread/attività**

Vedere [aumentare il numero di thread/attività](#increase-threads) nella sezione rete di questo articolo.

## <a name="indexing-policy"></a>Criterio di indicizzazione
 
**Escludere i percorsi non usati dall'indicizzazione per scritture più veloci**

I criteri di indicizzazione Azure Cosmos DB consentono inoltre di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione usando i percorsi di indicizzazione (IndexingPolicy. IncludedPaths e IndexingPolicy. ExcludedPaths). 

L'indicizzazione solo dei percorsi necessari può migliorare le prestazioni di scrittura, ridurre i addebiti delle unità richiesta per le operazioni di scrittura e ridurre l'archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo. Ciò è dovuto al fatto che i costi di indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati. Il codice seguente, ad esempio, illustra come escludere un'intera sezione dei documenti (un sottoalbero) dall'indicizzazione usando il carattere jolly "*":

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Velocità effettiva
<a id="measure-rus"></a>

**Misurazione e ottimizzazione per un utilizzo inferiore di Ur/s**

Azure Cosmos DB offre un'ampia gamma di operazioni di database. Queste operazioni includono query relazionali e gerarchiche con file UDF (Universal Disk Format), stored procedure e trigger, che operano sui documenti all'interno di una raccolta di database. 

I costi associati a ognuna di queste operazioni variano a seconda della CPU, dell'i/o e della memoria necessari per completare l'operazione. Anziché considerare e gestire le risorse hardware, è possibile considerare un'unità di richiesta come singola misura per le risorse necessarie per eseguire diverse operazioni di database e gestire una richiesta dell'applicazione.

Viene eseguito il provisioning della velocità effettiva in base al numero di [unità richiesta](request-units.md) impostate per ogni contenitore. Il consumo delle unità richiesta viene valutato come una tariffa di unità al secondo. Le applicazioni che superano la frequenza di unità richiesta di cui è stato effettuato il provisioning per il contenitore sono limitate fino a quando la frequenza scende sotto il livello di provisioning per il contenitore. Se l'applicazione richiede un livello superiore di velocità effettiva, è possibile aumentare la velocità effettiva effettuando il provisioning di unità richiesta aggiuntive.

La complessità di una query influiscono sul numero di unità richiesta utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di file UDF e le dimensioni del set di dati di origine influenzano tutti il costo delle operazioni di query.

Per misurare l'overhead di qualsiasi operazione (create, Update o DELETE), esaminare l'intestazione [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (o la proprietà equivalente `RequestCharge` in `ResourceResponse\<T>` o `FeedResponse\<T>` in .NET SDK) per misurare il numero di unità richiesta utilizzate dalle operazioni:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

L'addebito della richiesta restituito in questa intestazione è una frazione della velocità effettiva di cui è stato effettuato il provisioning (ovvero 2.000 UR/sec). Se, ad esempio, la query precedente restituisce documenti da 1.000 1 KB, il costo dell'operazione è 1.000. Quindi, entro un secondo, il server rispetta solo due richieste di questo tipo prima della frequenza, limita le richieste successive. Per altre informazioni, vedere [unità richiesta](request-units.md) e [calcolatore unità richiesta](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

Quando un client tenta di superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni nel server e non si utilizza la capacità di velocità effettiva oltre il livello riservato. Il server termina preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429). Restituisce un'intestazione [x-ms-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di ritentare la richiesta.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

Se si dispone di più di un client che funziona cumulativamente in modo costante al di sopra della frequenza delle richieste, il numero di tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. In questo caso, il client genera un'eccezione Cosmosexception con codice di stato 429 per l'applicazione. 

È possibile modificare il numero di tentativi predefinito impostando `RetryOptions` sull' `CosmosClientOptions` istanza. Per impostazione predefinita, Cosmosexception con codice di stato 429 viene restituito dopo un tempo di attesa cumulativo di 30 secondi se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Questo errore viene restituito anche quando il numero di tentativi corrente è inferiore al numero massimo di tentativi, se il valore corrente è quello predefinito 9 o un valore definito dall'utente.

Il comportamento automatico dei tentativi consente di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni. Ma potrebbe non essere il comportamento migliore quando si esegue un benchmark delle prestazioni, soprattutto quando si sta misurando la latenza. La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare picchi di latenza durante gli esperimenti sulle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste funzionino al di sotto della frequenza delle richieste riservate. 

Per altre informazioni, vedere [unità richiesta](request-units.md).

**Per una maggiore velocità effettiva, progettazione per documenti più piccoli**

L'addebito per le richieste, ovvero il costo di elaborazione delle richieste, di un'operazione specifica è correlato direttamente alle dimensioni del documento. Le operazioni sui documenti di grandi dimensioni costano più delle operazioni sui documenti di piccole dimensioni.

## <a name="next-steps"></a>Passaggi successivi
Per un'applicazione di esempio usata per valutare Azure Cosmos DB per scenari a prestazioni elevate in alcuni computer client, vedere [test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere l'articolo relativo a [partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
