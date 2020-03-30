---
title: Utilizzo di matrici e oggetti in Azure Cosmos DB
description: Informazioni sulla sintassi SQL per creare matrici e oggetti in Azure Cosmos DB. In questo articolo vengono inoltre forniti alcuni esempi per eseguire operazioni sugli oggetti di matrice
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246552"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Utilizzo di matrici e oggetti in Azure Cosmos DB

Una funzionalità chiave dell'API SQL del database Cosmos di Azure è la creazione di matrici e oggetti.

## <a name="arrays"></a>Matrici

È possibile costruire matrici, come illustrato nell'esempio seguente:You can construct arrays, as shown in the following example:

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

È inoltre possibile utilizzare [l'espressione ARRAY](sql-query-subquery.md#array-expression) per costruire una matrice dai risultati [della sottoquery.](sql-query-subquery.md) Questa query ottiene tutti i nomi distinti di elementi figlio in una matrice.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Iterazione

L'API SQL fornisce il supporto per l'iterazione su matrici JSON, con un nuovo costrutto aggiunto tramite la [parola chiave IN](sql-query-keywords.md#in) nell'origine FROM. Nell'esempio seguente:

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

La query successiva `children` esegue `Families` l'iterazione nel contenitore. La matrice di output è diversa dalla query precedente. Questo esempio `children`suddivide , e appiattisce i risultati in un'unica matrice:  

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

È possibile filtrare ulteriormente in base a ogni singola voce della matrice, come illustrato nell'esempio seguente:

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

È inoltre possibile eseguire l'aggregazione sul risultato di un'iterazione di matrice. Ad esempio, la query seguente conta il numero di figli tra tutte le famiglie:

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