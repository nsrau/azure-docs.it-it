---
title: Informazioni su come gestire la coerenza in Azure Cosmos DB
description: Informazioni su come gestire la coerenza in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: 511a12cd7f1e88a95342cf5129142791c6d50b31
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000897"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gestire i livelli di coerenza in Azure Cosmos DB

Questo articolo illustra come gestire i livelli di coerenza in Azure Cosmos DB. Si apprenderà come configurare la coerenza predefinita, sostituire la coerenza predefinita, gestire manualmente i token di sessione e monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Configurare il livello di coerenza predefinito

Il [livello di coerenza predefinito](consistency-levels.md) è il livello di coerenza che i client usano per impostazione predefinita. I client possono sempre eseguirne l'override.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

Questo esempio crea un nuovo account Azure Cosmos con più aree di scrittura abilitate nelle aree Stati Uniti orientali e Stati Uniti occidentali. Il livello di coerenza predefinito viene impostato su *Sessione*.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Portale di Azure

Per visualizzare o modificare il livello di coerenza predefinito, accedere al portale di Azure. Individuare l'account Azure Cosmos e aprire il riquadro **Coerenza predefinita**. Selezionare il livello di coerenza desiderato come il nuovo valore predefinito e quindi selezionare **Salva**. Il portale di Azure offre anche una visualizzazione dei diversi livelli di coerenza con note musicali. 

![Menu relativo alla coerenza nel portale di Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Sostituire il livello di coerenza predefinito

Nei client è possibile impostare un livello di coerenza diverso dall'impostazione predefinita del servizio. Il livello di coerenza può essere impostato per singole richieste, che esegue l'override del livello di coerenza predefinito impostato a livello di account.

### <a id="override-default-consistency-dotnet"></a>.NET SDK V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>.NET SDK V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async SDK

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Utilizzare i token di sessione

Uno dei livelli di coerenza in Azure Cosmos DB è il livello di coerenza *Sessione*. Questo è il livello predefinito applicato agli account Cosmos per impostazione predefinita. Quando si usa il livello di coerenza *Sessione*, il client userà internamente un token di sessione con ogni richiesta di lettura/query per assicurarsi che venga mantenuto il livello di coerenza impostato.

Per gestire i token di sessione manualmente, ottenere il token di sessione dalla risposta e impostarli per ogni richiesta. Se non si ha necessità di gestire manualmente i token di sessione, non è necessario usare questi esempi. L'SDK tiene traccia dei token di sessione automaticamente. Se non si imposta il token di sessione manualmente, per impostazione predefinita l'SDK usa il token di sessione più recente.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS)

Com'è la coerenza finale? Per il caso medio, è possibile offrire un decadimento ristretto rispetto alla cronologia delle versioni e al tempo. La metrica del decadimento ristretto probabilistico ([**Probabilistic Bounded Staleness, PBS)** ](https://pbs.cs.berkeley.edu/) cerca di quantificare la probabilità di decadimento e la mostra come metrica. Per visualizzare la metrica PBS, passare all'account Azure Cosmos nel portale di Azure. Aprire il riquadro **Metriche** e selezionare la scheda **Coerenza**. Esaminare il grafico denominato **Probabilità di letture con coerenza assoluta in base al carico di lavoro (vedere PBS)** .

![Grafico PBS nel portale di Azure](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Passaggi successivi

Scoprire di più su come gestire i conflitti di dati o passare al concetto chiave successivo di Azure Cosmos DB. Vedere gli articoli seguenti:

* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Gestire i conflitti tra le aree](how-to-manage-conflicts.md)
* [Partizionamento e distribuzione dei dati](partition-data.md)
* [Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Disponibilità elevata](high-availability.md)
* [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
