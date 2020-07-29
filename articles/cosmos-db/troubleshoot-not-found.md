---
title: Risolvere i problemi Azure Cosmos DB eccezione non trovata
description: Come diagnosticare e correggere l'eccezione non trovata
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294389"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB non trovato
Il codice di stato HTTP 404 indica che la risorsa non esiste più.

## <a name="expected-behavior"></a>Comportamento previsto
Esistono molti scenari validi in cui un'applicazione prevede 404 e gestisce correttamente lo scenario.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Restituito non trovato per un elemento che deve esistere o esiste
Di seguito è riportato il possibile motivo per cui viene restituito un codice di stato 404 se l'elemento deve essere chiuso o.

### <a name="1-race-condition"></a>1. race condition
Sono presenti più istanze del client SDK e la lettura è stata eseguita prima della scrittura.

#### <a name="solution"></a>Soluzione:
1. La coerenza di account predefinita per Cosmos DB è la coerenza di sessione. Quando viene creato o aggiornato un elemento, la risposta restituirà un token di sessione che può essere passato tra le istanze SDK per garantire che la richiesta di lettura stia leggendo da una replica con tale modifica.
2. Modificare il [livello di coerenza](consistency-levels-choosing.md) a un [livello più](consistency-levels-tradeoffs.md) avanzato

### <a name="2-invalid-partition-key-and-id-combination"></a>2. combinazione di chiave di partizione e ID non valida
La combinazione di chiave di partizione e ID non è valida.

#### <a name="solution"></a>Soluzione:
Correzione della logica dell'applicazione che causa la combinazione non corretta. 

### <a name="3-invalid-character-in-item-id"></a>3. carattere non valido nell'ID elemento
Un elemento viene inserito in Cosmos DB con un [carattere non valido](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) nell'ID elemento.

#### <a name="solution"></a>Soluzione:
È consigliabile che gli utenti modifichino l'ID con un valore diverso che non contenga i caratteri speciali. Se la modifica dell'ID non è un'opzione, è possibile codificare l'ID in base 64 per eseguire l'escape dei caratteri speciali.

Elementi già inseriti nel contenitore l'ID può essere sostituito usando i valori RID anziché i riferimenti basati sul nome.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. ripulitura durata (TTL)
Per l'elemento è stata impostata la proprietà [TTL (time to Live)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) . L'elemento è stato eliminato perché la durata (TTL) era scaduta.

#### <a name="solution"></a>Soluzione:
Modificare la durata (TTL) per impedire che l'elemento venga eliminato.

### <a name="5-lazy-indexing"></a>5. indicizzazione Lazy
L' [indicizzazione differita](index-policy.md#indexing-mode) non è stata rilevata.

#### <a name="solution"></a>Soluzione:
Attendere che l'indicizzazione aggiorni o modifichi i criteri di indicizzazione

### <a name="6-parent-resource-deleted"></a>6. risorsa padre eliminata
Il database e/o il contenitore in cui è presente l'elemento è stato eliminato.

#### <a name="solution"></a>Soluzione:
1. [Ripristinare](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) la risorsa padre o ricreare le risorse.
2. Crea una nuova risorsa per sostituire la risorsa eliminata

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi relativi all'uso di Azure Cosmos DB .NET SDK
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)