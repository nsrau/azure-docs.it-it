---
title: Eseguire la migrazione dell'applicazione per l'uso di Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos)
description: Informazioni su come aggiornare l'applicazione Java esistente usando gli SDK precedenti di Azure Cosmos DB Java per il pacchetto Java SDK 4,0 (com. Azure. Cosmos) più recente per l'API di base (SQL).
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.reviewer: sngun
ms.openlocfilehash: 929fa936cdb864fd9b84f8feba55ef01ae6fed9c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984707"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Eseguire la migrazione dell'applicazione per l'uso di Azure Cosmos DB Java SDK v4

> [!IMPORTANT]  
> Per altre informazioni su questo SDK, vedere le note sulla versione di Azure Cosmos DB Java SDK v4, il [repository maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), i [suggerimenti](performance-tips-java-sdk-v4-sql.md)per le prestazioni di Azure Cosmos DB Java SDK v4 e la [Guida alla risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md)di Azure Cosmos DB Java SDK v4.
>

Questo articolo illustra come aggiornare l'applicazione Java esistente che usa una versione precedente di Azure Cosmos DB Java SDK alla versione più recente di Azure Cosmos DB Java SDK 4,0 per l'API di base (SQL). Azure Cosmos DB Java SDK v4 corrisponde al `com.azure.cosmos` pacchetto. Se si esegue la migrazione dell'applicazione da uno dei seguenti Azure Cosmos DB SDK Java, è possibile usare le istruzioni riportate in questo documento: 

* Sync Java SDK 2. x. x
* Async Java SDK 2. x. x
* Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB Java SDK e i mapping dei pacchetti

La tabella seguente elenca diversi Azure Cosmos DB SDK Java, il nome del pacchetto e le informazioni sulla versione:

| SDK per Java| Data di rilascio | API in bundle   | Jar Maven  | Nome del pacchetto Java  |Informazioni di riferimento sulle API   | Note sulla versione  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2. x. x  | Giugno 2018    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Note sulla versione](sql-api-sdk-async-java.md) |
| Sync 2. x. x     | 2018 settembre    | Sincronizza   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Note sulla versione](sql-api-sdk-java.md)  |
| 3. x. x    | Luglio 2019    | Asincrono (Reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | Aprile 2020   | Asincrono (Reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>Modifiche all'implementazione a livello di SDK

Di seguito sono riportate le principali differenze di implementazione tra SDK diversi:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava viene sostituito con Reactor in Azure Cosmos DB Java SDK versioni 3. x.x. x e 4,0

Se non si ha familiarità con la programmazione asincrona o la programmazione reattiva, vedere la [Guida al modello del reattore](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) per un'introduzione alla programmazione asincrona e al Reactor del progetto. Questa guida può essere utile se si usa Azure Cosmos DB Sync Java SDK 2. x. x o Azure Cosmos DB API di sincronizzazione Java SDK 3. x in passato.

Se si usa Azure Cosmos DB Async Java SDK 2. x. x e si prevede di eseguire la migrazione a 4,0 SDK, vedere la guida a [reattore vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) per informazioni aggiuntive sulla conversione del codice RxJava per l'uso del reattore.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 ha la modalità di connettività diretta nelle API asincrone e di sincronizzazione

Se si usa Azure Cosmos DB Sync Java SDK 2. x. x, si noti che la modalità di connessione diretta basata su TCP (invece di HTTP) viene implementata in Azure Cosmos DB Java SDK 4,0 per le API Async e Sync.

## <a name="api-level-changes"></a>Modifiche a livello di API

Di seguito sono riportate le modifiche a livello di API in Azure Cosmos DB Java SDK 4. x. x rispetto agli SDK precedenti (Java SDK 3. x. x, Async Java SDK 2. x. x e Sync Java SDK 2. x. x):

![Azure Cosmos DB convenzioni di denominazione Java SDK](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* Il Azure Cosmos DB Java SDK 3. x. x e 4,0 fanno riferimento alle risorse client `Cosmos<resourceName>`come. Ad esempio `CosmosClient`, `CosmosDatabase`, `CosmosContainer`. Mentre nella versione 2. x. x, gli SDK Java Azure Cosmos DB non hanno uno schema di denominazione uniforme.

* Azure Cosmos DB Java SDK 3. x. x e 4,0 offrono sia le API di sincronizzazione sia quelle asincrone.

  * **Java SDK 4,0** : tutte le classi appartengono all'API di sincronizzazione, a meno che il nome della classe non `Async` venga `Cosmos`aggiunto dopo.

  * **Java SDK 3. x.** x: tutte le classi appartengono all'API asincrona, a meno che il nome della classe non venga `Async` aggiunto `Cosmos`dopo.

  * **Async Java SDK 2. x. x**: i nomi delle classi sono simili a Sync Java SDK 2. x. x, ma il nome inizia con *Async*.

### <a name="hierarchical-api-structure"></a>Struttura dell'API gerarchica

Azure Cosmos DB Java SDK 4,0 e 3. x. x introducono una struttura API gerarchica che organizza i client, i database e i contenitori in modo annidato, come illustrato nel frammento di codice 4,0 SDK seguente:

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

Nella versione 2. x. x di Azure Cosmos DB Java SDK, tutte le operazioni su risorse e documenti vengono eseguite tramite l'istanza del client.

### <a name="representing-documents"></a>Rappresentazione di documenti

In Azure Cosmos DB Java SDK 4,0, i POJO personalizzati e `JsonNodes` sono le due opzioni per la lettura e la scrittura dei documenti da Azure Cosmos DB.

In Azure Cosmos DB Java SDK 3. x. x, l' `CosmosItemProperties` oggetto viene esposto dall'API pubblica e servito come rappresentazione del documento. Questa classe non è più esposta pubblicamente nella versione 4,0.

### <a name="imports"></a>Importazioni

* I pacchetti Azure Cosmos DB Java SDK 4,0 iniziano con`com.azure.cosmos`
  * Azure Cosmos DB pacchetti Java SDK 3. x. x iniziano con`com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4,0 posiziona diverse classi in un pacchetto `com.azure.cosmos.models`annidato. Alcuni di questi pacchetti includono:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Analogie dell'API asincrona per tutti i pacchetti precedenti
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`... Via.

### <a name="accessors"></a>Funzioni di accesso

Azure Cosmos DB Java SDK 4,0 espone `get` i `set` metodi e per accedere ai membri dell'istanza. Ad esempio, l' `CosmosContainer` istanza dispone `container.getId()` di `container.setId()` metodi e.

Questa operazione è diversa da Azure Cosmos DB Java SDK 3. x. x che espone un'interfaccia Fluent. Un' `CosmosSyncContainer` istanza `container.id()` , ad esempio, è sottoposta a overload per ottenere o impostare `id` il valore.

## <a name="code-snippet-comparisons"></a>Confronti frammenti di codice

### <a name="create-resources"></a>Creare le risorse

Il frammento di codice seguente mostra le differenze nella modalità di creazione delle risorse tra le API asincrone 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona di Java SDK 4,0](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[API Async Java SDK 3. x](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPo"ic"();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>Operazioni sugli elementi

Il frammento di codice seguente illustra le differenze nella modalità di esecuzione delle operazioni sugli elementi tra le API asincrone 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona di Java SDK 4,0](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-3xx-async-api"></a>[API Async Java SDK 3. x](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>Indicizzazione

Il frammento di codice seguente illustra le differenze nella modalità di creazione dell'indicizzazione tra le API asincrone 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona di Java SDK 4,0](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[API Async Java SDK 3. x](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>Stored procedure

Il frammento di codice seguente mostra le differenze nella modalità di creazione delle stored procedure tra le API asincrone 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona di Java SDK 4,0](#tab/java-v4-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-3xx-async-api"></a>[API Async Java SDK 3. x](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>Feed delle modifiche

Il frammento di codice seguente illustra le differenze nella modalità di esecuzione delle operazioni di feed delle modifiche tra le API asincrone 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona di Java SDK 4,0](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        logger.info("--->handleChanges() END");

    })
    .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[API Async Java SDK 3. x](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>Durata (TTL) a livello di contenitore

Il frammento di codice seguente illustra le differenze in come creare una durata per i dati nel contenitore usando le API asincrone 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona di Java SDK 4,0](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[API Async Java SDK 3. x](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>TTL (time-to-Live) a livello di elemento

Il frammento di codice seguente illustra le differenze in come creare una durata per un elemento usando le API asincrone 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API asincrona di Java SDK 4,0](#tab/java-v4-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-3xx-async-api"></a>[API Async Java SDK 3. x](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app java](create-sql-api-java.md) per gestire i dati dell'API SQL di Azure Cosmos DB usando v4 SDK
* Informazioni sugli [SDK Java basati su Reactor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)
* Informazioni sulla conversione del codice asincrono RxJava nel codice asincrono del reattore con la [Guida a reattore e RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)