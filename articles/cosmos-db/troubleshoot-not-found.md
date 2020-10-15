---
title: Risolvere i problemi Azure Cosmos DB eccezioni non trovate
description: Informazioni su come diagnosticare e correggere le eccezioni non trovate.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f32a37d5d08e8b20e59455393c70e4e4d288eb11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802397"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB eccezioni non trovate
Il codice di stato HTTP 404 indica che la risorsa non esiste più.

## <a name="expected-behavior"></a>Comportamento previsto
Esistono molti scenari validi in cui un'applicazione prevede un codice 404 e gestisce correttamente lo scenario.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>È stata restituita un'eccezione non trovata per un elemento che deve esistere o esiste
Ecco i possibili motivi per cui viene restituito un codice di stato 404 se l'elemento deve esistere o esiste.

### <a name="race-condition"></a>Race condition
Sono presenti più istanze del client SDK e la lettura è stata eseguita prima della scrittura.

#### <a name="solution"></a>Soluzione:
1. La coerenza di account predefinita per Azure Cosmos DB è la coerenza di sessione. Quando viene creato o aggiornato un elemento, la risposta restituisce un token di sessione che può essere passato tra le istanze SDK per garantire che la richiesta di lettura legga da una replica con tale modifica.
1. Modificare il [livello di coerenza](consistency-levels-choosing.md) a un [livello più](consistency-levels-tradeoffs.md)avanzato.

### <a name="invalid-partition-key-and-id-combination"></a>Combinazione di chiave di partizione e ID non valida
La combinazione di chiave di partizione e ID non è valida.

#### <a name="solution"></a>Soluzione:
Correzione della logica dell'applicazione che causa la combinazione non corretta. 

### <a name="invalid-character-in-an-item-id"></a>Carattere non valido in un ID elemento
Un elemento viene inserito in Azure Cosmos DB con un [carattere non valido](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks) nell'ID elemento.

#### <a name="solution"></a>Soluzione:
Modificare l'ID impostando un valore diverso che non contiene i caratteri speciali. Se la modifica dell'ID non è un'opzione, è possibile codificare l'ID in base 64 per eseguire l'escape dei caratteri speciali.

Gli elementi già inseriti nel contenitore per l'ID possono essere sostituiti usando valori RID anziché riferimenti basati sul nome.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Tempo di ripulitura in tempo reale
Per l'elemento è stata impostata la proprietà [TTL (time to Live)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) . L'elemento è stato eliminato perché la proprietà TTL è scaduta.

#### <a name="solution"></a>Soluzione:
Modificare la proprietà TTL per impedire che l'elemento venga eliminato.

### <a name="lazy-indexing"></a>Indicizzazione differita
L' [indicizzazione differita](index-policy.md#indexing-mode) non è stata rilevata.

#### <a name="solution"></a>Soluzione:
Attendere che l'indicizzazione aggiorni o modifichi i criteri di indicizzazione.

### <a name="parent-resource-deleted"></a>Risorsa padre eliminata
Il database o il contenitore in cui è presente l'elemento è stato eliminato.

#### <a name="solution"></a>Soluzione:
1. [Ripristinare](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) la risorsa padre oppure ricreare le risorse.
1. Creare una nuova risorsa per sostituire la risorsa eliminata.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. i nomi di contenitore/raccolta fanno distinzione tra maiuscole e minuscole
I nomi di contenitore/raccolta sono case-sesnsitive in Cosmos DB.

#### <a name="solution"></a>Soluzione:
Assicurarsi di usare il nome esatto durante la connessione al Cosmos DB.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).