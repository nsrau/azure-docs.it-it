---
title: 'Azure Cosmos DB Java SDK v4 per API SQL: note sulla versione e risorse'
description: Informazioni complete su Azure Cosmos DB Java SDK v4 per SDK e API SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 686c1e37e093a358bb5122fbfeccdfd98eb301ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804232"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 per API Core (SQL): note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [SDK di feed di modifiche .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring data V2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 per API Core (SQL) combina un'API asincrona e un'API sincrona in un unico artefatto Maven. V4 SDK offre prestazioni migliorate, nuove funzionalità API e supporto asincrono basato su Project Reactor e sulla [libreria Netty](https://netty.io/). Si prevede quindi che Azure Cosmos DB Java SDK v4 offra prestazioni più elevate rispetto ad [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) e [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Queste note sulla versione sono destinate solo ad Azure Cosmos DB Java SDK v4. Se si usa una versione precedente, vedere l'articolo [Eseguire la migrazione a Java SDK v4 per Azure Cosmos DB](migrate-java-v4-sdk.md) per informazioni sull'aggiornamento alla versione 4.
>
> Per iniziare rapidamente, eseguire questi tre passaggi.
> 1. Installare la [prima versione supportata di Java Runtime, JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) per poter usare l'SDK.
> 2. Consultare la [Guida di avvio rapido per Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java), che consente di accedere all'artefatto Maven e prende in esame le richieste di base di Azure Cosmos DB.
> 3. Leggere i [suggerimenti sulle prestazioni](performance-tips-java-sdk-v4-sql.md) e le guide alla [risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4 per ottimizzare l'SDK per l'applicazione.
>
> I [workshop e i laboratori su Azure Cosmos DB](https://aka.ms/cosmosworkshop) costituiscono un'altra risorsa molto utile per imparare a usare Azure Cosmos DB Java SDK v4.
>

## <a name="helpful-content"></a>Contenuto utile

| Content | Collegamento |
|---|---|
|**Download dell'SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentazione sull'API** | [Documentazione di riferimento API Java](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable&preserve-view=true) |
|**Contribuire all'SDK** | [Repository centrale di Azure SDK per Java in GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Operazioni preliminari** | [Avvio rapido: Compilare un'app Java per gestire i dati dell'API SQL di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [Repository GitHub con codice di avvio rapido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Esempi di codice di base** | [Azure Cosmos DB: esempi di Java dell'API SQL](sql-api-java-sdk-samples.md) <br> [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**App console con feed di modifiche**| [Esempio di feed di modifiche-Java SDK v4](create-sql-api-java-changefeed.md) <br> [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Esempio di App Web**| [Creare un'app Web con Java SDK v4](sql-api-java-application.md) <br> [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Suggerimenti per incrementare le prestazioni**| [Suggerimenti sulle prestazioni per Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Risoluzione dei problemi** | [Risolvere i problemi di Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Eseguire la migrazione a v4 da un SDK precedente** | [Eseguire la migrazione a Java v4 SDK](migrate-java-v4-sdk.md) |
| **Runtime minimo supportato**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 
| **Workshop e laboratori su Azure Cosmos DB** |[Home page dei workshop per Cosmos DB](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).