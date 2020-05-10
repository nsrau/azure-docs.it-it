---
title: Clausola SELECT in Azure Cosmos DB
description: Informazioni sulla clausola SQL SELECT per Azure Cosmos DB. Usare SQL come linguaggio di query JSON Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: f33cf20b76655a893fe7eebd9e6e6569d35de98f
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005945"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Clausola SELECT in Azure Cosmos DB

Ogni query è costituita `SELECT` da una clausola e dalle clausole [from](sql-query-from.md) e [where](sql-query-where.md) facoltative per gli standard SQL ANSI. In genere, l'origine nella `FROM` clausola è enumerata e la `WHERE` clausola applica un filtro sull'origine per recuperare un subset di elementi JSON. La `SELECT` clausola proietta quindi i valori JSON richiesti nell'elenco di selezione.

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

## <a name="remarks"></a>Osservazioni

La sintassi di `SELECT *` è valida solo se la clausola FROM ha dichiarato esattamente un alias. `SELECT *` offre una proiezione dell'identità, che può essere utile se non è necessaria alcuna proiezione. SELECT * è valida solo se viene specificata la clausola FROM e se viene introdotta solo una singola origine di input.  
  
`SELECT <select_list>` e `SELECT *` sono "zucchero sintattico" e possono essere espressi in alternativa usando semplici istruzioni SELECT, come illustrato di seguito.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   Equivale a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   Equivale a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Esempi

L'esempio di query SELECT seguente `address` restituisce `Families` da `id` le `AndersenFamily`cui corrispondenze:

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
