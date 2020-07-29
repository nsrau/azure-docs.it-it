---
title: Introduzione alle query SQL in Azure Cosmos DB
description: Informazioni su come usare le query SQL per eseguire query sui dati da Azure Cosmos DB. È possibile caricare i dati di esempio in un contenitore Azure Cosmos DB ed eseguire query su di essi.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: d292b7cfcda73cb4cd6ac2535c7e27fc675e1030
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87308186"
---
# <a name="getting-started-with-sql-queries"></a>Introduzione alle query SQL

In Azure Cosmos DB account API SQL sono disponibili due modi per leggere i dati:

**Letture di punti** : è possibile eseguire una ricerca di chiave/valore su un *ID singolo elemento* e una chiave di partizione. La combinazione di *ID elemento* e chiave di partizione è la chiave e l'elemento stesso è il valore. Per un documento da 1 KB, le letture di punti in genere costano 1 [unità richiesta](request-units.md) con una latenza inferiore a 10 ms. Le letture Point restituiscono un singolo elemento.

**Query SQL** : è possibile eseguire query sui dati scrivendo query usando il Structured Query Language (SQL) come linguaggio di query JSON. Le query costano sempre almeno 2,3 unità richiesta e, in generale, avranno una latenza più elevata e variabile rispetto alle letture del punto. Le query possono restituire molti elementi.

Per la maggior parte dei carichi di lavoro con utilizzo intensivo di lettura su Azure Cosmos DB viene utilizzata una combinazione di letture di punti e query SQL. Se è sufficiente leggere un singolo elemento, le letture di punti sono più convenienti e più veloci rispetto alle query. Le letture di punti non devono usare il motore di query per accedere ai dati e possono leggere direttamente i dati. Naturalmente, non è possibile che tutti i carichi di lavoro leggano esclusivamente i dati usando le letture di punti, pertanto il supporto di SQL come linguaggio di query e [indicizzazione indipendente dallo schema](index-overview.md) fornisce un modo più flessibile per accedere ai dati.

Di seguito sono riportati alcuni esempi di come eseguire le letture dei punti con ogni SDK:

- [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet)
- [SDK per Java](https://docs.microsoft.com/java/api/com.azure.cosmos.cosmoscontainer.readitem?view=azure-java-stable#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](https://docs.microsoft.com/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-)
- [Python SDK](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.containerproxy?view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

Il resto di questo documento illustra come iniziare a scrivere query SQL in Azure Cosmos DB. Le query SQL possono essere eseguite tramite SDK o portale di Azure.

## <a name="upload-sample-data"></a>Carica dati di esempio

Nell'account di Cosmos DB dell'API SQL creare un contenitore denominato `Families` . Creare due semplici elementi JSON nel contenitore. È possibile eseguire la maggior parte delle query di esempio nella documentazione di Azure Cosmos DB query utilizzando questo set di dati.

### <a name="create-json-items"></a>Crea elementi JSON

Il codice seguente crea due semplici elementi JSON sulle famiglie. Gli elementi JSON semplici per le famiglie Andersen e Wakefield includono i genitori, i figli e i relativi animali, l'indirizzo e le informazioni di registrazione. Il primo elemento contiene stringhe, numeri, valori booleani, matrici e proprietà annidate.

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Il secondo elemento usa `givenName` e `familyName` invece di `firstName` e `lastName` .

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Eseguire query sugli elementi JSON

Provare alcune query sui dati JSON per comprendere alcuni aspetti chiave del linguaggio di query SQL di Azure Cosmos DB.

La query seguente restituisce gli elementi in cui il `id` campo corrisponde a `AndersenFamily` . Poiché si tratta di una `SELECT *` query, l'output della query è l'elemento JSON completo. Per ulteriori informazioni sulla sintassi SELECT, vedere [istruzione SELECT](sql-query-select.md).

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati della query sono:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

La query seguente riformatta l'output JSON in una forma diversa. La query proietta un nuovo `Family` oggetto JSON con due campi selezionati `Name` e `City` , quando la città degli indirizzi corrisponde allo stato. "NY, NY" corrisponde a questo caso.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

I risultati della query sono:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

La query seguente restituisce tutti i nomi di elementi figlio specificati nella famiglia le cui `id` corrispondenze `WakefieldFamily` , ordinate per città.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

I risultati sono:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Osservazioni

Negli esempi precedenti vengono illustrati diversi aspetti del linguaggio di query di Cosmos DB:  

* Poiché l'API SQL funziona sui valori JSON, gestisce le entità a forma di albero invece di righe e colonne. È possibile fare riferimento ai nodi della struttura ad albero in qualsiasi profondità arbitraria, ad esempio `Node1.Node2.Node3…..Nodem` , in modo simile al riferimento in due parti di `<table>.<column>` in ANSI SQL.

* Poiché il linguaggio di query funziona con dati senza schema, è necessario associare il sistema di tipi dinamicamente. La stessa espressione potrebbe produrre tipi differenti per elementi differenti. Il risultato di una query è un valore JSON valido, ma non è garantito che sia di uno schema fisso.  

* Cosmos DB supporta solo gli elementi JSON completi. Il sistema di tipi e le espressioni sono quindi limitati all'interazione esclusiva con i tipi JSON. Per altre informazioni, vedere le [specifiche JSON](https://www.json.org/).  

* Un contenitore Cosmos è una raccolta senza schema di elementi JSON. Le relazioni all'interno e tra gli elementi contenitore vengono acquisite in modo implicito dal contenimento, non dalle relazioni di chiave primaria e chiave esterna. Questa funzionalità è importante per i join intra-Item descritti più avanti in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clausola SELECT](sql-query-select.md)
