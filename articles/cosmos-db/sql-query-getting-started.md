---
title: Introduzione alle query SQL in Azure Cosmos DB
description: Introduzione alle query SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 5537133b31bb63c9fa6ac3a52b344f7f1d9c4c8a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614349"
---
# <a name="getting-started-with-sql-queries"></a>Introduzione alle query SQL

Azure Cosmos DB gli account API SQL supportano l'esecuzione di query sugli elementi usando Structured Query Language (SQL) come linguaggio di query JSON. Gli obiettivi di progettazione del linguaggio di query Azure Cosmos DB sono i seguenti:

* Supporta SQL, uno dei linguaggi di query più noti e più diffusi, anziché inventare un nuovo linguaggio di query. SQL fornisce un modello di programmazione formale per le query complesse sugli elementi JSON.  

* Usare il modello di programmazione di JavaScript come base per il linguaggio di query. Il sistema di tipi, la valutazione delle espressioni e la chiamata di funzioni di JavaScript sono le radici dell'API SQL. Queste radici forniscono un modello di programmazione naturale per le funzionalità come le proiezioni relazionali, la navigazione gerarchica in elementi JSON, self-join, query spaziali e la chiamata di funzioni definite dall'utente (UDF) scritte interamente in JavaScript.

## <a name="upload-sample-data"></a>Carica dati di esempio

Nell'account di Cosmos DB dell'API SQL creare un contenitore denominato `Families`. Creare due semplici elementi JSON nel contenitore. È possibile eseguire la maggior parte delle query di esempio nell'Azure Cosmos DB eseguire query docs utilizzando questo set di dati.

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

Il secondo elemento usa `givenName` e `familyName` invece di `firstName` e `lastName`.

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

La query seguente restituisce gli elementi in cui `id` il campo `AndersenFamily`corrisponde a. Poiché si tratta di `SELECT *` una query, l'output della query è l'elemento JSON completo. Per ulteriori informazioni sulla sintassi SELECT, vedere [istruzione SELECT](sql-query-select.md). 

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

La query seguente riformatta l'output JSON in una forma diversa. La query proietta un nuovo oggetto `Family` JSON con due `Name` campi selezionati e `City`, quando la città degli indirizzi corrisponde allo stato. "NY, NY" corrisponde a questo caso.

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

La query seguente restituisce tutti i nomi di elementi figlio specificati nella famiglia le `id` cui `WakefieldFamily`corrispondenze, ordinate per città.

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

## <a name="remarks"></a>Note

Negli esempi precedenti vengono illustrati diversi aspetti del linguaggio di query di Cosmos DB:  

* Poiché l'API SQL funziona sui valori JSON, gestisce le entità a forma di albero invece di righe e colonne. È possibile fare riferimento ai nodi della struttura ad albero in qualsiasi profondità arbitraria, ad esempio `Node1.Node2.Node3…..Nodem`, in modo simile al riferimento in due parti di `<table>.<column>` in ANSI SQL.

* Poiché il linguaggio di query funziona con dati senza schema, è necessario associare il sistema di tipi dinamicamente. La stessa espressione potrebbe produrre tipi differenti per elementi differenti. Il risultato di una query è un valore JSON valido, ma non è garantito che sia di uno schema fisso.  

* Cosmos DB supporta solo gli elementi JSON completi. Il sistema di tipi e le espressioni sono limitati a gestire solo i tipi JSON. Per ulteriori informazioni, vedere la [specifica JSON](https://www.json.org/).  

* Un contenitore Cosmos è una raccolta senza schema di elementi JSON. Le relazioni all'interno e tra gli elementi contenitore vengono acquisite in modo implicito dal contenimento, non dalle relazioni di chiave primaria e chiave esterna. Questa funzionalità è importante per i join intra-Item descritti più avanti in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Azure Cosmos DB](introduction.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Clausola SELECT](sql-query-select.md)
