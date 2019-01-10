---
title: Come chiamare stored procedure, trigger e funzioni definite dall'utente con gli SDK di Azure Cosmos DB
description: Informazioni su come registrare e chiamare stored procedure, trigger e funzioni definite dall'utente con gli SDK di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/08/2018
ms.author: mjbrown
ms.openlocfilehash: 374bc040cf43f89899bbe1fc5b0835cff187ec9b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037600"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Come registrare e usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB

Con l'API SQL di Azure Cosmos DB è possibile registrare e richiamare stored procedure, trigger e funzioni definite dall'utente scritte in JavaScript. È possibile usare gli SDK [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md) o [Python](sql-api-sdk-python.md) dell'API SQL per registrare e richiamare le stored procedure. Dopo avere definito uno o più stored procedure, trigger e funzioni definite dall'utente, è possibile caricarli e visualizzarli nel [portale di Azure](https://portal.azure.com/) con Esplora dati.

## <a id="stored-procedures"></a>Come eseguire stored procedure

Le stored procedure vengono scritte in JavaScript e possono creare, aggiornare, leggere ed eliminare elementi all'interno di un contenitore di Azure Cosmos, nonché eseguire query su tali elementi. Per altre informazioni su come scrivere le stored procedure in Azure Cosmos DB, vedere l'articolo [Come scrivere stored procedure in Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures).

Gli esempi seguenti illustrano come registrare e chiamare una stored procedure con gli SDK di Azure Cosmos DB. Vedere [Creare un documento](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) perché l'origine di questa stored procedure viene salvata come `spCreateToDoItem.js`.

> [!NOTE]
> Per i contenitori partizionati, quando si esegue una stored procedure, è necessario specificare un valore della chiave di partizione nelle opzioni di richiesta. Le stored procedure hanno sempre come ambito una chiave di partizione. Gli elementi con un valore della chiave di partizione diverso non saranno visibili alla stored procedure. Questo vale anche per i trigger.

### <a name="stored-procedures---net-sdk"></a>Stored procedure - SDK .NET

L'esempio seguente illustra come registrare una stored procedure con l'SDK .NET:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

Il codice seguente illustra come chiamare una stored procedure con l'SDK .NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Stored procedure - SDK Java

L'esempio seguente illustra come registrare una stored procedure con l'SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

Il codice seguente illustra come chiamare una stored procedure con l'SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Stored procedure - SDK JavaScript

L'esempio seguente illustra come registrare una stored procedure con l'SDK JavaScript

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Il codice seguente illustra come chiamare una stored procedure con l'SDK JavaScript:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Stored procedure - SDK Python

L'esempio seguente illustra come registrare una stored procedure con l'SDK Python

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
            'id': 'spCreateToDoItem',
            'serverScript': file_contents,
        }
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

Il codice seguente illustra come chiamare una stored procedure con l'SDK Python

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Come eseguire pre-trigger

Gli esempi seguenti illustrano come registrare e chiamare un pre-trigger con gli SDK di Azure Cosmos DB. Vedere l'[esempio di pre-trigger](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) perché l'origine di questo pre-trigger viene salvata come `trgPreValidateToDoItemTimestamp.js`.

I pre-trigger quando vengono eseguiti vengono passati nell'oggetto RequestOptions specificando `PreTriggerInclude` e quindi passando il nome del trigger in un oggetto elenco.

> [!NOTE]
> Anche se il nome del trigger viene passato come elenco, sarà comunque possibile eseguire un solo trigger per ogni operazione.

### <a name="pre-triggers---net-sdk"></a>Pre-trigger - SDK .NET

Il codice seguente illustra come registrare un pre-trigger con l'SDK .NET:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Il codice seguente illustra come chiamare un pre-trigger con l'SDK .NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Pre-trigger - SDK Java

Il codice seguente illustra come registrare un pre-trigger con l'SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Il codice seguente illustra come chiamare un pre-trigger con l'SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Pre-trigger - SDK JavaScript

Il codice seguente illustra come registrare un pre-trigger con l'SDK JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

Il codice seguente illustra come chiamare un pre-trigger con l'SDK JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Pre-trigger - SDK Python

Il codice seguente illustra come registrare un pre-trigger con l'SDK Python:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
            'id': 'trgPreValidateToDoItemTimestamp',
            'serverScript': file_contents,
            'triggerType': documents.TriggerType.Pre,
            'triggerOperation': documents.TriggerOperation.Create
        }
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Il codice seguente illustra come chiamare un pre-trigger con l'SDK Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'category': 'Personal', 'name': 'Groceries', 'description':'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, { 'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Come eseguire post-trigger

Gli esempi seguenti illustrano come registrare un post-trigger con gli SDK di Azure Cosmos DB. Vedere l'[esempio di post-trigger](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) perché l'origine di questo post-trigger viene salvata come `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Post-trigger - SDK .NET

Il codice seguente illustra come registrare un post-trigger con l'SDK .NET:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js");,
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Il codice seguente illustra come chiamare un post-trigger con l'SDK .NET:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

var options = { postTriggerInclude: "trgPostUpdateMetadata" };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Post-trigger - SDK Java

Il codice seguente illustra come registrare un post-trigger con l'SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Il codice seguente illustra come chiamare un post-trigger con l'SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Post-trigger - SDK JavaScript

Il codice seguente illustra come registrare un post-trigger con l'SDK JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

Il codice seguente illustra come chiamare un post-trigger con l'SDK JavaScript:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Post-trigger - SDK Python

Il codice seguente illustra come registrare un post-trigger con l'SDK Python:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
            'id': 'trgPostUpdateMetadata',
            'serverScript': file_contents,
            'triggerType': documents.TriggerType.Post,
            'triggerOperation': documents.TriggerOperation.Create
        }
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Il codice seguente illustra come chiamare un post-trigger con l'SDK Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'name': 'artist_profile_1023', 'artist': 'The Band', 'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, { 'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Come usare le funzioni definite dall'utente

Gli esempi seguenti illustrano come registrare una funzione definita dall'utente con gli SDK di Azure Cosmos DB. Vedere l'[esempio di funzioni definite dall'utente](how-to-write-stored-procedures-triggers-udfs.md#udfs) perché l'origine di questa funzione definita dall'utente viene salvata come `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Funzioni definite dall'utente - SDK .NET

Il codice seguente illustra come registrare una funzione definita dall'utente con l'SDK .NET:

```csharp
string udfId = "udfTax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Il codice seguente illustra come chiamare una funzione definita dall'utente con l'SDK .NET:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Funzioni definite dall'utente - SDK Java

Il codice seguente illustra come registrare una funzione definita dall'utente con l'SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "udfTax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Il codice seguente illustra come chiamare una funzione definita dall'utente con l'SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Funzioni definite dall'utente - SDK JavaScript

Il codice seguente illustra come registrare una funzione definita dall'utente con l'SDK JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "udfTax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Il codice seguente illustra come chiamare una funzione definita dall'utente con l'SDK JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Funzioni definite dall'utente - SDK Python

Il codice seguente illustra come registrare una funzione definita dall'utente con l'SDK Python:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
            'id': 'trgPostUpdateMetadata',
            'serverScript': file_contents,
        }
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

Il codice seguente illustra come chiamare una funzione definita dall'utente con l'SDK Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(container_link, 'SELECT * FROM Incomes t WHERE udf.tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come scrivere o usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB:

- [Utilizzo delle stored procedure, dei trigger e delle funzioni definite dall'utente in Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Utilizzo dell'API di query integrata nel linguaggio JavaScript in Azure Cosmos DB](javascript-query-api.md)
- [Come scrivere stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Come scrivere stored procedure e trigger usando l'API di query di Javascript in Azure Cosmos DB](how-to-write-javascript-query-api.md)
