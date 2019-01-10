---
title: Informazioni su come configurare e gestire la durata (TTL) in Azure Cosmos DB
description: Informazioni su come configurare e gestire la durata (TTL) in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/14/2018
ms.author: mjbrown
ms.openlocfilehash: 681878d43422e86df3db1050da24be88881d4b2e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036291"
---
# <a name="how-to-configure-time-to-live-in-azure-cosmos-db"></a>Come configurare la durata (TTL) in Azure Cosmos DB

In Azure Cosmos DB è possibile scegliere di configurare la durata (TTL) a livello di contenitore oppure è possibile eseguirne l'override a livello di elemento dopo l'impostazione per il contenitore. È possibile configurare la durata (TTL) per un contenitore usando il portale di Azure o gli SDK specifici del linguaggio. Gli override della durata (TTL) a livello di elemento possono essere configurati usando gli SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Abilitare la durata (TTL) in un contenitore usando il portale di Azure

Usare la procedura seguente per abilitare la durata (TTL) in un contenitore senza scadenza. Abilitare questa opzione per consentire l'override della durata (TTL) a livello di elemento. È anche possibile impostare la durata (TTL) immettendo un valore diverso da zero per i secondi.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Creare un nuovo account Azure Cosmos o selezionare un account esistente.

3. Aprire il riquadro **Esplora dati**.

4. Selezionare un contenitore esistente, espanderlo e modificare i valori seguenti:

   * Aprire la finestra **Scalabilità e impostazioni**.
   * In **Impostazione** trovare **Durata (TTL)**.
   * Selezionare **Sì (nessun valore predefinito)** oppure selezionare **Sì** e impostare un valore TTL
   * È consigliabile fare clic su **Salva** per salvare le modifiche.

   ![Configurare la durata (TTL) nel portale di Azure](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Abilitare la durata (TTL) in un contenitore usando l'SDK

### <a id="dotnet-enable-noexpiry"></a>.NET SDK

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Impostare la durata (TTL) in un contenitore usando l'SDK

### <a id="dotnet-enable-withexpiry"></a>.NET SDK

Per impostare la durata (TTL) in un contenitore, è necessario specificare un numero positivo diverso da zero che indica il periodo di tempo in secondi. In base al valore TTL configurato, tutti gli elementi nel contenitore dopo l'ultima modifica del timestamp dell'elemento `_ts` vengono eliminati.

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-an-item"></a>Impostare la durata (TTL) per un elemento

Oltre a impostare una durata (TTL) predefinita per un contenitore, è possibile impostare una durata (TTL) per un elemento. L'impostazione della durata (TTL) a livello di elemento eseguirà l'override della durata (TTL) predefinita dell'elemento in tale contenitore.

* Per impostare la durata (TTL) in un elemento, è necessario specificare un numero positivo diverso da zero che indica il periodo di tempo, espresso in secondi, per la scadenza dell'elemento dopo l'ultima modifica del timestamp dell'elemento `_ts`.

* Se l'elemento non ha un campo TTL, per impostazione predefinita all'elemento verrà applicata la durata (TTL) impostata per il contenitore.

* Se la durata (TTL) è disabilitata a livello di contenitore, il campo TTL dell'elemento viene ignorato finché la durata (TTL) non viene abilitata nuovamente per il contenitore.

### <a id="dotnet-set-ttl-item"></a>.NET SDK

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
    public int? TimeToLive { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

## <a name="reset-time-to-live"></a>Reimpostare la durata (TTL)

È possibile reimpostare la durata (TTL) per un elemento eseguendo un'operazione di scrittura o aggiornamento per l'elemento. L'operazione di scrittura o aggiornamento imposterà `_ts` sull'ora corrente e la durata (TTL) per la scadenza dell'elemento inizierà nuovamente. Se si vuole modificare la durata (TTL) di un elemento, è possibile aggiornare il campo nello stesso modo in cui si aggiornano tutti gli altri campi.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="turn-off-time-to-live"></a>Disattivare la durata (TTL)

Se è stata impostata la durata (TTL) per un elemento e non si vuole più una scadenza per tale elemento, è possibile recuperare l'elemento, rimuovere il campo TTL e sostituire l'elemento nel server. Quando il campo TTL viene rimosso dall'elemento, all'elemento verrà applicato il valore TTL predefinito assegnato al contenitore. Impostare il valore TTL su -1 per impedire a un elemento di scadere e non ereditare il valore TTL dal contenitore.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Disabilitare la durata (TTL)

Per disabilitare la durata (TTL) in un contenitore e impedire al processo in background di controllare gli elementi scaduti, è necessario eliminare la proprietà `DefaultTimeToLive` per il contenitore. Eliminare questa proprietà non equivale a impostarla su -1. Quando si imposta il valore su -1, i nuovi elementi aggiunti al contenitore non hanno scadenza, tuttavia è possibile eseguire l'override di questo valore per elementi specifici nel contenitore. Quando si rimuove la proprietà TTL dal contenitore, gli elementi non avranno scadenza anche se sono presenti elementi con override esplicito di un valore TTL predefinito precedente.

### <a id="dotnet-disable-ttl"></a>.NET SDK

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla durata (TTL) nell'articolo seguente:

* [Durata (TTL)](time-to-live.md)
