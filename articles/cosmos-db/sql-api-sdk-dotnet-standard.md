---
title: 'Azure Cosmos DB: API di SQL .NET Standard, risorse di & SDK'
description: Tutte le informazioni sull'API SQL e .NET SDK, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di Azure Cosmos DB .NET SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663800"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET Standard SDK per l'API SQL: download e note sulla versione
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
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
|**Download dell'SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Documentazione sull'API**|[Documentazione di riferimento API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Esempi**|[Esempi di codice .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Introduzione**|[Introduzione ad Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Esercitazione sull'app Web**|[Sviluppo di applicazioni Web con Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Framework attualmente supportato**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Note sulla versione
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>Aggiunto
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541) Aggiunta del livello di coerenza alle opzioni client e query
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544) Aggiunta del supporto del token di continuazione per LINQ
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557) Aggiunta di opzioni trigger alle opzioni di richiesta elemento
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571) Aggiunta di un serializzatore JSON.net predefinito con impostazioni facoltative
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572) Aggiunta della convalida della chiave di partizione in CreateContainerIfNotExistsAsync
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581) Aggiunta dell'API LINQ to QueryDefinition
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592) Aggiunta di CreateIfNotExistsAsync al generatore di contenitori
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597) Aggiunta della proprietà del token di continuazione a ResponseMessage
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604) Aggiunto metodo di estensione LINQ ToStreamIterator

#### <a name="fixed"></a>Fissa
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548) Correzione del messaggio con tipizzazione errata in Cosmosexception. ToString ();
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558) Correzione della contesa di blocco LocationCache ConcurrentDict
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561) GetItemLinqQueryable funziona ora con una query null
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567) La query gestisce correttamente diverse impostazioni cultura della lingua
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574) Correzione del messaggio di errore vuoto se l'analisi della query non riesce da un'eccezione imprevista
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576) Query serializza correttamente l'input in un flusso

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Disponibilità generale della [versione 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) di .NET SDK
* Targets .NET Standard 2,0, che supporta .NET Framework 4.6.1 + e .NET Core 2.0 +
* Nuovo modello a oggetti con CosmosClient e metodi di primo livello suddivisi in classi di contenitori e database rilevanti
* Nuove API di flusso a prestazioni elevate
* Supporto incorporato per le API del processore dei feed delle modifiche
* API di generatore Fluent per CosmosClient, contenitore e processore di feed di modifiche
* API di gestione della velocità effettiva idiomatiche
* RequestOptions granulari e ResponseTypes per richieste di database, contenitori, elementi, query e velocità effettiva
* Possibilità di ridimensionare i contenitori non partizionati 
* Serializzatore estendibile e personalizzabile
* Pipeline di richieste estendibile con supporto per gestori personalizzati


## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata ad Azure Cosmos DB con un SDK ritirato viene rifiutata dal servizio.

<br/>

| Version | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [3.1.0](#3.1.0) |29 luglio 2019 |--- |
| [3.0.0](#3.0.0) |15 luglio 2019 |--- |


## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

