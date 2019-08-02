---
title: 'Azure Cosmos DB: API SQL .NET Core, SDK e risorse'
description: Informazioni complete sull'SDK e sull'API SQL.NET Core, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB .NET Core SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: ce22efd6eccdc067b05697b8389c821c4c01a347
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638602"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>.NET Core SDK di Azure Cosmos DB per l'API SQL: note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor in blocco-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Download dell'SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Documentazione sull'API**|[Documentazione di riferimento API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Esempi**|[Esempi di codice .NET](sql-api-dotnet-samples.md)|
|**Introduzione**|[Introduzione a Azure Cosmos DB .NET](sql-api-sdk-dotnet.md)|
|**Esercitazione sull'app Web**|[Sviluppo di applicazioni Web con Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Framework attualmente supportato**|[.NET standard 1.6 e .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Note sulla versione

> [!NOTE]
> Se si usa .NET Core, vedere la versione 3. x più recente di [.NET SDK](sql-api-sdk-dotnet-standard.md), che ha come destinazione .NET standard. 

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Corregge la traccia race condition per le query che hanno causato pagine vuote

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* La versione di System .NET. http dell'SDK corrisponde a quanto definito nel pacchetto NuGet
* Aumento della dimensione di precisione decimale per le query LINQ.
* Sono state aggiunte nuove classi CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType e PartitionKeyDefinitionVersion
* Aggiunta di TimeToLivePropertyPath a DocumentCollection
* Aggiunta di CompositeIndexes e SpatialIndexes a IndexPolicy
* Aggiunta della versione a PartitionKeyDefinition
* Aggiunto None a PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Aggiunta di IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection e MaxTcpConnectionsPerEndpoint a ConnectionPolicy.
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Miglioramenti della diagnostica

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Aggiunta dell'impostazione di variabile di ambiente "POCOSerializationOnly".

* Rimozione di DocumentDB.Spatial.Sql.dll ora incluso in Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Miglioramento nella logica di ripetizione dei tentativi durante il failover per le chiamate di esecuzione di StoredProcedure.

* Creazione del singleton DocumentClientEventSource. 

* Correzione del timeout di GatewayAddressCache timeout che non rispetta RequestTimeout di ConnectionPolicy.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Per la diagnostica di trasporto diretto/TCP, è stata aggiunta TransportException, un tipo di eccezione interna dell'SDK. Quando è presente nei messaggi di eccezione, questo tipo consente di stampare informazioni aggiuntive per la risoluzione dei problemi di connettività client.

* È stato aggiunto un nuovo overload di costruttore che usa HttpMessageHandler, uno stack del gestore HTTP da usare per inviare le richieste HttpClient (ad esempio HttpClientHandler).

* Correzione di bug in cui l'intestazione con i valori Null non viene gestita correttamente.

* È stata migliorata la convalida della cache di raccolta.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* È stato effettuato l'aggiornamento di System.Net.Security alla versione 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Sono stati apportati miglioramenti all'analisi diagnostica.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Aumentata la resilienza agli errori temporanei delle richieste su più aree.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Aggiunto il supporto per la scrittura in più aree.
* Miglioramenti alle prestazioni delle query tra più partizioni con TOP e MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Aggiunto il supporto dell'annullamento richiesta.
* Aggiunta SetCurrentLocation a ConnectionPolicy, che popola automaticamente le posizioni preferite in base alla regione.
* Correzione del bug nelle query su più partizioni con Min/Max e un filtro che mette in corrispondenza nessun documento in una singola partizione.
* I metodi DocumentClient ora dispongono della parità con IDocumentClient.
* Aggiornato lo stack di trasporto TCP diretto per ridurre il numero di connessioni stabilite.
* Aggiunta del supporto per TCP in modalità diretta per i client non Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Aggiunto il supporto dell'annullamento richiesta.
* Aggiunta SetCurrentLocation a ConnectionPolicy, che popola automaticamente le posizioni preferite in base alla regione.
* Correzione del bug nelle query su più partizioni con Min/Max e un filtro che mette in corrispondenza nessun documento in una singola partizione.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* I metodi DocumentClient ora dispongono della parità con IDocumentClient.
* Aggiornato lo stack di trasporto TCP diretto per ridurre il numero di connessioni stabilite.
* Aggiunta del supporto per TCP in modalità diretta per i client non Windows.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Aggiunta della proprietà ConsistencyLevel a FeedOptions.
* Aggiunta di JsonSerializerSettings a RequestOptions e FeedOptions.
* Aggiunta di EnableReadRequestsFallback a ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* È stato risolto il problema della visualizzazione di un'eccezione KeyNotFoundException per le query ORDER BY su più partizioni in casi limite.
* Correzione del bug per cui l'attributo JsonProperty nella clausola SELECT per le query LINQ non viene rispettato.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Correzione del bug che si verifica in determinate race condition, che comporta errori intermittenti "Microsoft.Azure.Documents.NotFoundException: The read session is not available for the input session token" (Microsoft.Azure.Documents.NotFoundException: sessione di lettura non disponibile per il token della sessione di input) quando si usa il livello di coerenza di sessione.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Correzione della regressione in cui FeedOptions.MaxItemCount = -1 ha generato un errore System.ArithmeticException: dimensioni pagina negative.
* Aggiunta di una nuova funzione ToString() a QueryMetrics.
* Esposizione delle statistiche della partizione nelle raccolte di lettura.
* Aggiunta della proprietà PartitionKey a ChangeFeedOptions.
* Correzione di bug di minore entità.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Aggiunge la possibilità di specificare gli indici univoci per i documenti usando proprietà UniqueKeyPolicy in DocumentCollection.
 * È stato corretto un bug in cui le impostazioni JsonSerializer personalizzate non vengono rispettate per l'esecuzione di alcune query e stored procedure.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Modifica della personalizzazione da Azure DocumentDB ad Azure Cosmos DB nella documentazione di riferimento delle API, nelle informazioni sui metadati negli assembly e nel pacchetto NuGet.
 * Esposizione delle informazioni di diagnostica e della latenza della risposta alle richieste inviate con la modalità di connettività diretta. I nomi delle proprietà sono RequestDiagnosticsString e RequestLatency per la classe ResourceResponse.
 * Questa versione dell'SDK richiede la versione più recente dell'emulatore di Azure Cosmos DB che è possibile scaricare dalla pagina https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Aggiunta di diverse correzioni e miglioramenti dell'affidabilità.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Le modifiche interne correlate al supporto di [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Aggiunta del supporto per PartitionKeyRangeId come FeedOption per limitare l'ambito dei risultati di query a un intervallo di chiavi di partizione specifico.
* Aggiunta del supporto per StartTime come ChangeFeedOption per avviare la ricerca delle modifiche a partire dall'ora di inizio.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   È stato risolto un problema nella classe JsonSerializable che può generare un'eccezione di overflow dello stack.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Aggiunta del supporto per la definizione di JsonSerializerSettings personalizzate durante la creazione di un'istanza di [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Supporto di .NET Standard 1.5 come uno dei framework di destinazione.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Risoluzione di un problema riguardante i computer x64 che non supportano l'istruzione SSE4 e generano SEHException durante l'esecuzione di query di Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Aggiunta del supporto per un nuovo livello di coerenza denominato ConsistentPrefix.
*   Aggiunta del supporto per le metriche delle query per le singole partizioni.
*   Aggiunta del supporto per la limitazione delle dimensioni del token di continuazione per le query.
*   Aggiunta del supporto per una traccia più dettagliata delle richieste non riuscite.
*   Alcuni miglioramenti delle prestazioni nell'SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Risolto un problema per cui il valore PartitionKey in FeedOptions veniva ignorato per le query di aggregazione.
* Risolto un problema nella gestione trasparente delle partizioni durante l'esecuzione dell'ordinamento per query della partizione trasversale intermedia.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Correzione di un problema che ha causato deadlock in alcune API asincrone, se usate in un contesto ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Correzioni per rendere l'SDK più resiliente per il failover automatico in determinate condizioni.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Correzione di un problema che a volte causa un'eccezione WebException che indica che è impossibile risolvere il nome remoto.
* Aggiunta del supporto per la lettura diretta di un documento con tipo includendo nuovi overload all'API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Aggiunta del supporto LINQ per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Correzione per un problema di perdita di memoria per l'oggetto ConnectionPolicy provocata dall'uso del gestore eventi.
* Correzione per un problema relativo al mancato funzionamento di UpsertAttachmentAsync in caso di uso di ETag.
* Correzione di un problema relativo al mancato funzionamento della continuazione della query di tipo order-by tra partizioni in caso di ordinamento in un campo di tipo stringa.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG). Vedere [Supporto dell'aggregazione](sql-query-aggregates.md).
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK consente di compilare app [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) veloci e multipiattaforma da eseguire in Windows, Mac e Linux. L'ultima versione di Azure Cosmos DB .NET Core SDK è completamente compatibile con [Xamarin](https://www.xamarin.com) e può essere usata per compilare applicazioni destinate a iOS, Android e Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Azure Cosmos DB .NET Core Preview SDK consente di compilare app [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) veloci e multipiattaforma da eseguire in Windows, Mac e Linux.

Azure Cosmos DB .NET Core Preview SDK ha le stesse funzionalità della versione più recente di [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) e supporta quanto segue.
* Tutte le [modalità di connessione](performance-tips.md#networking): modalità gateway, TCP diretto e HTTP diretti.
* Tutti i [livelli di coerenza](consistency-levels.md): assoluta, sessione, con decadimento ristretto, finale.
* [Raccolte partizionate](partition-data.md).
* [Account di database tra più aree e replica geografica](distribute-data-globally.md).

Per domande su questo SDK, pubblicare un post su [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb) o segnalare un problema nel [repository GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [2.4.1](#2.4.1) |20 giugno 2019 |--- |
| [2.4.0](#2.4.0) |05 maggio, 2019 |--- |
| [2.3.0](#2.3.0) |04 aprile 2019 |--- |
| [2.2.3](#2.2.3) |11 marzo 2019 |--- |
| [2.2.2](#2.2.2) |06 febbraio, 2019 |--- |
| [2.2.1](#2.2.1) |24 dicembre 2018 |--- |
| [2.2.0](#2.2.0) |7 dicembre 2018 |--- |
| [2.1.3](#2.1.3) |15 ottobre 2018 |--- |
| [2.1.2](#2.1.2) |4 ottobre 2018 |--- |
| [2.1.1](#2.1.1) |27 settembre 2018 |--- |
| [2.1.0](#2.1.0) |21 settembre 2018 |--- |
| [2.0.0](#2.0.0) |7 settembre 2018 |--- |
| [1.9.1](#1.9.1) |09 marzo 2018 |--- |
| [1.8.2](#1.8.2) |21 febbraio 2018 |--- |
| [1.8.1](#1.8.1) |05 febbraio 2018 |--- |
| [1.7.1](#1.7.1) |16 novembre 2017 |--- |
| [1.7.0](#1.7.0) |10 novembre 2017 |--- |
| [1.6.0](#1.6.0) |17 ottobre 2017 |--- |
| [1.5.1](#1.5.1) |02 ottobre 2017 |--- |
| [1.5.0](#1.5.0) |10 agosto 2017 |--- | 
| [1.4.1](#1.4.1) |07 agosto 2017 |--- |
| [1.4.0](#1.4.0) |02 agosto 2017 |--- |
| [1.3.2](#1.3.2) |12 giugno 2017 |--- |
| [1.3.1](#1.3.1) |23 maggio 2017 |--- |
| [1.3.0](#1.3.0) |10 maggio 2017 |--- |
| [1.2.2](#1.2.2) |19 aprile 2017 |--- |
| [1.2.1](#1.2.1) |29 marzo 2017 |--- |
| [1.2.0](#1.2.0) |25 marzo 2017 |--- |
| [1.1.2](#1.1.2) |20 marzo 2017 |--- |
| [1.1.1](#1.1.1) |14 marzo 2017 |--- |
| [1.1.0](#1.1.0) |16 febbraio 2017 |--- |
| [1.0.0](#1.0.0) |21 dicembre 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 novembre 2016 |31 dicembre 2016 |

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

