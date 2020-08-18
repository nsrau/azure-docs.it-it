---
title: "Esercitazione: Procedura per l'esecuzione di query con SQL in Azure Cosmos DB"
description: 'Esercitazione: Informazioni su come eseguire query SQL in Azure Cosmos DB usando il playground per le query'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 2a6033ef1d2b7dda04b1510d42fa49141e0b79b4
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135998"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Esercitazione: Eseguire query in Azure Cosmos DB con l'API SQL

L'[API SQL](documentdb-introduction.md) di Azure Cosmos DB supporta l'esecuzione di query sui documenti usando SQL. Questo articolo include un documento di esempio e due esempi di query SQL e relativi risultati.

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Esecuzione di query sui dati con SQL

## <a name="sample-document"></a>Documento di esempio

Le query SQL di questo articolo usano il documento di esempio seguente.

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
        "gender": "female", "grade": 1,
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

## <a name="where-can-i-run-sql-queries"></a>Dove è possibile eseguire query SQL?

È possibile eseguire query usando Esplora dati nel Portale di Azure, tramite le [API REST e gli SDK](sql-api-sdk-dotnet.md) e anche usando [Query Playground](https://www.documentdb.com/sql/demo), che esegue query su un set di dati di esempio esistente.

Per altre informazioni sulle query SQL, vedere:
* [Query e sintassi SQL](sql-query-getting-started.md)

## <a name="prerequisites"></a>Prerequisiti

L'esercitazione presuppone la presenza di un account e di una raccolta di Azure Cosmos DB. Queste risorse non sono disponibili? Completare la [Guida introduttiva di 5 minuti](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>Query di esempio 1

Nel precedente documento della famiglia, la query SQL seguente restituisce i documenti in cui il campo ID corrisponde a `WakefieldFamily`. Poiché si tratta di un'istruzione `SELECT *`, l'output della query è il documento JSON completo:

**Query**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**Risultati**

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
        "gender": "female", "grade": 1,
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

## <a name="example-query-2"></a>Query di esempio 2

La query successiva restituisce tutti i nomi specificati dei figli della famiglia il cui ID corrisponde a `WakefieldFamily`.

**Query**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**Risultati**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione verranno effettuate le attività seguenti:

> [!div class="checklist"]
> * È stato appreso come eseguire una query usando SQL  

È ora possibile passare all'esercitazione successiva per imparare a distribuire i dati a livello globale.

> [!div class="nextstepaction"]
> [Distribuire i dati a livello globale](tutorial-global-distribution-sql-api.md)

