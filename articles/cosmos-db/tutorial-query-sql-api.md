---
title: Procedura per l'esecuzione di query con SQL in Azure Cosmos DB
description: Informazioni sull'esecuzione di query con SQL in Azure Cosmos DB
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 05/10/2017
ms.reviewer: sngun
ms.openlocfilehash: bc9835876e8b87213ddbae65e43222467e751ea3
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56241633"
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
* [Query e sintassi SQL](how-to-sql-query.md)

## <a name="prerequisites"></a>Prerequisiti

L'esercitazione presuppone la presenza di un account e di una raccolta di Azure Cosmos DB. Questi requisiti non sono disponibili? Completare la [Guida introduttiva di 5 minuti](create-mongodb-nodejs.md).

## <a name="example-query-1"></a>Query di esempio 1

Nel precedente documento della famiglia, la query SQL seguente restituisce i documenti in cui il campo ID corrisponde a `WakefieldFamily`. Poiché si tratta di un'istruzione `SELECT *`, l'output della query è il documento JSON completo:

**Query**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

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

La query successiva restituisce tutti i nomi dei figli della famiglia il cui ID corrisponde a `WakefieldFamily`, ordinati in base al grado della classe frequentata.

**Query**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**Risultati**

[ { "givenName": "Jesse" }, { "givenName": "Lisa" } ]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * È stato appreso come eseguire una query usando SQL  

È ora possibile passare all'esercitazione successiva per imparare a distribuire i dati a livello globale.

> [!div class="nextstepaction"]
> [Distribuire i dati a livello globale](tutorial-global-distribution-sql-api.md)

