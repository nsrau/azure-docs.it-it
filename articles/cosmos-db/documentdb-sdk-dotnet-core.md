---
title: API .NET Core, risorse e SDK per Azure DocumentDB | Microsoft Docs
description: Tutte le informazioni sull'SDK e sull'API .NET Core, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di DocumentDB .NET Core SDK.
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
ms.date: 06/12/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: c1f0bbfd1bea292eedaaf8904a2f60e9415dcbcf
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="documentdb-net-core-sdk-release-notes-and-resources"></a>DocumentDB .NET Core SDK: note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
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

<tr><td>**Download dell'SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Esempi**</td><td>[Esempi di codice .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Attività iniziali**</td><td>[Introduzione a DocumentDB .NET Core SDK](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Esercitazione sull'app Web**</td><td>[Sviluppo di applicazioni Web con DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Framework attualmente supportato**</td><td>[.NET standard 1.6 e .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

DocumentDB .NET Core SDK ha le stesse funzionalità delle versione più recente di [DocumentDB .NET SDK](documentdb-sdk-dotnet.md).

> [!NOTE] 
> DocumentDB .NET Core SDK non è ancora compatibile con le app della piattaforma UWP (Universal Windows Platform). In caso di interesse a .NET Core SDK che supporta le app della piattaforma UWP, inviare un messaggio di posta elettronica a [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Supporto di .NET Standard 1.5 come uno dei framework di destinazione.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Risolto un problema che riguardava i computer x64 computer che non supportano l'istruzione SSE4 e generano SEHException durante l'esecuzione di query di DocumentDB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Aggiunta del supporto per la funzionalità di unità richiesta al minuto (UR/min).
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

* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG). Vedere [Supporto dell'aggregazione](documentdb-sql-query.md#Aggregates).
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

DocumentDB .NET Core SDK consente di compilare app [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) veloci e multipiattaforma da eseguire in Windows, Mac e Linux. La versione più recente di DocumentDB .NET Core SDK è completamente compatibile con [Xamarin](https://www.xamarin.com) e può essere usata per compilare applicazioni destinate a iOS, Android e Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

DocumentDB .NET Core Preview SDK consente di compilare app [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) veloci e multipiattaforma da eseguire in Windows, Mac e Linux.

DocumentDB .NET Core Preview SDK ha le stesse funzionalità della versione più recente di [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) e supporta quanto segue:
* Tutte le [modalità di connessione](performance-tips.md#networking): modalità gateway, TCP diretto e HTTP diretti. 
* Tutti i [livelli di coerenza](consistency-levels.md): assoluta, di sessione, con decadimento ristretto e finale.
* [Raccolte partizionate](partition-data.md). 
* [Account di database tra più aree e replica geografica](distribute-data-globally.md).

Per domande su questo SDK, pubblicare un post su [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) o inserire la domanda nel [repository github](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
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


