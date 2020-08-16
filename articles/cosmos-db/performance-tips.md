---
title: Suggerimenti per le prestazioni di Azure Cosmos DB per .NET SDK v2
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni di Azure Cosmos DB .NET v2 SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/26/2020
ms.author: sngun
ms.openlocfilehash: 3e15adcac184a0609de3197181cb8c475a962e8d
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258356"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Suggerimenti sulle prestazioni per Azure Cosmos DB e .NET SDK v2

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB è un database distribuito veloce e flessibile, facilmente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche sostanziali all'architettura o scrivere codice complesso per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API. Per altre informazioni, vedere [Effettuare il provisioning della velocità effettiva per un contenitore](how-to-provision-container-throughput.md) oppure [Effettuare il provisioning della velocità effettiva per un database](how-to-provision-database-throughput.md). Tuttavia, poiché si accede a Azure Cosmos DB tramite chiamate di rete, è possibile apportare ottimizzazioni lato client per ottenere prestazioni ottimali quando si usa [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Se quindi si sta provando a migliorare le prestazioni del database, prendere in considerazione le opzioni seguenti:

## <a name="upgrade-to-the-net-v3-sdk"></a>Eseguire l'aggiornamento a .NET V3 SDK

[.NET V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) è stato rilasciato. Se si usa .NET V3 SDK, vedere la [Guida alle prestazioni di .NET V3](performance-tips-dotnet-sdk-v3-sql.md) per le informazioni seguenti:

- Per impostazione predefinita, viene impostata la modalità TCP diretta
- Supporto dell'API Stream
- Supporto del serializzatore personalizzato per consentire System.Text.JSsull'utilizzo
- Batch integrato e supporto bulk

## <a name="hosting-recommendations"></a>Suggerimenti per l'hosting

**Per i carichi di lavoro a elevato utilizzo di query, usare Windows a 64 bit anziché l'elaborazione host Linux o Windows a 32 bit**

Per migliorare le prestazioni, è consigliabile l'elaborazione host Windows a 64 bit. L'SDK SQL include un file ServiceInterop.dll nativo che consente di analizzare e ottimizzare le query in locale. Il file ServiceInterop.dll è supportato solo nella piattaforma Windows x64. Per Linux e altre piattaforme non supportate in cui ServiceInterop.dll non è disponibile, viene effettuata una chiamata di rete aggiuntiva al gateway per ottenere la query ottimizzata. Per impostazione predefinita, nei seguenti tipi di applicazioni viene utilizzata l'elaborazione host a 32 bit. Per modificare l'elaborazione dell'host nell'elaborazione a 64 bit, attenersi alla procedura seguente, in base al tipo di applicazione:

- Per le applicazioni eseguibili, è possibile modificare l'elaborazione dell'host impostando la [destinazione della piattaforma](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) su **x64**  nella finestra delle **proprietà del progetto** , nella scheda **Compila** .

- Per i progetti di test basati su VSTest, è possibile modificare l'elaborazione dell' **host selezionando**  >  **impostazioni test**di test  >  **Architettura processore predefinita come x64** nel menu **test** di Visual Studio.

- Per le applicazioni Web ASP.NET distribuite localmente, è possibile modificare l'elaborazione host selezionando **Usa la versione a 64 bit di IIS Express per siti e progetti web** in **strumenti**  >  **Opzioni**  >  **progetti e soluzioni**progetti  >  **Web**.

- Per le applicazioni Web ASP.NET distribuite in Azure, è possibile modificare l'elaborazione degli host selezionando la piattaforma a **64 bit** nelle **impostazioni dell'applicazione** nel portale di Azure.

> [!NOTE] 
> Per impostazione predefinita, i nuovi progetti di Visual Studio sono impostati su **qualsiasi CPU**. Si consiglia di impostare il progetto su **x64** in modo che non passi a **x86**. Un progetto impostato su **qualsiasi CPU** può passare facilmente a **x86** se viene aggiunta una dipendenza solo x86.<br/>
> ServiceInterop.dll deve trovarsi nella cartella da cui viene eseguita la DLL SDK. Questo dovrebbe essere un problema solo se si copiano le dll manualmente o si hanno sistemi di compilazione/distribuzione personalizzati.
    
**Attivare Garbage Collection sul lato server (GC)**

In alcuni casi, la riduzione della frequenza di Garbage Collection può risultare utile. In .NET impostare [gcserver](https://msdn.microsoft.com/library/ms229357.aspx) su `true` .

**Scalabilità orizzontale del carico di lavoro del client**

Se si sta eseguendo il test a livelli di velocità effettiva elevati (più di 50.000 UR/sec), l'applicazione client potrebbe diventare il collo di bottiglia a causa della limitazione della CPU o dell'utilizzo della rete. In questo caso, è possibile continuare a effettuare il push dell'account Azure Cosmos DB aumentando il numero di istanze delle applicazioni client in più server.

> [!NOTE] 
> Un utilizzo elevato della CPU può causare un aumento della latenza e delle eccezioni di timeout richieste.

## <a name="networking"></a>Funzionalità di rete di <a id="networking"></a>

**Criteri di connessione: usare la modalità di connessione diretta**

Il modo in cui un client si connette a Azure Cosmos DB ha implicazioni importanti sulle prestazioni, specialmente per la latenza lato client osservata. Per la configurazione dei criteri di connessione client sono disponibili due impostazioni di configurazione chiave, ovvero la *modalità* di connessione e il *protocollo*di connessione.  Le due modalità disponibili sono:

  * Modalità Gateway (impostazione predefinita)
      
    La modalità Gateway è supportata in tutte le piattaforme SDK ed è l'impostazione predefinita configurata per il [Microsoft.Azure.DocUMENTDB SDK](sql-api-sdk-dotnet.md). Se l'applicazione viene eseguita all'interno di una rete aziendale con restrizioni rigide del firewall, la modalità Gateway è la scelta migliore perché usa la porta HTTPS standard e un singolo endpoint. Il compromesso in termini di prestazioni, tuttavia, è che la modalità Gateway prevede un hop di rete aggiuntivo ogni volta che i dati vengono letti o scritti in Azure Cosmos DB. In modo che la modalità diretta offra prestazioni migliori in quanto sono presenti meno hop di rete. Si consiglia anche la modalità di connessione del gateway quando si eseguono applicazioni in ambienti con un numero limitato di connessioni socket.

    Quando si usa l'SDK in funzioni di Azure, in particolare nel [piano a consumo](../azure-functions/functions-scale.md#consumption-plan), tenere presenti i [limiti correnti per le connessioni](../azure-functions/manage-connections.md). In tal caso, la modalità gateway potrebbe essere migliore se si lavora anche con altri client basati su HTTP all'interno dell'applicazione funzioni di Azure.

  * Modalità diretta

    La modalità diretta supporta la connettività tramite il protocollo TCP.
     
Quando si usa TCP in modalità diretta, oltre alle porte del gateway, è necessario assicurarsi che l'intervallo di porte compreso tra 10000 e 20000 sia aperto perché Azure Cosmos DB usa porte TCP dinamiche. Quando si usa la modalità diretta negli [endpoint privati](./how-to-configure-private-endpoints.md), è necessario aprire l'intera gamma di porte TCP, da 0 a 65535. Se queste porte non sono aperte e si tenta di usare il protocollo TCP, si riceverà un errore di 503 servizio non disponibile. La tabella seguente illustra le modalità di connettività disponibili per le varie API e le porte del servizio usate per ogni API:

|Modalità di connessione  |Protocollo supportato  |SDK supportati  |API/porta servizio  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Tutti gli SDK    |   SQL (443), MongoDB (10250, 10255, 10256), tabella (443), Cassandra (10350), Graph (443) <br> La porta 10250 esegue il mapping a un'API Azure Cosmos DB predefinita per l'istanza di MongoDB senza replica geografica. Mentre le porte 10255 e 10256 sono mappate all'istanza con replica geografica.   |
|Connessione diretta    |     TCP    |  .NET SDK    | Quando si usano gli endpoint pubblici/di servizio: porte nell'intervallo da 10000 a 20000<br>Quando si usano endpoint privati: porte nell'intervallo da 0 a 65535 |

Azure Cosmos DB offre un modello di programmazione RESTful semplice e aperto su HTTPS. DocumentDB offre anche un protocollo TCP efficiente, con un modello di comunicazione di tipo RESTful disponibile tramite .NET SDK per client. Il protocollo TCP usa TLS per l'autenticazione iniziale e la crittografia del traffico. Per prestazioni ottimali, usare il protocollo TCP quando possibile.

Per il Microsoft.Azure.DocSDK umentDB, è possibile configurare la modalità di connessione durante la costruzione dell' `DocumentClient` istanza di usando il `ConnectionPolicy` parametro. Se si usa la modalità diretta, è anche possibile impostare `Protocol` utilizzando il `ConnectionPolicy` parametro.

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Poiché TCP è supportato solo in modalità diretta, se si usa la modalità Gateway, il protocollo HTTPS viene sempre usato per comunicare con il gateway e il `Protocol` valore in `ConnectionPolicy` viene ignorato.

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Criterio di connessione Azure Cosmos DB" border="false":::

**Esaurimento della porta temporanea**

Se viene visualizzato un volume di connessione elevato o un utilizzo elevato delle porte nelle istanze, verificare innanzitutto che le istanze client siano singleton. In altre parole, le istanze del client devono essere univoche per la durata dell'applicazione.

Quando è in esecuzione sul protocollo TCP, il client ottimizza la latenza usando le connessioni di lunga durata anziché il protocollo HTTPS, che termina le connessioni dopo 2 minuti di inattività.

Negli scenari in cui si dispone dell'accesso di tipo sparse e si nota un numero di connessioni superiore rispetto all'accesso in modalità Gateway, è possibile:

* Configurare la proprietà [ConnectionPolicy. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) su `PrivatePortPool` (effetto con la versione del Framework>= 4.6.1 e .net core versione >= 2,0): questa proprietà consente all'SDK di usare un piccolo pool di porte temporanee per endpoint di destinazione Azure Cosmos DB diversi.
* Configurare la proprietà [ConnectionPolicy. idleconnectiontimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) deve essere maggiore o uguale a 10 minuti. I valori consigliati sono compresi tra 20 minuti e 24 ore.

**Chiamare OpenAsync per evitare la latenza di avvio alla prima richiesta**

Per impostazione predefinita, la prima richiesta ha una latenza più elevata perché deve recuperare la tabella di routing degli indirizzi. Quando si usa [SDK v2](sql-api-sdk-dotnet.md), chiamare `OpenAsync()` una sola volta durante l'inizializzazione per evitare questa latenza di avvio alla prima richiesta. La chiamata ha un aspetto simile al seguente: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` genererà richieste per ottenere la tabella di routing degli indirizzi per tutti i contenitori nell'account. Per gli account che dispongono di molti contenitori ma la cui applicazione accede a un subset di essi, `OpenAsync` genera una quantità di traffico superflua che rende l'inizializzazione lenta. Pertanto `OpenAsync` , l'utilizzo di potrebbe non essere utile in questo scenario perché rallenta l'avvio dell'applicazione.

**Per le prestazioni, collocare i client nella stessa area di Azure**

Quando possibile, inserire tutte le applicazioni che chiamano Azure Cosmos DB nella stessa area del database di Azure Cosmos DB. Ecco un confronto approssimativo: le chiamate a Azure Cosmos DB all'interno della stessa area vengono completate entro 1 ms per 2 ms, ma la latenza tra la costa occidentale e quella orientale degli Stati Uniti è superiore a 50 ms. Questa latenza può variare dalla richiesta alla richiesta, a seconda della route eseguita dalla richiesta mentre passa dal client al limite del Data Center di Azure. Per ottenere la latenza più bassa possibile, è possibile verificare che l'applicazione chiamante si trovi nella stessa area di Azure dell'endpoint del Azure Cosmos DB di cui è stato effettuato il provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Criterio di connessione Azure Cosmos DB" border="false":::

**Aumentare il numero di thread/attività**
<a id="increase-threads"></a>

Poiché le chiamate a Azure Cosmos DB vengono effettuate sulla rete, potrebbe essere necessario variare il grado di parallelismo delle richieste in modo che l'applicazione client spenda tempo minimo di attesa tra le richieste. Se ad esempio si usa il [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx).NET, creare in base all'ordine di centinaia di attività che leggono o scrivono Azure Cosmos DB.

**Abilita rete accelerata**
 
Per ridurre la latenza e la jitter della CPU, è consigliabile abilitare la rete accelerata nelle macchine virtuali client. Vedere [creare una macchina virtuale Windows con rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md) o [creare una macchina virtuale Linux con rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Uso dell'SDK

**Installare l'SDK più recente**

Agli SDK di Azure Cosmos DB vengono apportati continui miglioramenti per offrire prestazioni ottimali. Per determinare la versione di SDK più recente e verificare i miglioramenti, vedere le pagine relative agli [SDK di Azure Cosmos DB](sql-api-sdk-dotnet-standard.md).

**Usare un client Azure Cosmos DB singleton per la durata dell'applicazione**

Ogni `DocumentClient` istanza è thread-safe ed esegue una gestione efficiente delle connessioni e la memorizzazione nella cache degli indirizzi quando si opera in modalità diretta. Per consentire una gestione efficiente delle connessioni e prestazioni migliori del client SDK, è consigliabile usare una singola istanza per `AppDomain` per la durata dell'applicazione.

**Aumentare System.Net MaxConnections per host quando si usa la modalità gateway**

Quando si usa la modalità Gateway, le richieste di Azure Cosmos DB vengono eseguite su HTTPS/REST. Sono soggette al limite di connessione predefinito per ogni nome host o indirizzo IP. Potrebbe essere necessario impostare `MaxConnections` su un valore più alto (da 100 a 1.000), in modo che la libreria client possa usare più connessioni simultanee per Azure Cosmos DB. In .NET SDK 1.8.0 e versioni successive il valore predefinito per [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) è 50. Per modificare il valore, è possibile impostare [Documents. client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) su un valore superiore.

**Ottimizzare le query parallele per le raccolte partizionate**

SQL .NET SDK 1.9.0 e versioni successive supportano le query parallele, che consentono di eseguire query in una raccolta partizionata in parallelo. Per altre informazioni, vedere [esempi di codice](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) correlati all'utilizzo con gli SDK. Le query parallele sono progettate per offrire una migliore latenza e velocità effettiva delle query rispetto alla relativa controparte seriale. Le query parallele forniscono due parametri che è possibile ottimizzare per soddisfare i requisiti: 
- `MaxDegreeOfParallelism` Controlla il numero massimo di partizioni su cui è possibile eseguire query in parallelo. 
- `MaxBufferedItemCount` Controlla il numero di risultati prerecuperati.

***Ottimizzazione del grado di parallelismo***

La query parallela funziona eseguendo query su più partizioni in parallelo. Tuttavia, i dati di una singola partizione vengono recuperati in modo seriale rispetto alla query. L'impostazione di `MaxDegreeOfParallelism` [SDK v2](sql-api-sdk-dotnet.md) sul numero di partizioni ha la possibilità migliore di ottenere la query più efficiente, purché tutte le altre condizioni del sistema rimangano invariate. Se non si conosce il numero di partizioni, è possibile impostare il grado di parallelismo su un numero elevato. Il sistema sceglierà il numero minimo di partizioni, ovvero l'input fornito dall'utente, come grado di parallelismo.

Le query parallele producono i maggiori vantaggi se i dati vengono distribuiti uniformemente tra tutte le partizioni rispetto alla query. Se la raccolta partizionata è partizionata in modo che tutti o la maggior parte dei dati restituiti da una query siano concentrati in poche partizioni (una partizione è il caso peggiore), tali partizioni faranno un collo di bottiglia delle prestazioni della query.

***Ottimizzazione di MaxBufferedItemCount***
    
La query parallela è progettata per la prelettura dei risultati mentre il client elabora il batch di risultati corrente. Il recupero preliminare consente di migliorare la latenza complessiva di una query. Il `MaxBufferedItemCount` parametro limita il numero di risultati prerecuperati. Impostare sul `MaxBufferedItemCount` numero previsto di risultati restituiti (o un numero più elevato) per consentire alla query di ricevere il vantaggio massimo dal recupero preliminare.

Il recupero preliminare funziona allo stesso modo, indipendentemente dal grado di parallelismo, ed esiste un solo buffer per i dati di tutte le partizioni.  

**Implementare il backoff in corrispondenza di intervalli RetryAfter**

Durante i test delle prestazioni, è necessario aumentare il carico fino a quando non viene limitata una piccola frequenza di richieste. Se le richieste sono limitate, l'applicazione client deve essere disattivata in caso di limitazione per l'intervallo tra tentativi specificato dal server. Il rispetto del backoff garantisce una quantità minima di tempo di attesa tra i tentativi. 

Il supporto dei criteri di ripetizione dei tentativi è incluso in questi SDK:
- Versione 1.8.0 e successive di [.NET SDK per SQL](sql-api-sdk-dotnet.md) e [Java SDK per SQL](sql-api-sdk-java.md)
- Versione 1.9.0 e successive di [Node.js SDK per SQL](sql-api-sdk-node.md) e [Python SDK per SQL](sql-api-sdk-python.md)
- Tutte le versioni supportate degli SDK di [.NET Core](sql-api-sdk-dotnet-core.md) 

Per ulteriori informazioni, vedere [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
Nella versione 1,19 e successive di .NET SDK è disponibile un meccanismo per la registrazione di informazioni di diagnostica aggiuntive e la risoluzione dei problemi di latenza, come illustrato nell'esempio seguente. È possibile registrare la stringa di diagnostica per le richieste che hanno una latenza di lettura più elevata. La stringa di diagnostica acquisita consentirà di comprendere il numero di volte in cui sono stati ricevuti 429 errori per una determinata richiesta.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Memorizzare nella cache gli URI dei documenti per una minore latenza di lettura**

Memorizzare nella cache gli URI dei documenti quando possibile per ottenere prestazioni di lettura ottimali. Quando si crea una risorsa, è necessario definire la logica per memorizzare nella cache l'ID risorsa. Le ricerche basate sugli ID di risorsa sono più veloci delle ricerche basate sul nome, pertanto la memorizzazione nella cache di questi valori migliora le prestazioni.

**Modificare le dimensioni di pagina per le query o i feed di lettura per ottenere prestazioni migliori**

Quando si esegue una lettura in blocco di documenti usando la funzionalità dei feed di lettura (ad esempio, `ReadDocumentFeedAsync` ) o quando si emette una query SQL, i risultati vengono restituiti in modo segmentato se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

Per ridurre il numero di round trip di rete necessari per recuperare tutti i risultati applicabili, è possibile aumentare le dimensioni della pagina usando [x-ms-max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) per richiedere un numero di intestazioni pari a 1.000. Quando è necessario visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre le dimensioni della pagina a 10 per ridurre la velocità effettiva utilizzata per le letture e le query.

> [!NOTE] 
> La `maxItemCount` proprietà non deve essere utilizzata solo per la paginazione. L'uso principale è quello di migliorare le prestazioni delle query riducendo il numero massimo di elementi restituiti in una singola pagina.  

È anche possibile impostare le dimensioni della pagina usando gli SDK di Azure Cosmos DB disponibili. La proprietà [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) in `FeedOptions` consente di impostare il numero massimo di elementi da restituire nell'operazione di enumerazione. Quando `maxItemCount` è impostato su-1, l'SDK rileva automaticamente il valore ottimale, a seconda delle dimensioni del documento. Esempio:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Quando viene eseguita una query, i dati risultanti vengono inviati all'interno di un pacchetto TCP. Se si specifica un valore troppo basso per `maxItemCount` , il numero di corse necessarie per inviare i dati all'interno del pacchetto TCP è elevato, che influiscono sulle prestazioni. Se non si è certi del valore da impostare per la `maxItemCount` proprietà, è consigliabile impostarlo su-1 e consentire all'SDK di scegliere il valore predefinito.

**Aumentare il numero di thread/attività**

Vedere [aumentare il numero di thread/attività](#increase-threads) nella sezione rete di questo articolo.

## <a name="indexing-policy"></a>Criterio di indicizzazione
 
**Escludere i percorsi non usati dall'indicizzazione per scritture più veloci**

I criteri di indicizzazione Azure Cosmos DB consentono inoltre di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione usando i percorsi di indicizzazione (IndexingPolicy. IncludedPaths e IndexingPolicy. ExcludedPaths). I percorsi di indicizzazione possono migliorare le prestazioni di scrittura e ridurre l'archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo. Ciò è dovuto al fatto che i costi di indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati. Ad esempio, questo codice Mostra come escludere un'intera sezione dei documenti (un sottoalbero) dall'indicizzazione usando il carattere jolly "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Throughput

**Misurare e ottimizzare l'utilizzo di unità richiesta/secondo inferiori**

Azure Cosmos DB offre un'ampia gamma di operazioni di database. Queste operazioni includono query relazionali e gerarchiche con funzioni definite dall'utente, stored procedure e trigger, che operano tutti sui documenti all'interno di una raccolta di database. Il costo associato a ognuna di queste operazioni varia a seconda della CPU, dell'i/o e della memoria necessaria per completare l'operazione. Anziché considerare e gestire le risorse hardware, è possibile pensare a un'unità richiesta (UR) come singola misura per le risorse necessarie per eseguire diverse operazioni di database e gestire una richiesta dell'applicazione.

Viene eseguito il provisioning della velocità effettiva in base al numero di [unità richiesta](request-units.md) impostate per ogni contenitore. Il consumo delle unità richiesta viene valutato come una frequenza al secondo. Le applicazioni che superano la frequenza di unità richiesta di cui è stato effettuato il provisioning per il contenitore sono limitate fino a quando la frequenza scende sotto il livello di provisioning per il contenitore. Se l'applicazione richiede un livello superiore di velocità effettiva, è possibile aumentare la velocità effettiva effettuando il provisioning di unità richiesta aggiuntive.

La complessità di una query influiscono sul numero di unità richiesta utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine influenzano tutti il costo delle operazioni di query.

Per misurare l'overhead di qualsiasi operazione (create, Update o DELETE), esaminare l'intestazione [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (o la proprietà equivalente `RequestCharge` in `ResourceResponse\<T>` o `FeedResponse\<T>` in .NET SDK) per misurare il numero di unità richiesta utilizzate dalle operazioni:

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

L'addebito per la richiesta restituito in questa intestazione è una frazione della velocità effettiva con provisioning (ovvero 2.000 UR/secondo). Se, ad esempio, la query precedente restituisce documenti da 1.000 1 KB, il costo dell'operazione è 1.000. Quindi, entro un secondo, il server rispetta solo due richieste di questo tipo prima della frequenza di limitazione delle richieste successive. Per altre informazioni, vedere [unità richiesta](request-units.md) e [calcolatore unità richiesta](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

Quando un client tenta di superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni nel server e non si utilizza la capacità di velocità effettiva oltre il livello riservato. Il server terminerà preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429). Restituisce un'intestazione [x-ms-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di ritentare la richiesta.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

Se si dispone di più di un client che funziona cumulativamente in modo costante al di sopra della frequenza delle richieste, il numero di tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. In questo caso, il client genera un DocumentClientException con codice di stato 429 per l'applicazione. 

È possibile modificare il numero di tentativi predefinito impostando `RetryOptions` sull' `ConnectionPolicy` istanza. Per impostazione predefinita, l'eccezione DocumentClientException con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Questo errore viene restituito anche quando il numero di tentativi corrente è inferiore al numero massimo di tentativi, se il valore corrente è quello predefinito 9 o un valore definito dall'utente.

Il comportamento automatico dei tentativi consente di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni. Ma potrebbe non essere il comportamento migliore quando si esegue un benchmark delle prestazioni, soprattutto quando si sta misurando la latenza. La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per altre informazioni, vedere [unità richiesta](request-units.md).

**Per una maggiore velocità effettiva, progettazione per documenti più piccoli**

L'addebito per le richieste, ovvero il costo di elaborazione richieste, di una determinata operazione è correlato direttamente alle dimensioni del documento. Le operazioni sui documenti di grandi dimensioni costano più delle operazioni sui documenti di piccole dimensioni.

## <a name="next-steps"></a>Passaggi successivi

Per un'applicazione di esempio usata per valutare Azure Cosmos DB per scenari a prestazioni elevate in alcuni computer client, vedere [test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere l'articolo relativo a [partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
