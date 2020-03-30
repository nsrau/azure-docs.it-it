---
title: Clausola WHERE in Azure Cosmos DB
description: Informazioni sulla clausola SQL WHERE per Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898757"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Clausola WHERE in Azure Cosmos DB

La clausola`WHERE <filter_condition>`FACOLTATIVA WHERE ( ) specifica le condizioni che gli elementi JSON di origine devono soddisfare affinché la query li includa nei risultati. Un elemento JSON deve valutare `true` le condizioni specificate da considerare per il risultato. Il livello di indice utilizza la clausola WHERE per determinare il sottoinsieme più piccolo di elementi di origine che possono far parte del risultato.
  
## <a name="syntax"></a>Sintassi
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argomenti

- `<filter_condition>`  
  
   Specifica la condizione che deve essere soddisfatta per i documenti da restituire.  
  
- `<scalar_expression>`  
  
   Espressione che rappresenta il valore da calcolare. Per informazioni dettagliate, vedere [Espressioni scalari.](sql-query-scalar-expressions.md)  
  
## <a name="remarks"></a>Osservazioni
  
  Affinché il documento venga restituito, un'espressione specificata come condizione di filtro deve restituire true. Solo il `true` valore booleano soddisferà la condizione, qualsiasi altro valore: undefined, null, false, Number, Array o Object non soddisferà la condizione.

  Se si include la `WHERE` chiave di partizione nella clausola come parte di un filtro di uguaglianza, la query verrà automaticamente filtrata solo per le partizioni pertinenti.

## <a name="examples"></a>Esempi

La query seguente richiede `id` gli elementi `AndersenFamily`che contengono una proprietà il cui valore è . Esclude qualsiasi elemento che non `id` dispone di una proprietà `AndersenFamily`o il cui valore non corrisponde a .

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

Nell'esempio precedente è stata illustrata una semplice query di uguaglianza. L'API SQL supporta inoltre varie [espressioni scalari.](sql-query-scalar-expressions.md) Quelle di uso più comune sono le espressioni binarie e unarie. Anche i riferimenti di proprietà dell'oggetto JSON sono espressioni valide.

È possibile utilizzare i seguenti operatori binari supportati:  

|**Tipo di operatore**  | **Valori** |
|---------|---------|
|Aritmetico | +,-,*,/,% |
|Bit per bit    | \|, &, ^, <<, >>, >>> (spostamento a destra riempimento zero) |
|Logico    | AND, OR, NOT      |
|Confronto | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|string     |  \|\| (concatenazione) |

Le query seguenti utilizzano operatori binari:

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

Nelle query è inoltre possibile utilizzare gli operatori unario , ,-, , e NOT nelle query, come illustrato negli esempi seguenti:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

È inoltre possibile utilizzare i riferimenti alle proprietà nelle query. Ad esempio, `SELECT * FROM Families f WHERE f.isRegistered` restituisce l'elemento JSON contenente la proprietà `isRegistered` con valore uguale a `true`. Qualsiasi altro valore, `false` `null`ad `Undefined` `<number>`esempio `<string>` `<object>`, `<array>`, , , o , esclude l'elemento dal risultato.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Parola chiave IN](sql-query-keywords.md#in)
- [Clausola FROM](sql-query-from.md)