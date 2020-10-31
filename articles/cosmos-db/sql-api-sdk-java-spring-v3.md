---
title: Spring data Azure Cosmos DB V3 per le risorse e le note sulla versione dell'API SQL
description: Informazioni sull'API Spring data Azure Cosmos DB V3 per SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 64054a2bb5c1f7e17eef87c3babb28137b6c912a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097125"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring data Azure Cosmos DB V3 per l'API Core (SQL): Note sulla versione e risorse
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Esecuzione bulk - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Spring data Azure Cosmos DB versione 3 per core (SQL) consente agli sviluppatori di usare Azure Cosmos DB nelle applicazioni Spring. Spring data Azure Cosmos DB espone l'interfaccia Spring data per la modifica di database e raccolte, l'utilizzo di documenti e l'emissione di query. Entrambe le API Sync e async (Reactive) sono supportate nello stesso artefatto Maven. 

Spring data Azure Cosmos DB presenta una dipendenza dal framework Spring data. Il team di Azure Cosmos DB SDK rilascia gli artefatti Maven per Spring data Versions 2,2 e 2,3.

[Spring Framework](https://spring.io/projects/spring-framework) è un modello di programmazione e configurazione che semplifica lo sviluppo di applicazioni Java. Spring semplifica il "plumbing" delle applicazioni usando l'inserimento di dipendenze. Molti sviluppatori come Spring perché rendono più semplice la creazione e il test di applicazioni. [Spring boot](https://spring.io/projects/spring-boot) estende questa gestione del plumbing con un occhio verso lo sviluppo di applicazioni Web e microservizi. [Spring data](https://spring.io/projects/spring-data) è un modello di programmazione e un Framework per l'accesso a archivi dati come Azure Cosmos DB dal contesto di un'applicazione Spring o Spring boot. 

È possibile usare Spring data Azure Cosmos DB nelle applicazioni [cloud della primavera di Azure](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Queste note sulla versione sono relative alla versione 3 di Spring Data Azure Cosmos DB. È possibile trovare [qui le note sulla versione per la versione 2](sql-api-sdk-java-spring-v2.md). 
>
> Spring Data Azure Cosmos DB supporta solo l'API SQL.
>
> Per informazioni su Spring Data in altre API di Azure Cosmos DB, vedere questi articoli:
> * [Spring Data per Apache Cassandra con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Inizia da qui

# <a name="explore"></a>[Esplora](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Queste schede contengono i dati di base di Spring Azure Cosmos DB esempi.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Configurare le dipendenze

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Connettere](#tab/connect)

### <a name="connect"></a>Connessione

Specificare Azure Cosmos DB dettagli dell'account e del contenitore. Spring data Azure Cosmos DB crea automaticamente il client e si connette al contenitore.

[Application. Properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Operazioni doc](#tab/docs)

### <a name="document-operations"></a>Operazioni relative ai documenti

---

## <a name="resources"></a>Risorse

* **Contribuire all'SDK** : [Spring data Azure Cosmos DB repo su GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Esercitazione** : [Introduzione a Spring data Azure Cosmos DB su GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Scopri di più sul [framework Spring](https://spring.io/projects/spring-framework).

Scopri di più su [Spring boot](https://spring.io/projects/spring-boot).

Scopri di più sui [dati primaverili](https://spring.io/projects/spring-data).