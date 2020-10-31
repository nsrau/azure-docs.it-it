---
title: Clausola GROUP BY in Azure Cosmos DB
description: Informazioni sulla clausola GROUP BY per Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: 48f272ea9005d8eb24f639b4039a84163441cffa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101443"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Clausola GROUP BY in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La clausola GROUP BY consente di dividere i risultati della query in base ai valori di una o più proprietà specificate.

## <a name="syntax"></a>Sintassi

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argomenti

- `<scalar_expression_list>`

   Specifica le espressioni che verranno usate per dividere i risultati della query.

- `<scalar_expression>`
  
   È consentita qualsiasi espressione scalare ad eccezione di sottoquery scalari e aggregazioni scalari. Ogni espressione scalare deve includere almeno un riferimento a proprietà. Non è previsto alcun limite al numero di singole espressioni o alla cardinalità di ogni espressione.

## <a name="remarks"></a>Osservazioni
  
  Quando una query usa una clausola GROUP BY, la clausola SELECT può contenere solo il subset di proprietà e funzioni di sistema incluse nella clausola GROUP BY. Un'eccezione è costituita dalle [funzioni di sistema di aggregazione](sql-query-aggregates.md), che possono essere visualizzate nella clausola SELECT senza essere incluse nella clausola GROUP BY. Nella clausola SELECT è inoltre possibile includere sempre valori letterali.

  La clausola GROUP BY deve seguire la clausola SELECT, FROM e WHERE e precedere la clausola OFFSET LIMIT. Al momento non è possibile usare GROUP BY con una clausola ORDER BY, ma è previsto in futuro.

  Con la clausola GROUP BY non sono consentiti gli elementi seguenti:
  
- Proprietà di aliasing o funzioni di sistema di aliasing (l'aliasing è comunque consentito nella clausola SELECT)
- Sottoquery
- Funzioni di sistema di aggregazione (sono consentite solo nella clausola SELECT)

Le query con una funzione di sistema di aggregazione e una sottoquery con `GROUP BY` non sono supportate. Ad esempio, la query seguente non è supportata:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Esempi

In questi esempi viene usato il set di dati delle informazioni nutrizionali disponibile tramite [Playground per le query di Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Ecco, ad esempio, una query che restituisce il numero totale di elementi in ogni foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Per limitare i risultati, si usa la parola chiave TOP. Alcuni risultati sono:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Questa query include due espressioni usate per dividere i risultati:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Alcuni risultati sono:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Questa query include una funzione di sistema nella clausola GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Alcuni risultati sono:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Questa query usa sia parole chiave che funzioni di sistema nell'espressione di proprietà dell'elemento:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

I risultati sono:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Funzioni di aggregazione](sql-query-aggregates.md)
