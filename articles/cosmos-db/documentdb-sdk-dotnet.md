---
title: Risorse e SDK .NET per Azure Cosmos DB | Documentazione Microsoft
description: Tutte le informazioni sull'SDK e sull'API .NET, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di Azure Cosmos DB .NET SDK.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 8d6b953ae95713dc8b5a4037fa59bec49ea52b85
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="azure-cosmos-db-net-sdk-download-and-release-notes"></a>Azure Cosmos DB .NET SDK: download e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Feed delle modifiche .NET](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Download dell'SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Esempi**</td><td>[Esempi di codice .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Attività iniziali**</td><td>[Introduzione ad Azure Cosmos DB .NET SDK](documentdb-get-started.md)</td></tr>

<tr><td>**Esercitazione sull'app Web**</td><td>[Sviluppo di applicazioni Web con Azure Cosmos DB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Framework attualmente supportato**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Aggiunta del supporto per la definizione di JsonSerializerSettings personalizzate durante la creazione di un'istanza di [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Risolto un problema che interessava i computer x64 che non supportano l'istruzione SSE4 e generano una SEHException durante l'esecuzione di query nell'API di DocumentDB di Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Aggiunta del supporto per la funzionalità di unità richiesta al minuto (UR/m).
*   Aggiunta del supporto per un nuovo livello di coerenza denominato ConsistentPrefix.
*   Aggiunta del supporto per le metriche delle query per le singole partizioni.
*   Aggiunta del supporto per la limitazione delle dimensioni del token di continuazione per le query.
*   Aggiunta del supporto per una traccia più dettagliata delle richieste non riuscite.
*   Alcuni miglioramenti delle prestazioni nell'SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Stesse funzionalità della versione 1.13.3. Alcune modifiche interne.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Stesse funzionalità della versione 1.13.2. Alcune modifiche interne.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Risolto un problema per cui il valore PartitionKey in FeedOptions veniva ignorato per le query di aggregazione.
* Risolto un problema nella gestione trasparente delle partizioni durante l'esecuzione dell'ordinamento per query della partizione trasversale intermedia.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Correzione di un problema che ha causato deadlock in alcune API asincrone, se usate in un contesto ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Correzioni per rendere l'SDK più resiliente per il failover automatico in determinate condizioni.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Correzione di un problema che occasionalmente causa un'eccezione WebException, ovvero l'impossibilità di risolvere il nome remoto.
* Aggiunta del supporto per la lettura diretta di un documento con tipo includendo nuovi overload all'API ReadDocumentAsync.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Aggiunta del supporto LINQ per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Correzione per un problema di perdita di memoria per l'oggetto ConnectionPolicy provocata dall'uso del gestore eventi.
* Correzione per un problema relativo al mancato funzionamento di UpsertAttachmentAsync in caso di uso di ETag.
* Correzione di un problema relativo al mancato funzionamento della continuazione della query di tipo order-by tra partizioni in caso di ordinamento in un campo di tipo stringa.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG). Vedere [Supporto dell'aggregazione](documentdb-sql-query.md#Aggregates).
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Correzione di un problema che causava l'esito negativo di alcune query tra partizioni nel processo host a 32 bit.
* Correzione di un problema che impediva l'aggiornamento del contenitore della sessione con il token per le richieste con esito negativo in modalità gateway.
* Correzione di un problema che causava, in alcuni casi, l'esito negativo di una query con chiamate definite dall'utente nella proiezione.
* Correzioni alle prestazioni lato client per aumentare la velocità effettiva di lettura e scrittura delle richieste.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Correzione di un problema che impediva l'aggiornamento del contenitore della sessione con il token per le richieste con esito negativo.
* Aggiunta del supporto per consentire il funzionamento dell'SDK in un processo host a 32 bit. Si noti che se si usano query tra partizioni, è consigliabile usare l'elaborazione dell'host a 64 bit per migliorare le prestazioni.
* Prestazioni migliorate per gli scenari che riguardano query con un numero elevato di valori della chiave di partizione in un'espressione IN.
* Varie statistiche popolate sulla quota di risorse in ResourceResponse per le richieste di lettura della raccolta documenti quando è impostata l'opzione di richiesta PopulateQuotaInfo.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Risoluzione secondaria per i problemi di prestazioni relativi all'API CreateDocumentCollectionIfNotExistsAsync introdotta nella versione 1.11.0.
* Risoluzione dei problemi delle prestazioni nell'SDK per scenari che comportano un livello molto elevato di richieste simultanee.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Supporto per nuove classi e metodi per elaborare il [feed di modifiche](change-feed.md) dei documenti in una raccolta.
* Supporto per la continuazione della query tra partizioni e miglioramenti delle prestazioni per le query tra partizioni.
* Aggiunta dei metodi CreateDatabaseIfNotExistsAsync e CreateDocumentCollectionIfNotExistsAsync.
* Supporto di LINQ per le funzioni di sistema: IsDefined, IsNull e IsPrimitive.
* Correzione per l'inserimento automatico nel Cestino degli assembly Microsoft.Azure.Documents.ServiceInterop.dll e DocumentDB.Spatial.Sql.dll nella cartella Cestino dell'applicazione quando si usa il pacchetto Nuget con progetti che includono strumenti project.json.
* Supporto per l'emissione di tracce ETW lato client che possono essere utili in scenari di debug.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Aggiunto il supporto per la connettività diretta delle raccolte partizionate.
* Miglioramento delle prestazioni per il livello di coerenza con obsolescenza associata.
* Aggiunta Polygon e LineString DataTypes quando si specifica il criterio di indicizzazione per la raccolta criteri per le query spaziali di geo-fencing.
* Aggiunto il supporto LINQ per StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter durante la traduzione dei predicati.
* Varie correzioni di bug dell'SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Risolto un problema che causava l'eccezione NotFoundException seguente: la sessione di lettura non è disponibile per il token della sessione di input. In alcuni casi questa eccezione si è verificata durante l'esecuzione di query per l'area di lettura di un account geograficamente distribuito.
* L'esposizione della proprietà ResponseStream nella classe ResourceResponse consente l'accesso diretto al flusso sottostante proveniente da una risposta.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Modificate le classi ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse per l'implementazione dell'interfaccia pubblica corrispondente in modo che possano essere simulate per la distribuzione basata su test (TDD).
* Risolto un problema che causava un'intestazione di chiave di partizione non valida con l'uso di un oggetto JsonSerializerSettings personalizzato per la serializzazione dei dati.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Risoluzione di un problema che provocava l'esito negativo con errore delle query con esecuzione prolungata. Al momento il token di autorizzazione non è valido.
* Risoluzione di un problema che rimuoveva l'oggetto SqlParameterCollection originale dalle query top/order-by tra partizioni.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Aggiunta del supporto per le query parallele nelle raccolte partizionate.
* Aggiunta del supporto ORDER BY e TOP tra partizioni per le raccolte partizionate.
* Correzione di riferimenti mancanti a DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll, necessari quando si fa riferimento a un progetto di Azure Cosmos DB con riferimento al pacchetto Nuget Azure Cosmos DB.
* Correzione della possibilità di usare parametri di tipi diversi per le funzioni definite dall'utente in LINQ. 
* Risoluzione di un bug per gli account replicati a livello globale in cui le chiamate Upsert sono state indirizzate verso posizioni di lettura invece di posizioni di scrittura.
* Aggiunta di metodi mancanti all'interfaccia IDocumentClient: 
  * il metodo UpsertAttachmentAsync che accetta mediaStream e opzioni come parametri
  * il metodo CreateAttachmentAsync che accetta opzioni come parametro
  * il metodo CreateOfferQuery che accetta querySpec come parametro.
* Rivelazione di classi pubbliche esposte nell'interfaccia IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Aggiunta del supporto per gli account di database con più aree.
* Aggiunta del supporto per la ripetizione dei tentativi delle richieste limitate.  L'utente può personalizzare il numero di tentativi e il tempo di attesa massimo configurando la proprietà ConnectionPolicy.RetryOptions.
* Aggiunta di una nuova interfaccia IDocumentClient che definisce le firme di tutti i metodi e di tutte le proprietà DocumenClient.  Come parte di questa modifica, anche i metodi di estensione che creano IQueryable e IOrderedQueryable sono stati modificati in metodi sulla stessa classe DocumentClient.
* Aggiunta dell'opzione di configurazione per impostare ServicePoint.ConnectionLimit per un URI dell'endpoint di Azure Cosmos DB specifico.  Usare ConnectionPolicy.MaxConnectionLimit per modificare il valore predefinito, impostato su 50.
* IPartitionResolver e la relativa implementazione sono stati deprecati.  Il supporto per IPartitionResolver è ora obsoleto. È consigliabile usare le raccolte partizionate per un'archiviazione e una velocità effettiva superiori.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Aggiunta di un overload al metodo ExecuteStoredProcedureAsync basato su URI che accetta RequestOptions come parametro.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Aggiunta del supporto per la durata (TTL) relativa ai documenti.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Correzione di un bug nel pacchetto Nuget di .NET SDK per creare il pacchetto come parte di una soluzione del servizio cloud di Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementazione delle [raccolte partizionate](partition-data.md) e dei [livelli di prestazioni definiti dall'utente](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Correzione]** Se si eseguono query nell'endpoint Azure Cosmos DB viene generato il messaggio seguente: "System.Net.Http.HttpRequestException: Errore durante la copia del contenuto in un flusso".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Supporto LINQ espanso, tra cui nuovi operatori per il paging, espressioni condizionali e confronto di intervalli.
  * Operatore Take per abilitare il comportamento SELECT TOP in LINQ
  * Operatore CompareTo per abilitare i confronti di intervallo di stringa
  * Operatori Conditional (?) e Coalesce (??)
* **[Corretto]** ArgumentOutOfRangeException durante l'unione della proiezione del modello con Where-In in una query LINQ. [N. 81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Corretto]** Se Select non è l'ultima espressione il provider LINQ presuppone che non ci sia alcuna proiezione e produce SELECT * in modo non corretto.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Implementazione di Upsert, aggiunti metodi UpsertXXXAsync
* Miglioramenti delle prestazioni per tutte le richieste
* Supporto del provider LINQ per metodi condizionali, coalesce e CompareTo per le stringhe
* **[Corretto]** Provider LINQ --> Implementa il metodo Contains nell'elenco per generare lo stesso codice SQL generato in IEnumerable e Matrice
* **[Corretto]** BackoffRetryUtility usa nuovamente lo stesso HttpRequestMessage anziché crearne uno nuovo in un tentativo successivo
* **[Obsoleto]** UriFactory.CreateCollection --> Ora deve usare UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Corretto]** Problemi di localizzazione quando si usano impostazioni cultura diverse dalla lingua inglese, ad esempio nl-NL e così via. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Aggiunto il routing basato su IP
  * Nuovo helper UriFactory per agevolare la costruzione di collegamenti alle risorse basati su ID
  * Nuovi overload su DocumentClient da eseguire nell'URI
* Aggiunti IsValid() and IsValidDetailed() in LINQ per i dati geospaziali
* Migliorato il supporto del provider LINQ:
  * **Matematica** : Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
  * **Stringa** : Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **Matrice** : Concat, Contains, Count
  * **IN** 

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Aggiunto il supporto per la modifica dei criteri di indicizzazione.
  * Nuovo metodo ReplaceDocumentCollectionAsync in DocumentClient
  * Nuova proprietà IndexTransformationProgress in ResourceResponse<T> per tenere traccia dello stato percentuale delle modifiche ai criteri di indice
  * DocumentCollection.IndexingPolicy è ora modificabile
* Aggiunto il supporto per query e indicizzazione spaziali.
  * Nuovo spazio dei nomi Microsoft.Azure.Documents.Spatial per la serializzazione/deserializzazione di tipi di dati spaziali come point e polygon
  * Nuova classe SpatialIndex per l'indicizzazione dei dati GeoJSON archiviati in Cosmos DB
* **[Corretto]** Query SQL non corretta generata da un'espressione LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Aggiunta una dipendenza su Newtonsoft.Json 5.0.7.
* Apportate modifiche per il supporto di Order By:
  
  * Supporto del provider LINQ per OrderBy() o OrderByDescending()
  * IndexingPolicy per il supporto di Order By 
    
    **Possibile modifica importante** 
    
    Se è presente un codice che effettua il provisioning delle raccolte con criteri di indicizzazione personalizzati, è necessario che il codice venga aggiornato in modo da supportare la nuova classe IndexingPolicy. Se non sono presenti criteri di indicizzazione, tale modifica non avrà alcun impatto.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Aggiunto il supporto per il partizionamento dei dati con IPartitionResolver e le nuove classi HashPartitionResolver e RangePartitionResolver.
* Aggiunta la serializzazione di DataContract.
* Aggiunto il supporto per GUID nel provider LINQ.
* Aggiunto il supporto per UDF in LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK con disponibilità generale

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata ad Azure Cosmos DB con un SDK ritirato viene rifiutata dal servizio.

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.15.0](#1.15.0) |30 giugno 2017 |--- |
| [1.14.1](#1.14.1) |23 maggio 2017 |--- |
| [1.14.0](#1.14.0) |10 maggio 2017 |--- |
| [1.13.4](#1.13.4) |09 maggio 2017 |--- |
| [1.13.3](#1.13.3) |06 maggio 2017 |--- |
| [1.13.2](#1.13.2) |19 aprile 2017 |--- |
| [1.13.1](#1.13.1) |29 marzo 2017 |--- |
| [1.13.0](#1.13.0) |24 marzo 2017 |--- |
| [1.12.2](#1.12.2) |20 marzo 2017 |--- |
| [1.12.1](#1.12.1) |14 marzo 2017 |--- |
| [1.12.0](#1.12.0) |15 febbraio 2017 |--- |
| [1.11.4](#1.11.4) |06 febbraio 2017 |--- |
| [1.11.3](#1.11.3) |26 gennaio 2017 |--- |
| [1.11.1](#1.11.1) |21 dicembre 2016 |--- |
| [1.11.0](#1.11.0) |08 dicembre 2016 |--- |
| [1.10.0](#1.10.0) |27 settembre 2016 |--- |
| [1.9.5](#1.9.5) |1° settembre 2016 |--- |
| [1.9.4](#1.9.4) |24 agosto 2016 |--- |
| [1.9.3](#1.9.3) |15 agosto 2016 |--- |
| [1.9.2](#1.9.2) |23 luglio 2016 |--- |
| [1.8.0](#1.8.0) |14 giugno 2016 |--- |
| [1.7.1](#1.7.1) |06 maggio 2016 |--- |
| [1.7.0](#1.7.0) |26 aprile 2016 |--- |
| [1.6.3](#1.6.3) |08 aprile 2016 |--- |
| [1.6.2](#1.6.2) |29 marzo 2016 |--- |
| [1.5.3](#1.5.3) |19 febbraio 2016 |--- |
| [1.5.2](#1.5.2) |14 dicembre 2015 |--- |
| [1.5.1](#1.5.1) |23 novembre 2015 |--- |
| [1.5.0](#1.5.0) |05 ottobre 2015 |--- |
| [1.4.1](#1.4.1) |25 agosto 2015 |--- |
| [1.4.0](#1.4.0) |13 agosto 2015 |--- |
| [1.3.0](#1.3.0) |05 agosto 2015 |--- |
| [1.2.0](#1.2.0) |06 luglio 2015 |--- |
| [1.1.0](#1.1.0) |30 aprile 2015 |--- |
| [1.0.0](#1.0.0) |08 aprile 2015 |--- |


## <a name="faq"></a>domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 


