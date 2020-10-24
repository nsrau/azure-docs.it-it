---
title: 'Azure Cosmos DB: SQL Async Java API, SDK e risorse'
description: Informazioni complete sull'SDK e sull'API SQL Async Java, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: a3f9261922a5d281e173fba9e833da2ba82e9102
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477811"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK per API SQL: note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [Provider di risorse REST](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Esecuzione bulk - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SD differisce da SQL API Java SDK offrendo operazioni asincrone con il supporto della libreria [Netty](https://netty.io/). La versione pre-esistente [SQL API Java SDK](sql-api-sdk-java.md) non supporta operazioni asincrone. 

> [!IMPORTANT]  
> Questa *non* corrisponde alla versione più recente di Java SDK per Azure Cosmos DB. Provare a usare [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) per il progetto. Per eseguire l'aggiornamento, seguire le istruzioni della guida alla [migrazione a Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) e la guida relativa al [confronto tra Reactor e RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md). 
>

| |  |
|---|---|
| **Download dell'SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Documentazione sull'API** |[Documentazione di riferimento API Java](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?preserve-view=true&view=azure-java-stable) | 
|**Contribuire all'SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Operazioni preliminari** | [Introduzione ad Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Codice di esempio** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Suggerimenti per incrementare le prestazioni**| [File Leggimi di GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Runtime minimo supportato**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).