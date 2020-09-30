---
title: Note sulla versione di Spring data Azure Cosmos DB v2 per le API SQL
description: Informazioni sui dati di Spring Azure Cosmos DB v2 per l'API SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 11278f558f94fe358be94c914ecfeae6cfd5461e
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570745"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring data Azure Cosmos DB v2 per l'API Core (SQL): Note sulla versione e risorse
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

 Spring data Azure Cosmos DB versione 2 per core (SQL) consente agli sviluppatori di usare Azure Cosmos DB nelle applicazioni Spring. Spring data Azure Cosmos DB espone l'interfaccia Spring data per la modifica di database e raccolte, l'utilizzo di documenti e l'emissione di query. Entrambe le API Sync e async (Reactive) sono supportate nello stesso artefatto Maven. 

[Spring Framework](https://spring.io/projects/spring-framework) è un modello di programmazione e configurazione che semplifica lo sviluppo di applicazioni Java. Spring semplifica il "plumbing" delle applicazioni usando l'inserimento di dipendenze. Molti sviluppatori come Spring perché rendono più semplice la creazione e il test di applicazioni. [Spring boot](https://spring.io/projects/spring-boot) estende questa gestione del plumbing con un occhio verso lo sviluppo di applicazioni Web e microservizi. [Spring data](https://spring.io/projects/spring-data) è un modello di programmazione per l'accesso a archivi dati come Azure Cosmos DB dal contesto di un'applicazione Spring o Spring boot. 

È possibile usare Spring data Azure Cosmos DB nelle applicazioni [cloud della primavera di Azure](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Queste note sulla versione sono destinate alla versione 2 di Spring data Azure Cosmos DB. È possibile trovare le [Note sulla versione 3 qui](sql-api-sdk-java-spring-v3.md). 
>
> Spring data Azure Cosmos DB supporta solo l'API SQL.
>
> Per informazioni sui dati primaverili su altre API Azure Cosmos DB, vedere gli articoli seguenti:
> * [Spring data per Apache Cassandra con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB dati di Spring con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Data Gremlin della primavera con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Si desidera procedere velocemente?
> 1. Installare il [runtime Java minimo supportato, JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true), in modo da poter usare l'SDK.
> 2. Creare un'app Spring data Azure Cosmos DB usando lo [Starter](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). È semplice.
> 3. Usare la guida per gli [sviluppatori di Spring Data Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb), che illustra le richieste di Azure Cosmos DB di base.
>
> È possibile avviare rapidamente le app Spring boot Starter con [Spring Initializr](https://start.spring.io/)!
>

## <a name="resources"></a>Risorse

| Risorsa | Collegamento |
|---|---|
| **Download dell'SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Documentazione sull'API** | [Documentazione di riferimento di Spring data Azure Cosmos DB]() |
|**Contribuire all'SDK** | [Repository Spring data Azure Cosmos DB su GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Starter Spring boot**| [Azure Cosmos DB libreria client Starter Spring boot per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Esempio di app Spring TODO con Azure Cosmos DB**| [Esperienza Java end-to-end nel servizio app Linux (parte 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guida per gli sviluppatori** | [Guida per sviluppatori di Spring Data Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Uso di Starter** | [Come usare Spring boot Starter con l'API SQL di Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Repository GitHub per Azure Cosmos DB Starter Spring boot](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Esempio con servizio app Azure** | [Come usare Spring e Cosmos DB con Servizio app in Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Esempio di app TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Cronologia delle versioni

### <a name="230-may-21-2020"></a>2.3.0 (21 maggio 2020)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna la versione Spring boot a 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19 maggio 2020)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna Azure Cosmos DB versione a 3.7.3.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Contiene correzioni di perdite di memoria e aggiornamenti della versione Netty da Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 aprile 2020)
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge il flag da considerare `allowTelemetry` `CosmosDbConfig` .
* Corregge la `TTL` proprietà nel contenitore.

### <a name="223-february-25-2020"></a>2.2.3 (25 febbraio 2020)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge nuova `findAll` API per la chiave di partizione.
* Aggiorna Azure Cosmos DB versione a 3.7.0.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzioni `collectionName`  ->  `containerName` .
* Corregge `entityClass` e `domainClass`  ->  `domainType` .
* Corregge "restituire la raccolta di entità salvata dal repository anziché dall'entità di input".

### <a name="2110-february-25-2020"></a>2.1.10 (25 febbraio 2020)
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Backports correzione per "restituire la raccolta di entità salvata dal repository anziché dall'entità di input".

### <a name="222-january-15-2020"></a>2.2.2 (15 gennaio 2020)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna Azure Cosmos DB versione a 3.6.0.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="221-december-31-2019"></a>2.2.1 (31 dicembre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna Azure Cosmos DB versione SDK a 3.5.0.
* Aggiunge un campo di annotazione per abilitare o disabilitare la creazione automatica della raccolta.
* Migliora la gestione delle eccezioni. Espone `CosmosClientException` tramite `CosmosDBAccessException` .
* Espone `requestCharge` e `activityId` fino a `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzioni di aggiornamento 3.5.0 SDK "eccezione quando Cosmos DB intestazione della risposta HTTP è maggiore di 8192 byte", "" ConsistencyPolicy. defaultConsistencyLevel () ha esito negativo in caso di decadimento ristretto e prefisso coerente ".
* Corregge `findById` il comportamento del metodo. In precedenza, questo metodo restituisce un valore vuoto se l'entità non è stata trovata anziché generare un'eccezione.
* Corregge un bug in cui l'ordinamento non è stato applicato alla pagina successiva quando `CosmosPageRequest` è stato utilizzato.

### <a name="219-december-26-2019"></a>2.1.9 (26 dicembre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge un campo di annotazione per abilitare o disabilitare la creazione automatica della raccolta.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
*  Corregge `findById` il comportamento del metodo. In precedenza, questo metodo restituisce un valore vuoto se l'entità non è stata trovata anziché generare un'eccezione.

### <a name="220-october-21-2019"></a>2.2.0 (21 ottobre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Completare il supporto del repository Cosmos reattivo.
* Azure Cosmos DB la stringa di diagnostica della richiesta e il supporto delle metriche di query.
* Aggiornamento della versione di Azure Cosmos DB SDK a 3.3.1.
* Aggiornamento della versione di Spring Framework a 5.2.0. RELEASE.
* Aggiornamento della versione di Spring Data Commons a 2.2.0. RELEASE.
* Aggiunge `findByIdAndPartitionKey` le `deleteByIdAndPartitionKey` API e.
* Rimuove la dipendenza da Azure-documentdb.
* Consente di rietichettare DocumentDB in Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzioni "l'ordinamento genera un'eccezione quando pageSize è inferiore al numero totale di elementi nel repository".

### <a name="218-october-18-2019"></a>2.1.8 (18 ottobre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Depreca le API DocumentDB.
* Aggiunge `findByIdAndPartitionKey` le `deleteByIdAndPartitionKey` API e.
* Aggiunge un blocco ottimistico basato su `_etag` .
* Abilita l'espressione SpEL per il nome della raccolta documenti.
* Aggiunge `ObjectMapper` miglioramenti.

### <a name="217-october-18-2019"></a>2.1.7 (18 ottobre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge la dipendenza Azure Cosmos DB SDK versione 3.
* Aggiunge un repository Cosmos reattivo.
* Aggiorna l'implementazione di `DocumentDbTemplate` per usare Azure Cosmos DB SDK versione 3.
* Aggiunge altre modifiche di configurazione per il supporto del repository Cosmos reattivo.

### <a name="212-march-19-2019"></a>2.1.2 (19 marzo 2019)
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Rimuove `applicationInsights` la dipendenza per:
    * Rischio potenziale di inquinamento delle dipendenze.
    * Incompatibilità con Java 11.
    * Evitare potenziali conseguenze sulle prestazioni per CPU e/o memoria.

### <a name="207-march-20-2019"></a>2.0.7 (20 marzo 2019)
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Backporting rimuove `applicationInsights` la dipendenza per:
    * Rischio potenziale di inquinamento delle dipendenze.
    * Incompatibilità con Java 11.
    * Evitare potenziali conseguenze sulle prestazioni per CPU e/o memoria.

### <a name="211-march-7-2019"></a>2.1.1 (7 marzo 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna la versione principale a 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 marzo 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Ignorare tutte le eccezioni dalla telemetria.

### <a name="210-december-17-2018"></a>2.1.0 (17 dicembre 2018)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna la versione a 2.1.0 per risolvere il problema.

### <a name="205-september-13-2018"></a>2.0.5 (13 settembre 2018)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge parole chiave `exists` e `startsWith` .
* Leggimi degli aggiornamenti.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzioni "Impossibile chiamare self href direttamente per l'entità".
* Correzioni "findAll avrà esito negativo se la raccolta non viene creata".

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (versione preliminare) (23 agosto 2018)
#### <a name="new-features"></a>Nuove funzionalità
* Rinomina il pacchetto da documentdb a cosmosdb.
* Aggiunge una nuova funzionalità della parola chiave del metodo di query. sono ora supportate 16 parole chiave dall'API SQL.
* Aggiunge una nuova funzionalità di query con paging e ordinamento.
* Semplifica la configurazione di Spring-data-cosmosdb.
* Aggiunge `deleteCollection` le `deleteAll` API e.

#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione di bug e attenuazione dei difetti.

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Scopri di più sul [framework Spring](https://spring.io/projects/spring-framework).

Scopri di più su [Spring boot](https://spring.io/projects/spring-boot).

Scopri di più sui [dati primaverili](https://spring.io/projects/spring-data).