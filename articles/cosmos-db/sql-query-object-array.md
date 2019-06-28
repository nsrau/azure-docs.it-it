---
title: Uso di matrici e oggetti in Azure Cosmos DB
description: Informazioni su creazione matrice e oggetto sintassi SQL per Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342691"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Uso di matrici e oggetti in Azure Cosmos DB

Una funzionalità chiave dell'API SQL di Azure Cosmos DB è la creazione di matrice e oggetto.

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

È anche possibile usare il [espressione di matrice](sql-query-subquery.md#array-expression) per costruire una matrice dai [sottoquery](sql-query-subquery.md) risultati. Questa query recupera tutti i nomi specificati distinti di elementi figlio in una matrice.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iterazione

L'API SQL offre il supporto per eseguire l'iterazione nelle matrici JSON, con un nuovo costrutto di aggiunto tramite il [parola chiave](sql-query-keywords.md#in) nell'origine FROM. Nell'esempio seguente:

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

La query seguente esegue l'iterazione su `children` nella `Families` contenitore. Matrice di output è diversa dalla query precedente. Questo esempio suddivide `children`e converte i risultati in una matrice:  

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

È possibile filtrare ulteriormente ciascuna voce individuale della matrice, come illustrato nell'esempio seguente:

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

È anche possibile aggregare il risultato di un'iterazione della matrice. Ad esempio, la query seguente conta il numero di elementi figlio tutte le famiglie:

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
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Join](sql-query-join.md)