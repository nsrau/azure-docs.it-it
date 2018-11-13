---
title: Informazioni su come gestire la coerenza in Azure Cosmos DB
description: Informazioni su come gestire la coerenza in Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: be2c68922221af848c9e484d03527d02808c071a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283819"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gestire i livelli di coerenza in Azure Cosmos DB

Questo articolo illustra i diversi modi per impostare la coerenza predefinita, impostare una coerenza diversa nel client, gestire manualmente i token di sessione e monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS).

## <a name="configure-the-default-consistency-level"></a>Configurare il livello di coerenza predefinito

Il livello di coerenza predefinito è il livello di coerenza che i client useranno per impostazione predefinita. È possibile specificare un livello diverso nei singoli client.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

In questo esempio viene creato un nuovo account Cosmos DB con multimaster abilitato nelle aree Stati Uniti orientali e Stati Uniti occidentali, che imposta il criterio di coerenza predefinito come decadimento ristretto con un intervallo di decadimento massimo di 10 secondi e un numero massimo di richieste non aggiornate tollerate uguale a 200.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>Portale

Per visualizzare o modificare il livello di coerenza predefinito, accedere al portale di Azure. Individuare l'account Cosmos DB e aprire il riquadro **Coerenza predefinita**. In questo riquadro scegliere il livello di coerenza desiderato come nuovo valore predefinito e fare clic su salva.

![Immagine del menu relativo alla coerenza nel portale di Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Sostituire il livello di coerenza predefinito

Nei client è possibile impostare un livello di coerenza diverso dall'impostazione predefinita del servizio. L'impostazione può applicarsi all'intero client o alle singole richieste.

### <a id="override-default-consistency-dotnet"></a>.NET SDK

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
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
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Utilizzare i token di sessione

Se si desidera gestire manualmente i token di sessione, è possibile ottenerli dalle risposte e impostarli per ogni richiesta. Se non si ha necessità di gestire manualmente i token di sessione, non è necessario usare gli esempi seguenti. Se non si imposta il token di sessione manualmente, l'SDK terrà automaticamente traccia dei token di sessione e userà il token più recente.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
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

Per visualizzare la metrica PBS, passare all'account Cosmos DB nel portale di Azure e quindi aprire il riquadro **Metriche**. In questo riquadro scegliere la scheda **Coerenza** ed esaminare il grafico denominato "**Probabilità di letture con coerenza assoluta in base al carico di lavoro (vedere PBS)**".

![Immagine del grafico PBS nel portale di Azure](./media/how-to-manage-consistency/pbs-metric.png)

Per visualizzare questa metrica, usare il menu delle metriche di Cosmos DB. Non viene visualizzata nell'esperienza delle metriche di Monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione dei conflitti di dati o per passare al concetto chiave successivo di Cosmos DB, vedere la documentazione seguente:

* [Gestire i conflitti tra le aree](how-to-manage-conflicts.md)
* [Partizionamento e distribuzione dei dati](partition-data.md)
