---
title: Introduzione alle query SQL nel database Cosmos di AzureGetting started with SQL queries in Azure Cosmos DB
description: Informazioni su come usare le query SQL per eseguire query sui dati da Database Cosmos di Azure.Learn how to use SQL queries to query data from Azure Cosmos DB. È possibile caricare dati di esempio in un contenitore in Azure Cosmos DB ed eseguire query su di esso.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873336"
---
# <a name="getting-started-with-sql-queries"></a>Introduzione alle query SQL

Gli account API SQL del database di Azure Cosmos supportano l'esecuzione di query sugli elementi tramite SQL (Structured Query Language) come linguaggio di query JSON. Gli obiettivi di progettazione del linguaggio di query di Azure Cosmos DB sono:The design goals of the Azure Cosmos DB query language are to:

* Supporta SQL, uno dei linguaggi di query più familiari e popolari, invece di inventare un nuovo linguaggio di query. SQL fornisce un modello di programmazione formale per le query avanzate sugli elementi JSON.  

* Usa il modello di programmazione di JavaScript come base per il linguaggio di query. Il sistema di tipi di JavaScript, la valutazione delle espressioni e la chiamata di funzione sono le radici dell'API SQL. Queste radici forniscono un modello di programmazione naturale per funzionalità quali proiezioni relazionali, navigazione gerarchica tra elementi JSON, self-join, query spaziali e chiamata di funzioni definite dall'utente (UDF) scritte interamente in JavaScript.

## <a name="upload-sample-data"></a>Caricare dati di esempio

Nell'account Cosmos DB dell'API `Families`SQL creare un contenitore denominato . Creare due elementi JSON semplici nel contenitore. È possibile eseguire la maggior parte delle query di esempio nei documenti di query di Azure Cosmos DB usando questo set di dati.

### <a name="create-json-items"></a>Creare elementi JSONCreate JSON items

Il codice seguente crea due semplici elementi JSON sulle famiglie. I semplici elementi JSON per le famiglie Andersen e Wakefield includono i genitori, i figli e i relativi animali domestici, l'indirizzo e le informazioni di registrazione. Il primo elemento contiene stringhe, numeri, valori booleani, matrici e proprietà annidate.


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

Il secondo `givenName` elemento `familyName` utilizza `firstName` `lastName`e invece di e .

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

### <a name="query-the-json-items"></a>Eseguire una query sugli elementi JSONQuery the JSON items

Provare alcune query sui dati JSON per comprendere alcuni degli aspetti chiave del linguaggio di query SQL di Azure Cosmos DB.

La query seguente restituisce `id` gli `AndersenFamily`elementi in cui il campo corrisponde a . Poiché si `SELECT *` tratta di una query, l'output della query è l'elemento JSON completo. Per ulteriori informazioni sulla sintassi SELECT, vedere [Istruzione SELECT](sql-query-select.md). 

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

La query seguente riformatta l'output JSON in una forma diversa. La query proietta `Family` un nuovo oggetto `Name` JSON `City`con due campi selezionati e , quando la città dell'indirizzo è uguale allo stato. "NY, NY" corrisponde a questo caso.

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

La query seguente restituisce tutti i nomi `id` `WakefieldFamily`di elementi figlio della famiglia le cui corrispondenze, ordinate in base alla città.

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

Gli esempi precedenti mostrano diversi aspetti del linguaggio di query Cosmos DB:  

* Poiché l'API SQL funziona su valori JSON, si occupa di entità a forma di struttura ad albero anziché di righe e colonne. È possibile fare riferimento ai nodi `Node1.Node2.Node3…..Nodem`della struttura ad albero a `<table>.<column>` qualsiasi profondità arbitraria, ad esempio , in modo simile al riferimento in due parti in SQL ANSI.

* Poiché il linguaggio di query funziona con dati senza schema, il sistema di tipi deve essere associato in modo dinamico. La stessa espressione potrebbe produrre tipi differenti per elementi differenti. Il risultato di una query è un valore JSON valido, ma non è garantito che sia di uno schema fisso.  

* Cosmos DB supporta solo gli elementi JSON completi. Il sistema di tipi e le espressioni sono limitati per gestire solo i tipi JSON. Per ulteriori informazioni, vedere la [specifica JSON](https://www.json.org/).  

* Un contenitore Cosmos è una raccolta senza schema di elementi JSON. Le relazioni all'interno e tra gli elementi contenitore vengono acquisite in modo implicito dal contenimento, non dalle relazioni di chiave primaria e di chiave esterna. Questa funzionalità è importante per i join intra-elementi illustrati più avanti in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clausola SELECT](sql-query-select.md)
