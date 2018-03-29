---
title: 'Azure Cosmos DB: risorse, SDK e API SQL.NET Core | Microsoft Docs'
description: Informazioni complete sull'SDK e sull'API SQL.NET Core, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB .NET Core SDK.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b367112c68574cc81d00e3d4b40b405d2d35131
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>.NET Core SDK di Azure Cosmos DB per API SQL: risorse e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Download dell'SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Esempi**</td><td>[Esempi di codice .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Introduzione**</td><td>[Introduzione ad Azure Cosmos DB .NET Core SDK](sql-api-dotnetcore-get-started.md)</td></tr>

<tr><td>**Esercitazione sull'app Web**</td><td>[Sviluppo di applicazioni Web con Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Framework attualmente supportato**</td><td>[.NET standard 1.6 e .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

Azure Cosmos DB .NET Core SDK ha le stesse funzionalità della versione più recente di [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md).

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* È stato risolto il problema della visualizzazione di un'eccezione KeyNotFoundException per le query ORDER BY su più partizioni in casi limite.
* Correzione del bug per cui l'attributo JsonPropery nella clausola SELECT per le query LINQ non viene rispettato.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Correzione del bug che si verifica in determinate race condition, che comporta errori intermittenti "Microsoft.Azure.Documents.NotFoundException: The read session is not available for the input session token" (La sessione di lettura non è disponibile per il token della sessione di input) quando si usa il livello di coerenza di sessione.

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

* Correzione di un problema che occasionalmente causa un'eccezione WebException, ovvero l'impossibilità di risolvere il nome remoto.
* Aggiunta del supporto per la lettura diretta di un documento con tipo includendo nuovi overload all'API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Aggiunta del supporto LINQ per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Correzione per un problema di perdita di memoria per l'oggetto ConnectionPolicy provocata dall'uso del gestore eventi.
* Correzione per un problema relativo al mancato funzionamento di UpsertAttachmentAsync in caso di uso di ETag.
* Correzione di un problema relativo al mancato funzionamento della continuazione della query di tipo order-by tra partizioni in caso di ordinamento in un campo di tipo stringa.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG). Vedere [Supporto dell'aggregazione](sql-api-sql-query.md#Aggregates).
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK consente di compilare app [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) veloci e multipiattaforma da eseguire in Windows, Mac e Linux. L'ultima versione di Azure Cosmos DB .NET Core SDK è completamente compatibile con [Xamarin](https://www.xamarin.com) e può essere usata per compilare applicazioni destinate a iOS, Android e Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Azure Cosmos DB .NET Core Preview SDK consente di compilare app [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) veloci e multipiattaforma da eseguire in Windows, Mac e Linux.

Azure Cosmos DB .NET Core Preview SDK ha le stesse funzionalità della versione più recente di [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) e supporta quanto segue.
* Tutte le [modalità di connessione](performance-tips.md#networking): modalità gateway, TCP diretto e HTTP diretti. 
* Tutti i [livelli di coerenza](consistency-levels.md): assoluta, di sessione, con decadimento ristretto e finale.
* [Raccolte partizionate](partition-data.md). 
* [Account di database tra più aree e replica geografica](distribute-data-globally.md).

Per domande su questo SDK, pubblicare un post su [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) o inserire la domanda nel [repository github](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
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

