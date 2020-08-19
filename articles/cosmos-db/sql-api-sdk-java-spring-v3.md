---
title: Spring data Azure Cosmos DB V3 per le risorse e le note sulla versione dell'API SQL
description: Informazioni su Spring data Azure Cosmos DB V3 per l'API SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 04a50d37606754ff4540d1056e378d46388e2592
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590727"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring data Azure Cosmos DB V3 per l'API Core (SQL): Note sulla versione e risorse
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
> * [Spring data V3](sql-api-sdk-java-spring-v3.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Spring data Azure Cosmos DB V3 for Core (SQL) consente agli sviluppatori di usare Azure Cosmos DB nelle applicazioni Spring. Spring data Azure Cosmos DB espone l'interfaccia Spring data per la modifica di database e raccolte, l'utilizzo di documenti e l'emissione di query. Entrambe le API Sync e async (Reactive) sono supportate nello stesso artefatto Maven. 

Spring data Azure Cosmos DB acquisisce una dipendenza dal framework Spring data. Il team di Azure Cosmos DB SDK rilascia gli artefatti Maven per Spring data v 2.2 e v 2.3.

[Spring Framework](https://spring.io/projects/spring-framework) è un modello di programmazione e configurazione che semplifica lo sviluppo di applicazioni Java. Per citare il sito Web dell'organizzazione, Spring semplifica il "plumbing" delle applicazioni usando l'inserimento di dipendenze. Molti sviluppatori, ad esempio Spring, perché la compilazione e il test di applicazioni diventano più semplici. [Spring boot](https://spring.io/projects/spring-boot) estende questa idea di gestione del plumbing con un occhio per lo sviluppo di applicazioni Web e microservizi. [Spring data](https://spring.io/projects/spring-data) è un modello di programmazione e un Framework per l'accesso agli archivi dati, ad esempio Azure Cosmos DB dal contesto di un'applicazione Spring o Spring boot. 

È possibile usare Spring data Azure Cosmos DB nelle applicazioni [cloud della primavera di Azure](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Queste note sulla versione sono destinate alla versione V3 di Spring data Azure Cosmos DB. [Qui](sql-api-sdk-java-spring-v2.md)è possibile trovare le note sulla versione V2. 
>
> Spring data Azure Cosmos DB supporta solo l'API SQL.
>
> Le guide seguenti supportano i dati di Spring su altre API Azure Cosmos DB:
> * [Spring data per Apache Cassandra con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB dati di Spring con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Data Gremlin della primavera con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Inizia da qui

# <a name="explore"></a>[Esplora](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>Esplorare le schede sopra per i dati di base Spring Azure Cosmos DB Samples.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Configurare le dipendenze

Sono disponibili due elementi Spring data Azure Cosmos DB V3 Maven.

Artefatto che dipende da Spring Data Framework v 2.2:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

Artefatto che dipende da Spring Data Framework v 2.3:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[Connettere](#tab/connect)

### <a name="connect"></a>Connessione

Specificare Azure Cosmos DB dettagli dell'account e del contenitore. Spring data Azure Cosmos DB crea automaticamente il client e si connette al contenitore.

[Application. Properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
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

[Crea](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Elimina](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Query](#tab/queries)

### <a name="query"></a>Query

[Query](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>Contenuto utile

| Contenuto | Spring Data Framework v 2.2 | Framework Spring data v 2.3 |
|---|---|
| **Download dell'SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Contribuire all'SDK** | [Repository Spring data Azure Cosmos DB su GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Repository Spring data Azure Cosmos DB su GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Esercitazione**| [Esercitazione sui dati primaverili Azure Cosmos DB su GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Esercitazione sui dati primaverili Azure Cosmos DB su GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Cronologia delle versioni

### <a name="300-beta1-2020-08-17"></a>3.0.0-beta. 1 (2020-08-17)
#### <a name="new-features"></a>Nuove funzionalità
* ID gruppo aggiornato a `com.azure` .
* ID artefatto aggiornato a `azure-spring-data-2-3-cosmos` .
* La dipendenza Azure-Cosmos SDK è stata aggiornata a `4.3.2-beta.2` .
* Supporto per le entità di controllo: gestione automatica dei campi con annotazioni createdBy, createdDate, lastModifiedBy e lastModifiedDate.
* `@GeneratedValue` supporto dell'annotazione per la generazione automatica di ID per campi ID di `String` tipo.
* Supporto della configurazione di più database per un singolo account Cosmos con più database e più account Cosmos con più database.
* Supporto per l' `@Version` annotazione in qualsiasi campo stringa.
* Le API di sincronizzazione aggiornate restituiscono tipi ai `Iterable` tipi anziché a `List` .
* Esposto `CosmosClientBuilder` da Cosmos SDK come Spring Bean alla `@Configuration` classe.
* Aggiornamento `CosmosConfig` per contenere le metriche di query e l'implementazione del processore di diagnostica della risposta.
* Supporto per la restituzione del `Optional` tipo di dati per query a risultato singolo.
#### <a name="renames"></a>Rinomina
* `CosmosDbFactory` a `CosmosFactory` .
* `CosmosDBConfig` a `CosmosConfig` .
* `CosmosDBAccessException` a `CosmosAccessException` .
* `Document` annotazione per l' `Container` annotazione.
* `DocumentIndexingPolicy` annotazione per l' `CosmosIndexingPolicy` annotazione.
* `DocumentQuery` a `CosmosQuery` .
* Application. Properties flag `populateQueryMetrics` a `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Pianificazione dell'attività di registrazione diagnostica nei `Parallel` thread per evitare il blocco di thread I/O netti.
* Correzione del blocco ottimistico sull'operazione di eliminazione.
* Correzione del problema relativo alla clausola di escape delle query per la `IN` clausola.
* Correzione del problema consentendo il `long` tipo di dati per `@Id` .
* Correzione del problema consentendo `boolean` `long` i tipi di dati,,, `int` `double` come per l' `@PartitionKey` annotazione.
* Correzione `IgnoreCase`  &  `AllIgnoreCase` delle parole chiave per le query di Ignora caso.
* Rimosso il valore predefinito di unità richiesta 4000 quando si creano i contenitori automaticamente.

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Per altre informazioni sul framework Spring, vedere il [Home page di progetto](https://spring.io/projects/spring-framework).

Per altre informazioni su Spring boot, vedere il [Home page di progetto](https://spring.io/projects/spring-boot).

Per ulteriori informazioni sui dati di Spring, vedere il [Home page di progetto](https://spring.io/projects/spring-data).