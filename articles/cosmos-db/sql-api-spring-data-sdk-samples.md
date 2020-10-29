---
title: 'API SQL di Azure Cosmos DB: esempi di Spring Data v3'
description: Trovare esempi Spring Data v3 in GitHub per attività comuni con l'API SQL di Azure Cosmos DB, incluse le operazioni CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: f49b7a2b4acc42724616121186093b112867d2b5
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487722"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>API SQL di Azure Cosmos DB: Esempi di Spring Data Azure Cosmos DB v3

> [!div class="op_single_selector"]
> * [Esempi di .NET V2 SDK](sql-api-dotnet-samples.md)
> * [Esempi di .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Esempi di Java V4 SDK](sql-api-java-sdk-samples.md)
> * [Esempi di Spring Data V3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Esempi di Node.js](sql-api-nodejs-samples.md)
> * [Esempi di Python](sql-api-python-samples.md)
> * [Raccolta di esempi di codice di Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

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

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- È possibile [attivare i vantaggi della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Con l'abbonamento Visual Studio ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Le applicazioni di esempio più recenti che eseguono operazioni CRUD e altre operazioni di uso comune su risorse di Azure Cosmos DB sono disponibili nel repository [azure-spring-data-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples) di GitHub. Questo articolo include:

* Collegamenti alle attività in ogni file di progetto Spring Data Azure Cosmos DB di esempio. 
* Collegamenti al contenuto di riferimento sulle API correlato.

**Prerequisiti**

Per eseguire questa applicazione di esempio, occorre quanto segue:

* Java Development Kit 8
* Spring Data Azure Cosmos DB v3

È possibile usare facoltativamente Maven per ottenere i file binari più recenti di Spring Data Azure Cosmos DB v3 da usare nel progetto. Maven aggiunge automaticamente le dipendenze necessarie. In caso contrario, è possibile scaricare direttamente le dipendenze elencate nel file **pom.xml** e aggiungerle al percorso di compilazione.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Esecuzione delle applicazioni di esempio**

Clonare il repository di esempio:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

È possibile eseguire gli esempi tramite un IDE (Eclipse, IntelliJ o VSCODE) o dalla riga di comando usando Maven.

In **application.properties** è necessario configurare queste variabili di ambiente

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

per concedere all'account, ai database e ai contenitori l'accesso in lettura/scrittura agli esempi.

È possibile che l'IDE consenta di eseguire il codice di esempio di Spring Data. In caso contrario, è possibile usare il comando seguente del terminale per eseguire l'esempio:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>Esempi CRUD del documento
Il file [samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) illustra come eseguire le attività seguenti. Per ottenere informazioni sui documenti di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](account-databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un documento](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository.save |
| [Leggere un documento in base all'ID](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [Eliminare tutti i documenti](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>Esempi di metodi di query derivati
Il file [samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) illustra come eseguire le attività seguenti. Per ottenere informazioni sulle query di Azure Cosmos DB prima di eseguire gli esempi seguenti, può risultare utile leggere l'articolo [Metodi di query derivati in Spring di Baeldung](https://www.baeldung.com/spring-data-derived-queries).

| [Query per i documenti](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository.derivedQueryMethod |

## <a name="custom-query-examples"></a>Esempi di query personalizzate
Il file [samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) illustra come eseguire le attività seguenti usando la grammatica di query SQL. Per informazioni sulle query SQL in Azure Cosmos DB prima di passare agli esempi seguenti, vedere [Esempi di query SQL per Azure Cosmos DB](./sql-query-getting-started.md). 


| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Eseguire query per tutti i documenti](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | Annotazione @Query |
| [Eseguire query relative all'uguaglianza con ==](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | Annotazione @Query |
| [Eseguire query relative alla disuguaglianza con != e NOT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | Annotazione @Query |
| [Eseguire query usando operatori di intervallo come >, <, >=, <=](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | Annotazione @Query |
| [Eseguire query usando operatori di intervallo su stringhe](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | Annotazione @Query |
| [Eseguire query con ORDER BY](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | Annotazione @Query |
| [Eseguire query con DISTINCT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | Annotazione @Query |
| [Eseguire query con funzioni di aggregazione](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | Annotazione @Query |
| [Utilizzare i documenti secondari](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | Annotazione @Query |
| [Eseguire query con join all'interno del documento](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | Annotazione @Query |
| [Eseguire query con operatori di stringa, matematici e di matrice](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | Annotazione @Query |