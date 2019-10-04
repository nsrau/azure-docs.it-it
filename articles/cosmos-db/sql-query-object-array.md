---
title: Utilizzo di matrici e oggetti in Azure Cosmos DB
description: Informazioni sulla sintassi SQL per la creazione di oggetti e matrici per Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 17a0e4ddf5acd267a4cfbb68c218fe9409a91d57
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003930"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Utilizzo di matrici e oggetti in Azure Cosmos DB

Una funzionalità chiave dell'API di Azure Cosmos DB SQL è la creazione di matrici e oggetti.

## <a name="arrays"></a>Matrici

È possibile creare matrici, come illustrato nell'esempio seguente:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

I risultati sono:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

È anche possibile usare l' [espressione di matrice](sql-query-subquery.md#array-expression) per costruire una matrice dai risultati della [sottoquery](sql-query-subquery.md) . Questa query ottiene tutti i nomi distinti di elementi figlio in una matrice.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iterazione

L'API SQL fornisce supporto per l'iterazione su matrici JSON, con un nuovo costrutto aggiunto tramite la [parola chiave in](sql-query-keywords.md#in) nell'origine da. Nell'esempio seguente:

```sql
    SELECT *
    FROM Families.children
```

I risultati sono:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

La query successiva esegue l'iterazione `children` `Families` nel contenitore. La matrice di output è diversa dalla query precedente. Questo esempio divide `children`e rende Flat i risultati in una singola matrice:  

```sql
    SELECT *
    FROM c IN Families.children
```

I risultati sono:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

È possibile filtrare ulteriormente in ogni singola voce della matrice, come illustrato nell'esempio seguente:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

I risultati sono:

```json
    [{
      "givenName": "Lisa"
    }]
```

È anche possibile aggregare sul risultato di un'iterazione della matrice. Ad esempio, la query seguente conta il numero di elementi figlio tra tutte le famiglie:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

I risultati sono:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Join](sql-query-join.md)