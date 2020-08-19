---
title: Note sulla versione di Spring data Azure Cosmos DB v2 per le API SQL
description: Informazioni su Spring data Azure Cosmos DB v2 per l'API SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590742"
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

Spring data Azure Cosmos DB v2 per core (SQL) consente agli sviluppatori di usare Azure Cosmos DB nelle applicazioni Spring. Spring data Azure Cosmos DB espone l'interfaccia Spring data per la modifica di database e raccolte, l'utilizzo di documenti e l'emissione di query. Entrambe le API Sync e async (Reactive) sono supportate nello stesso artefatto Maven. 

[Spring Framework](https://spring.io/projects/spring-framework) è un modello di programmazione e configurazione che semplifica lo sviluppo di applicazioni Java. Per citare il sito Web dell'organizzazione, Spring semplifica il "plumbing" delle applicazioni usando l'inserimento di dipendenze. Molti sviluppatori, ad esempio Spring, perché la compilazione e il test di applicazioni diventano più semplici. [Spring boot](https://spring.io/projects/spring-boot) estende questa idea di gestione del plumbing con un occhio per lo sviluppo di applicazioni Web e microservizi. [Spring data](https://spring.io/projects/spring-data) è un modello di programmazione per l'accesso agli archivi dati, ad esempio Azure Cosmos DB dal contesto di un'applicazione Spring o Spring boot. 

È possibile usare Spring data Azure Cosmos DB nelle applicazioni [cloud della primavera di Azure](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Queste note sulla versione sono per la versione V2 di Spring data Azure Cosmos DB. [Qui](sql-api-sdk-java-spring-v3.md)è possibile trovare le note sulla versione V3. 
>
> Spring data Azure Cosmos DB supporta solo l'API SQL.
>
> Le guide seguenti supportano i dati di Spring su altre API Azure Cosmos DB:
> * [Spring data per Apache Cassandra con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB dati di Spring con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Data Gremlin della primavera con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Si desidera procedere velocemente?
> 1. Installare la [prima versione supportata di Java Runtime, JDK 8](/java/azure/jdk/?view=azure-java-stable) per poter usare l'SDK.
> 2. Crea un'app Spring data Azure Cosmos DB usando lo Starter, è [facile](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)!
> 3. Usare la [Guida di Spring Data Azure Cosmos DB Developers](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) che illustra le richieste di Azure Cosmos DB di base.
>
> È possibile avviare rapidamente le app Spring boot Starter con [Spring Initializr](https://start.spring.io/)!
>

## <a name="helpful-content"></a>Contenuto utile

| Contenuto | Collegamento |
|---|---|
| **Download dell'SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Documentazione sull'API** | [Documentazione di riferimento di Spring data Azure Cosmos DB]() |
|**Contribuire all'SDK** | [Repository Spring data Azure Cosmos DB su GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Starter Spring boot**| [Azure Cosmos DB libreria client Starter Spring boot per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Esempio di app Spring TODO con Azure Cosmos DB**| [Esperienza Java end-to-end nel servizio app Linux (parte 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guida per gli sviluppatori** | [Guida per gli sviluppatori di Spring data Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Guida all'uso di Starter** | [Come usare Spring Boot Starter con l'API SQL di Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Repository GitHub per Azure Spring boot Starter Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Esempio con servizi app** | [Come usare Spring e Cosmos DB con Servizio app in Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Esempio di app TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Cronologia delle versioni

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiornare la versione di Spring boot a 2.3.0 
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiornamento della versione di Azure Cosmos DB a v 3.7.3
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Contiene correzioni di perdite di memoria e aggiornamenti della versione Netty di Cosmos SDK v 3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione del flag allowTelemetry da prendere in considerazione da CosmosDbConfig
* Proprietà TTL fissa nel contenitore

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunta della nuova API findAll by Partition Key
* Aggiornamento della versione di Azure-Cosmos a 3.7.0
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Fixed CollectionName-> ContainerName
* Correzione di entityClass & domainClass-> domainType
* Correzione della "restituzione della raccolta di entità salvata dal repository anziché dall'entità di input"

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione backport per "restituire la raccolta di entità salvata dal repository anziché dall'entità di input"

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Nuove funzionalità
* Versione Azure-Cosmos aggiornata a v 3.6.0
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiornamento della versione di Cosmos DB SDK a 3.5.0
* Aggiunto campo di annotazione per abilitare/disabilitare creazione automatica raccolta
* Migliore gestione delle eccezioni, esposto CosmosClientException tramite CosmosDBAccessException
* RequestCharge ed activityId esposti tramite ResponseDiagnostics
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzioni di aggiornamento 3.5.0 SDK "eccezione quando Cosmos DB intestazione della risposta HTTP è maggiore di 8192 byte", "ConsistencyPolicy. defaultConsistencyLevel () ha esito negativo in caso di decadimento delimitato e prefisso coerente"
* Correzione del comportamento delle API findById, restituito Empty non trovato, anziché generare un'eccezione
* Correzione di un bug per cui l'ordinamento non è stato applicato nella pagina successiva quando si usa CosmosPageRequest

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunto campo di annotazione per abilitare/disabilitare creazione automatica raccolta
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione del comportamento delle API findById, restituito Empty non trovato, anziché generare un'eccezione

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Nuove funzionalità
* Supporto completo del repository Cosmos reattivo
* Supporto della metrica di query e della stringa di diagnostica della richiesta Cosmos DB
* Aggiornamento della versione di Cosmos DB SDK a 3.3.1
* Aggiornamento della versione di Spring Framework a 5.2.0. RELEASE
* Aggiornamento della versione di Spring Data Commons a 2.2.0. RELEASE
* Aggiunta di findByIdAndPartitionKey, API deleteByIdAndPartitionKey
* Dipendenza rimossa da Azure-doumentdb
* DocumentDb di personalizzazione in Cosmos
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corretto "l'ordinamento genera un'eccezione quando pageSize è inferiore al numero totale di elementi nel repository"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Nuove funzionalità
* Deprecare le API del database di documenti
* Sono state aggiunte le API findByIdAndPartitionKey, deleteByIdAndPartitionKey.
* Aggiunto blocco ottimistico basato su _etag
* Espressione SPeL abilitata per il nome della raccolta documenti
* Miglioramenti di ObjectMapper.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunta dipendenza di Cosmos SDK V3
* Aggiunta del repository di Cosmos reattivo
* Aggiornamento dell'implementazione di DocumentDbTemplate per l'uso di Cosmos SDK V3.
* Altre modifiche di configurazione per il supporto del repository Cosmos reattivo.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Rimuovere la dipendenza applicationInsights per
    * Rischio potenziale di contaminazione delle dipendenze
    * Incompatibilità con Java 11
    * Evitare potenziali conseguenze sulle prestazioni per CPU e/o memoria.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Backporting rimuove la dipendenza applicationInsights per
    * Rischio potenziale di contaminazione delle dipendenze
    * Incompatibilità con Java 11
    * Evitare potenziali conseguenze sulle prestazioni per CPU e/o memoria.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiornare la versione master a 2.1.1
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Nuove funzionalità
* Ignora tutte le eccezioni dalla telemetria
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiornare la versione di 2.1.0 per risolvere il problema
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Nuove funzionalità
* Parola chiave add esistente, startsWith
* Aggiornare il file Leggimi
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione di "chiamata a self-href direttamente per l'entità"
* FIX "findAll avrà esito negativo se la raccolta non viene creata"

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (versione preliminare) (2018-08-23)
#### <a name="new-features"></a>Nuove funzionalità
* Ridenominazione del pacchetto da documentdb a cosmosdb,
* Aggiunta di una nuova funzionalità della parola chiave del metodo di query, 16 parole chiave dall'API SQL supportata.
* Consente di aggiungere una nuova funzionalità di query con paging e ordinamento.
* Semplificare la configurazione di Spring-data-cosmosdb.
* Aggiungere DeleteCollection e l'API deleteAll.

#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione di bug e miglioramento del difetto.

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Per altre informazioni sul framework Spring, vedere il [Home page di progetto](https://spring.io/projects/spring-framework).

Per altre informazioni su Spring boot, vedere il [Home page di progetto](https://spring.io/projects/spring-boot).

Per ulteriori informazioni sui dati di Spring, vedere il [Home page di progetto](https://spring.io/projects/spring-data).