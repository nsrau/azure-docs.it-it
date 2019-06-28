---
title: Introduzione alle query SQL in Azure Cosmos DB
description: Introduzione alle query SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342689"
---
# <a name="getting-started-with-sql-queries"></a>Introduzione alle query SQL

Gli account Azure Cosmos DB SQL API supportano l'esecuzione di query di elementi usando Structured Query Language (SQL) come linguaggio di query JSON. Gli obiettivi di progettazione del linguaggio di query di Azure Cosmos DB sono:

* Supporta SQL, uno dei linguaggi query più familiari e popolari, invece di inventare un nuovo linguaggio di query. SQL fornisce un modello di programmazione formale per le query complesse sugli elementi JSON.  

* Per il linguaggio di query, usare il modello di programmazione di JavaScript come base. Sistema di tipi di JavaScript, valutazione delle espressioni e chiamata di funzione sono le radici dell'API SQL. Queste fonti forniscono un modello di programmazione naturale per funzionalità come le proiezioni relazionali, navigazione gerarchica tra gli elementi JSON, i self join, query spaziali e la chiamata di funzioni definite dall'utente (UDF) scritte interamente in JavaScript.

## <a name="upload-sample-data"></a>Caricare dati di esempio

Nell'account di API SQL di Cosmos DB, creare un contenitore denominato `Families`. Creare due elementi JSON semplici nel contenitore. È possibile eseguire la maggior parte delle query di esempio nella documentazione di query di Azure Cosmos DB usando il set di dati.

### <a name="create-json-items"></a>Creare elementi di JSON

Il codice seguente crea due elementi JSON semplici sulle famiglie. Gli elementi JSON semplici per le famiglie Andersen e Wakefield includono padri, figli e animali domestici, indirizzo e informazioni di registrazione. Il primo elemento ha stringhe, numeri, valori booleani, matrici e proprietà annidate.


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

Il secondo elemento Usa `givenName` e `familyName` invece di `firstName` e `lastName`.

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

### <a name="query-the-json-items"></a>La query di elementi JSON

Provare alcune query sui dati JSON per comprendere alcuni aspetti chiave del linguaggio di query SQL di Azure Cosmos DB.

La query seguente restituisce gli elementi in cui il `id` campo corrispondenze `AndersenFamily`. Poiché si tratta un `SELECT *` query, l'output della query è l'elemento JSON completo. Per altre informazioni sulla sintassi SELECT, vedere [istruzione SELECT](sql-query-select.md). 

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

La query seguente riformatta l'output JSON in una forma diversa. La query proietta un nuovo file JSON `Family` oggetto con due campi selezionati `Name` e `City`, quando la città di indirizzo è quello utilizzato per lo stato. In questo caso corrisponde a "NY, NY".

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

La query seguente restituisce tutti i nomi specificati dei figli della famiglia di prodotti il cui `id` corrisponde a `WakefieldFamily`, ordinato per città.

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

Gli esempi precedenti illustrano vari aspetti del linguaggio di query di Cosmos DB:  

* Poiché l'API SQL elabora i valori JSON, deve gestire le entità a forma di struttura ad albero invece di righe e colonne. È possibile fare riferimento ai nodi dell'albero a qualsiasi profondità arbitraria, ad esempio `Node1.Node2.Node3…..Nodem`, in modo analogo al riferimento di due parti del `<table>.<column>` in SQL ANSI.

* Poiché il linguaggio di query funziona con dati senza schema, il sistema di tipi deve essere associato in modo dinamico. La stessa espressione potrebbe produrre tipi differenti per elementi differenti. Il risultato di una query è un valore JSON valido, ma non è garantito che appartenga a uno schema fisso.  

* Cosmos DB supporta solo gli elementi JSON completi. Il sistema di tipi e le espressioni sono limitate a trattare unicamente tipi JSON. Per altre informazioni, vedere la [specifica JSON](https://www.json.org/).  

* Un contenitore Cosmos DB è una raccolta senza schema degli elementi JSON. Le relazioni tra gli elementi di contenitore e all'interno vengono implicitamente acquisite dal contenimento, non dalla chiave primaria e le relazioni di chiave esterne. Questa funzionalità è importante per i join intra-elemento descritti più avanti in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Clausola SELECT](sql-query-select.md)
