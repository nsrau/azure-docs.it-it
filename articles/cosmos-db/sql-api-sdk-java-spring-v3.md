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
ms.openlocfilehash: 9e5b92918d93109183740be555bb805877862407
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817871"
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

Spring data Azure Cosmos DB versione 3 per core (SQL) consente agli sviluppatori di usare Azure Cosmos DB nelle applicazioni Spring. Spring data Azure Cosmos DB espone l'interfaccia Spring data per la modifica di database e raccolte, l'utilizzo di documenti e l'emissione di query. Entrambe le API Sync e async (Reactive) sono supportate nello stesso artefatto Maven. 

Spring data Azure Cosmos DB presenta una dipendenza dal framework Spring data. Il team di Azure Cosmos DB SDK rilascia gli artefatti Maven per Spring data Versions 2,2 e 2,3.

[Spring Framework](https://spring.io/projects/spring-framework) è un modello di programmazione e configurazione che semplifica lo sviluppo di applicazioni Java. Spring semplifica il "plumbing" delle applicazioni usando l'inserimento di dipendenze. Molti sviluppatori come Spring perché rendono più semplice la creazione e il test di applicazioni. [Spring boot](https://spring.io/projects/spring-boot) estende questa gestione del plumbing con un occhio verso lo sviluppo di applicazioni Web e microservizi. [Spring data](https://spring.io/projects/spring-data) è un modello di programmazione e un Framework per l'accesso a archivi dati come Azure Cosmos DB dal contesto di un'applicazione Spring o Spring boot. 

È possibile usare Spring data Azure Cosmos DB nelle applicazioni [cloud della primavera di Azure](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Queste note sulla versione sono rilasciate per la versione 3 di Spring data Azure Cosmos DB. È possibile trovare le [Note sulla versione 2 qui](sql-api-sdk-java-spring-v2.md). 
>
> Spring data Azure Cosmos DB supporta solo l'API SQL.
>
> Vedere questi articoli per informazioni sui dati primaverili su altre API Azure Cosmos DB:
> * [Spring data per Apache Cassandra con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB dati di Spring con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Data Gremlin della primavera con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Inizia da qui

# <a name="explore"></a>[Esplorare](#tab/explore)

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

Crea:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Create":::

Delete (Elimina):
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Delete":::

# <a name="query"></a>[Query](#tab/queries)

Query:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Query":::

---

## <a name="resources"></a>Risorse

* **Contribuire all'SDK**: [Spring data Azure Cosmos DB repo su GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Esercitazione**: [Introduzione a Spring data Azure Cosmos DB su GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

## <a name="release-history"></a>Cronologia delle versioni

### <a name="300-beta2-september-17-2020"></a>3.0.0-beta. 2 (17 settembre 2020)

#### <a name="new-features"></a>Nuove funzionalità

* ID artefatto aggiornato a `azure-spring-data-cosmos` .
* La dipendenza Azure-Cosmos è stata aggiornata a `4.5.0` .
* `Query Annotation` supporto per le query native.
* Supporto per Java 11.
* Aggiunta del supporto per la chiave di partizione nidificata esponendo il `partitionKeyPath` campo nell' `@Container` annotazione.
* Aggiunta del supporto per il `limit` tipo di query che consente `top` e `first` da utilizzare per la definizione delle API del repository.

#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

* Correzione del bug della chiave di partizione annidata se utilizzata con l' `@GeneratedValue` annotazione.

### <a name="300-beta1-august-17-2020"></a>3.0.0-beta. 1 (17 agosto 2020)

#### <a name="new-features"></a>Nuove funzionalità

* Aggiorna l'ID del gruppo a `com.azure` .
* Aggiorna l'ID dell'artefatto a `azure-spring-data-2-3-cosmos` .
* Aggiorna la dipendenza di Azure-Cosmos SDK a `4.3.2-beta.2` .
* Aggiunge il supporto per le entità di controllo: gestione automatica dei `createdBy` `createdDate` campi,, `lastModifiedBy` e `lastModifiedDate` annotati.
* Aggiunge il `@GeneratedValue` supporto dell'annotazione per la generazione automatica di ID per i campi ID di `String` tipo.
* Aggiunge il supporto per la configurazione di più database per gli account con Azure Cosmos DB singola con più database e più account Azure Cosmos DB con più database.
* Aggiunge il supporto per l' `@Version` annotazione in qualsiasi campo stringa.
* Aggiorna i tipi restituiti dell'API di sincronizzazione ai `Iterable` tipi anziché `List` .
* Espone `CosmosClientBuilder` dal Azure Cosmos DB SDK come Spring Bean alla `@Configuration` classe.
* Aggiornamenti `CosmosConfig` per contenere metriche di query e implementazione del processore di diagnostica delle risposte.
* Aggiunge il supporto per la restituzione del `Optional` tipo di dati per le singole query di risultato.

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
* Corregge il blocco ottimistico sull'operazione di eliminazione.
* Corregge il problema relativo alla clausola di escape delle query per la `IN` clausola.
* Corregge il problema consentendo il `long` tipo di dati per `@Id` .
* Corregge il problema consentendo `boolean` `long` `int` i tipi di dati,, e `double` per l' `@PartitionKey` annotazione.
* Correzioni `IgnoreCase` e `AllIgnoreCase` parole chiave per le query di Ignora caso.
* Rimuove il valore predefinito dell'unità richiesta 4.000 quando i contenitori vengono creati automaticamente.

## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Scopri di più sul [framework Spring](https://spring.io/projects/spring-framework).

Scopri di più su [Spring boot](https://spring.io/projects/spring-boot).

Scopri di più sui [dati primaverili](https://spring.io/projects/spring-data).