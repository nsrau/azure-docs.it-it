---
title: 'Azure Cosmos DB Java SDK v4 per API SQL: note sulla versione e risorse'
description: Informazioni complete su Azure Cosmos DB Java SDK v4 per SDK e API SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: b222a94ee754b24192261451d8ddc429886e705c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725653"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 per API Core (SQL): note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Esecuzione bulk - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 per API Core (SQL) combina un'API asincrona e un'API sincrona in un unico artefatto Maven. V4 SDK offre prestazioni migliorate, nuove funzionalità API e supporto asincrono basato su Project Reactor e sulla [libreria Netty](https://netty.io/). Si prevede quindi che Azure Cosmos DB Java SDK v4 offra prestazioni più elevate rispetto ad [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) e [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Queste note sulla versione sono destinate solo ad Azure Cosmos DB Java SDK v4. Se si usa una versione precedente, vedere l'articolo [Eseguire la migrazione a Java SDK v4 per Azure Cosmos DB](migrate-java-v4-sdk.md) per informazioni sull'aggiornamento alla versione 4.
>
> Per iniziare rapidamente, eseguire questi tre passaggi.
> 1. Installare la [prima versione supportata di Java Runtime, JDK 8](/java/azure/jdk/?view=azure-java-stable) per poter usare l'SDK.
> 2. Consultare la [Guida di avvio rapido per Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java), che consente di accedere all'artefatto Maven e prende in esame le richieste di base di Azure Cosmos DB.
> 3. Leggere i [suggerimenti sulle prestazioni](performance-tips-java-sdk-v4-sql.md) e le guide alla [risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4 per ottimizzare l'SDK per l'applicazione.
>
> I [workshop e i laboratori su Azure Cosmos DB](https://aka.ms/cosmosworkshop) costituiscono un'altra risorsa molto utile per imparare a usare Azure Cosmos DB Java SDK v4.
>

| |  |
|---|---|
| **Download dell'SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentazione sull'API** | [Documentazione di riferimento API Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1-beta.3/index.html) |
|**Contribuire all'SDK** | [Repository centrale di Azure SDK per Java in GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Operazioni preliminari** | [Avvio rapido: Compilare un'app Java per gestire i dati dell'API SQL di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Repository GitHub con codice di avvio rapido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Esempi di codice di base** | [Azure Cosmos DB: Esempi di Java per l'API SQL](sql-api-java-sdk-samples.md) · [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**App console con feed di modifiche**| [Feed di modifiche - Esempio di Java SDK v4](create-sql-api-java-changefeed.md) · [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Esempio di App Web**| [Compilare un'app Web con Java SDK v4](sql-api-java-application.md) · [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Suggerimenti per incrementare le prestazioni**| [Suggerimenti sulle prestazioni per Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Risoluzione dei problemi** | [Risolvere i problemi di Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Eseguire la migrazione a v4 da un SDK precedente** | [Eseguire la migrazione a Java v4 SDK](migrate-java-v4-sdk.md) |
| **Runtime minimo supportato**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Workshop e laboratori su Azure Cosmos DB** |[Home page dei workshop per Cosmos DB](https://aka.ms/cosmosworkshop)

