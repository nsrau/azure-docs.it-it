---
title: Informazioni su come configurare e gestire la durata (TTL) in Azure Cosmos DB
description: Informazioni su come configurare e gestire la durata (TTL) in un contenitore e un elemento in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 51022956ad32059771b8d56f0ae177fb5165b450
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873608"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Configurare la durata (TTL) in Azure Cosmos DB

In Azure Cosmos DB è possibile scegliere di configurare la durata (TTL) a livello di contenitore oppure è possibile eseguirne l'override a livello di elemento dopo l'impostazione per il contenitore. È possibile configurare la durata (TTL) per un contenitore usando il portale di Azure o gli SDK specifici del linguaggio. Gli override della durata (TTL) a livello di elemento possono essere configurati usando gli SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Abilitare la durata (TTL) in un contenitore usando il portale di Azure

Usare la procedura seguente per abilitare la durata (TTL) in un contenitore senza scadenza. Abilitare questa opzione per consentire l'override della durata (TTL) a livello di elemento. È anche possibile impostare la durata (TTL) immettendo un valore diverso da zero per i secondi.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Creare un nuovo account Azure Cosmos o selezionare un account esistente.

3. Aprire il riquadro **Esplora dati**.

4. Selezionare un contenitore esistente, espanderlo e modificare i valori seguenti:

   * Aprire la finestra **Scalabilità e impostazioni**.
   * In **Impostazione** trovare **Durata (TTL)** .
   * Selezionare **Sì (nessun valore predefinito)** oppure selezionare **Sì** e impostare un valore TTL
   * È consigliabile fare clic su **Salva** per salvare le modifiche.

   ![Configurare la durata (TTL) nel portale di Azure](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* Se DefaultTimeToLive è Null, la durata TTL è disattivata
* Se DefaultTimeToLive è -1, l'impostazione della durata TTL è attivata (nessun valore predefinito)
* Se DefaultTimeToLive include qualsiasi altro valore Int, ad eccezione di 0, l'impostazione della durata TTL è attivata

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Abilitare la durata per un contenitore usando l'interfaccia della riga di comando di Azure o PowerShell

Per creare o abilitare la durata (TTL) in un contenitore, vedere.

* [Creare un contenitore con TTL usando l'interfaccia della riga di comando di Azure](manage-with-cli.md#create-a-container-with-ttl)
* [Creare un contenitore con TTL usando PowerShell](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Abilitare la durata (TTL) in un contenitore usando l'SDK

### <a id="dotnet-enable-noexpiry"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a id="dotnet-enable-noexpiry"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Impostare la durata (TTL) in un contenitore usando l'SDK

Per impostare la durata (TTL) in un contenitore, è necessario specificare un numero positivo diverso da zero che indica il periodo di tempo in secondi. In base al valore TTL configurato, tutti gli elementi nel contenitore dopo l'ultima modifica del timestamp dell'elemento `_ts` vengono eliminati.

### <a id="dotnet-enable-withexpiry"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a id="dotnet-enable-withexpiry"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Impostare la durata (TTL) per un elemento

Oltre a impostare una durata (TTL) predefinita per un contenitore, è possibile impostare una durata (TTL) per un elemento. L'impostazione della durata (TTL) a livello di elemento eseguirà l'override della durata (TTL) predefinita dell'elemento in tale contenitore.

* Per impostare la durata (TTL) in un elemento, è necessario specificare un numero positivo diverso da zero che indica il periodo di tempo, espresso in secondi, per la scadenza dell'elemento dopo l'ultima modifica del timestamp dell'elemento `_ts`.

* Se l'elemento non ha un campo TTL, per impostazione predefinita all'elemento verrà applicata la durata (TTL) impostata per il contenitore.

* Se la durata (TTL) è disabilitata a livello di contenitore, il campo TTL dell'elemento viene ignorato finché la durata (TTL) non viene abilitata nuovamente per il contenitore.

### <a id="portal-set-ttl-item"></a>Portale di Azure

Usare la procedura seguente per abilitare la durata (TTL) in un elemento:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Creare un nuovo account Azure Cosmos o selezionare un account esistente.

3. Aprire il riquadro **Esplora dati**.

4. Selezionare un contenitore esistente, espanderlo e modificare i valori seguenti:

   * Aprire la finestra **Scalabilità e impostazioni**.
   * In **Impostazione** trovare **Durata (TTL)** .
   * Selezionare **Sì (nessun valore predefinito)** oppure selezionare **Sì** e impostare un valore TTL. 
   * È consigliabile fare clic su **Salva** per salvare le modifiche.

5. Passare quindi all'elemento per cui si desidera impostare la durata, aggiungere la proprietà `ttl` e selezionare **Aggiorna**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>.NET SDK (any)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

## <a name="reset-time-to-live"></a>Reimpostare la durata (TTL)

È possibile reimpostare la durata (TTL) per un elemento eseguendo un'operazione di scrittura o aggiornamento per l'elemento. L'operazione di scrittura o aggiornamento imposterà `_ts` sull'ora corrente e la durata (TTL) per la scadenza dell'elemento inizierà nuovamente. Se si vuole modificare la durata (TTL) di un elemento, è possibile aggiornare il campo nello stesso modo in cui si aggiornano tutti gli altri campi.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-extend-ttl-item"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="turn-off-time-to-live"></a>Disattivare la durata (TTL)

Se è stata impostata la durata (TTL) per un elemento e non si vuole più una scadenza per tale elemento, è possibile recuperare l'elemento, rimuovere il campo TTL e sostituire l'elemento nel server. Quando il campo TTL viene rimosso dall'elemento, all'elemento verrà applicato il valore TTL predefinito assegnato al contenitore. Impostare il valore TTL su -1 per impedire a un elemento di scadere e non ereditare il valore TTL dal contenitore.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="disable-time-to-live"></a>Disabilitare la durata (TTL)

Per disabilitare la durata (TTL) in un contenitore e impedire al processo in background di controllare gli elementi scaduti, è necessario eliminare la proprietà `DefaultTimeToLive` per il contenitore. Eliminare questa proprietà non equivale a impostarla su -1. Quando si imposta il valore su -1, i nuovi elementi aggiunti al contenitore non hanno scadenza, tuttavia è possibile eseguire l'override di questo valore per elementi specifici nel contenitore. Quando si rimuove la proprietà TTL dal contenitore, gli elementi non avranno mai scadenza anche se sono presenti elementi con override esplicito di un valore TTL predefinito precedente.

### <a id="dotnet-disable-ttl"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a id="dotnet-disable-ttl"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla durata (TTL) nell'articolo seguente:

* [Durata (TTL)](time-to-live.md)
