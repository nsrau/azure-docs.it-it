---
title: Clausola SELECT in Azure Cosmos DB
description: Informazioni sulla clausola SQL SELECT per Azure Cosmos DB. Usare SQL come linguaggio di query JSON Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335442"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Clausola SELECT in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ogni query è costituita da una `SELECT` clausola e dalle clausole [from](sql-query-from.md) e [where](sql-query-where.md) facoltative per gli standard SQL ANSI. In genere, l'origine nella `FROM` clausola è enumerata e la `WHERE` clausola applica un filtro sull'origine per recuperare un subset di elementi JSON. La `SELECT` clausola proietta quindi i valori JSON richiesti nell'elenco di selezione.

## <a name="syntax"></a>Sintassi

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argomenti
  
- `<select_specification>`  

  Proprietà o valore da selezionare per il set di risultati.  
  
- `'*'`  

  Specifica che il valore deve essere recuperato senza apportare alcuna modifica. In particolare, se il valore elaborato è un oggetto, tutte le proprietà verranno recuperate.  
  
- `<object_property_list>`  
  
  Specifica l'elenco di proprietà da recuperare. Ogni valore restituito sarà un oggetto con le proprietà specificate.  
  
- `VALUE`  

  Specifica che deve essere recuperato il valore JSON anziché l'oggetto JSON completo. A differenza di `<property_list>` non esegue il wrapping del valore previsto in un oggetto.  

- `DISTINCT`
  
  Specifica che i duplicati delle proprietà proiettate devono essere rimossi.  

- `<scalar_expression>`  

  Espressione che rappresenta il valore da calcolare. Vedere la sezione [Espressioni scalari](sql-query-scalar-expressions.md) per informazioni dettagliate.  

## <a name="remarks"></a>Commenti

La sintassi di `SELECT *` è valida solo se la clausola FROM ha dichiarato esattamente un alias. `SELECT *` offre una proiezione dell'identità, che può essere utile se non è necessaria alcuna proiezione. SELECT * è valida solo se viene specificata la clausola FROM e se viene introdotta solo una singola origine di input.  
  
`SELECT <select_list>` e `SELECT *` sono "zucchero sintattico" e possono essere espressi in alternativa usando semplici istruzioni SELECT, come illustrato di seguito.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   Equivale a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   Equivale a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Esempi

L'esempio di query SELECT seguente restituisce `address` da le `Families` cui `id` corrispondenze `AndersenFamily` :

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clausola WHERE](sql-query-where.md)
