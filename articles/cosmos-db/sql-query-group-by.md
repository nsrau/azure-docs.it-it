---
title: Clausola GROUP BY in Azure Cosmos DB
description: Informazioni sulla clausola GROUP BY per Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: d92e24836a0eb5757de9bbdb516be290456deb7f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333261"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Clausola GROUP BY in Azure Cosmos DB

La clausola GROUP BY divide i risultati della query in base ai valori di una o più proprietà specificate.

> [!NOTE]
> Azure Cosmos DB supporta attualmente GROUP BY in [.NET SDK 3,3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.3.0) o versione successiva.
> Il supporto per altri SDK del linguaggio e il portale di Azure non è attualmente disponibile ma è pianificato.

## <a name="syntax"></a>Sintassi

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argomenti

- `<scalar_expression_list>`

   Specifica le espressioni che verranno utilizzate per dividere i risultati della query.

- `<scalar_expression>`
  
   Qualsiasi espressione scalare è consentita ad eccezione di sottoquery scalari e aggregazioni scalari. Ogni espressione scalare deve contenere almeno un riferimento a una proprietà. Non esiste alcun limite al numero di singole espressioni o alla cardinalità di ogni espressione.

## <a name="remarks"></a>Osservazioni
  
  Quando in una query viene utilizzata una clausola GROUP BY, la clausola SELECT può contenere solo il subset di proprietà e le funzioni di sistema incluse nella clausola GROUP BY. Un'eccezione è rappresentata dalle [funzioni di sistema aggregate](sql-query-aggregates.md), che possono essere visualizzate nella clausola SELECT senza essere incluse nella clausola Group by. Nella clausola SELECT è inoltre possibile includere sempre valori letterali.

  La clausola GROUP BY deve essere successiva alla clausola SELECT, FROM e WHERE e prima della clausola OFFSET limite. Attualmente non è possibile utilizzare GROUP BY con una clausola ORDER BY, ma questo è pianificato.

  La clausola GROUP BY non consente le operazioni seguenti:
  
- Proprietà di aliasing o funzioni di sistema di alias (l'aliasing è ancora consentito nella clausola SELECT)
- Sottoquery
- Funzioni di sistema aggregate (sono consentite solo nella clausola SELECT)

## <a name="examples"></a>esempi

In questi esempi viene usato il set di dati nutrizionale disponibile tramite il [Playground per le query Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Ad esempio, ecco una query che restituisce il numero totale di elementi in ogni foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Alcuni risultati sono (la parola chiave TOP viene usata per limitare i risultati):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Questa query include due espressioni utilizzate per dividere i risultati:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Alcuni risultati sono:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Questa query include una funzione di sistema nella clausola GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Alcuni risultati sono:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Questa query usa le parole chiave e le funzioni di sistema nell'espressione di proprietà Item:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

I risultati sono:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Funzioni di aggregazione](sql-query-aggregates.md)
