---
title: Clausola GROUP BY in Azure Cosmos DB
description: Informazioni sulla clausola GROUP BY per Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 8a3cbbafc066747b62f79934f2cd12301aa1ba17
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261602"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Clausola GROUP BY in Azure Cosmos DB

La clausola GROUP BY divide i risultati della query in base ai valori di una o più proprietà specificate.

> [!NOTE]
> Azure Cosmos DB supporta attualmente GROUP BY in .NET SDK 3.3 e versioni successive, nonché JavaScript SDK 3.4 e versioni successive.
> Il supporto per altri SDK di lingua non è attualmente disponibile, ma è pianificato.

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
  
   È consentita qualsiasi espressione scalare, ad eccezione delle sottoquery scalari e delle aggregazioni scalari. Ogni espressione scalare deve contenere almeno un riferimento di proprietà. Non esiste alcun limite al numero di singole espressioni o alla cardinalità di ogni espressione.

## <a name="remarks"></a>Osservazioni
  
  Quando una query utilizza una clausola GROUP BY, la clausola SELECT può contenere solo il sottoinsieme di proprietà e funzioni di sistema incluse nella clausola GROUP BY. Un'eccezione è [l'aggregazione](sql-query-aggregates.md)delle funzioni di sistema , che possono essere visualizzate nella clausola SELECT senza essere incluse nella clausola GROUP BY. È inoltre possibile includere sempre valori letterali nella clausola SELECT.

  La clausola GROUP BY deve trovarsi dopo la clausola SELECT, FROM e WHERE e prima della clausola OFFSET LIMIT. Al momento non è possibile utilizzare GROUP BY con una clausola ORDER BY, ma questa operazione è stata pianificata.

  La clausola GROUP BY non consente alcuna delle seguenti operazioni:
  
- Proprietà aliaso o funzioni di sistema di alias (alias è ancora consentito all'interno della clausola SELECT)
- Sottoquery:
- Funzioni di sistema di aggregazione (consentite solo nella clausola SELECT)

Le query con una funzione di `GROUP BY` sistema di aggregazione e una sottoquery con non sono supportate. Ad esempio, la query seguente non è supportata:

```sql
SELECT COUNT(UniqueLastNames) FROM (SELECT AVG(f.age) FROM f GROUP BY f.lastName) AS UniqueLastNames
```

## <a name="examples"></a>Esempi

In questi esempi viene usato il set di dati nutrizionali disponibile tramite [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

Ad esempio, ecco una query che restituisce il conteggio totale di elementi in ogni foodGroup:For example, here's a query which returns the total count of items in each foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Alcuni risultati sono (la parola chiave TOP viene utilizzata per limitare i risultati):

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

Questa query include due espressioni utilizzate per dividere i risultati:This query has two expressions used to divide results:

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

Questa query ha una funzione di sistema nella clausola GROUP BY:

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

Questa query utilizza sia parole chiave che funzioni di sistema nell'espressione di proprietà dell'elemento:This query uses both keywords and system functions in the item property expression:

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
