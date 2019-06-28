---
title: Clausola WHERE in Azure Cosmos DB
description: Informazioni sulla clausola WHERE SQL per Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342728"
---
# <a name="where-clause"></a>Clausola WHERE

La clausola WHERE facoltativa (`WHERE <filter_condition>`) specifica le condizioni che gli elementi JSON di origine devono soddisfare per la query per includerle nei risultati. Un elemento JSON deve valutare le condizioni specificate come `true` da considerare per il risultato. Il livello di indice viene utilizzata la clausola WHERE per determinare il subset più piccolo di elementi di origine che possono far parte del risultato.
  
## <a name="syntax"></a>Sintassi
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argomenti

- `<filter_condition>`  
  
   Specifica la condizione che deve essere soddisfatta per i documenti da restituire.  
  
- `<scalar_expression>`  
  
   Espressione che rappresenta il valore da calcolare. Visualizzare [espressioni scalari](sql-query-scalar-expressions.md) per informazioni dettagliate.  
  

## <a name="remarks"></a>Note
  
  Affinché il documento venga restituito, un'espressione specificata come condizione di filtro deve restituire true. Solo un valore booleano true soddisferà la condizione, i valori non definiti, null, false, numero, matrice o oggetto non soddisfano la condizione. 

## <a name="examples"></a>Esempi

La query seguente gli elementi di richieste che contengono un' `id` proprietà il cui valore `AndersenFamily`. Esclude qualsiasi elemento che non è un `id` proprietà o il cui valore non corrisponde al `AndersenFamily`.

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

### <a name="scalar-expressions-in-the-where-clause"></a>Espressioni scalari nella clausola WHERE

Nell'esempio precedente è stata illustrata una semplice query di uguaglianza. L'API SQL supporta anche vari [espressioni scalari](sql-query-scalar-expressions.md). Quelle di uso più comune sono le espressioni binarie e unarie. Anche i riferimenti di proprietà dell'oggetto JSON sono espressioni valide.

È possibile usare gli operatori binari supportati seguenti:  

|**Tipo di operatore**  | **Valori** |
|---------|---------|
|Aritmetico | +,-,*,/,% |
|Bit per bit    | \|, &, ^, <<, >>, >>> (spostamento a destra riempimento zero) |
|Logico    | AND, OR, NOT      |
|Confronto | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|string     |  \|\| (concatenazione) |

Le seguenti query di usano gli operatori binari:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

È anche possibile usare gli operatori unari +,-, ~ e non nelle query, come illustrato negli esempi seguenti:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

È anche possibile usare riferimenti di proprietà nelle query. Ad esempio, `SELECT * FROM Families f WHERE f.isRegistered` restituisce l'elemento JSON che contiene la proprietà `isRegistered` con valore uguale a `true`. Qualsiasi altro valore, come `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, o `<array>`, esclude l'elemento dal risultato. 

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Clausola FROM](sql-query-from.md)