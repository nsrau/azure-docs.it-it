---
title: Clausola WHERE in Azure Cosmos DB
description: Informazioni sulla clausola WHERE SQL per Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898757"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Clausola WHERE in Azure Cosmos DB

La clausola WHERE facoltativa`WHERE <filter_condition>`() specifica le condizioni che devono essere soddisfatte dagli elementi JSON di origine affinché la query li includa nei risultati. Un elemento JSON deve valutare le condizioni `true` specificate per essere considerato per il risultato. Il livello di indice utilizza la clausola WHERE per determinare il subset più piccolo di elementi di origine che può far parte del risultato.
  
## <a name="syntax"></a>Sintassi
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argomenti

- `<filter_condition>`  
  
   Specifica la condizione che deve essere soddisfatta per i documenti da restituire.  
  
- `<scalar_expression>`  
  
   Espressione che rappresenta il valore da calcolare. Per informazioni dettagliate, vedere [espressioni scalari](sql-query-scalar-expressions.md) .  
  
## <a name="remarks"></a>Osservazioni
  
  Affinché il documento venga restituito, un'espressione specificata come condizione di filtro deve restituire true. Solo il valore `true` booleano soddisferà la condizione, qualsiasi altro valore: undefined, null, false, Number, array o Object non soddisferà la condizione.

  Se si include la chiave di partizione nella `WHERE` clausola come parte di un filtro di uguaglianza, la query verrà automaticamente filtrata solo per le partizioni pertinenti.

## <a name="examples"></a>Esempi

La query seguente richiede elementi che contengono una `id` proprietà il cui valore `AndersenFamily`è. Esclude tutti gli elementi che non dispongono di una `id` proprietà o il cui valore non corrisponde `AndersenFamily`.

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

Nell'esempio precedente è stata illustrata una semplice query di uguaglianza. L'API SQL supporta inoltre varie [espressioni scalari](sql-query-scalar-expressions.md). Quelle di uso più comune sono le espressioni binarie e unarie. Anche i riferimenti di proprietà dell'oggetto JSON sono espressioni valide.

È possibile utilizzare gli operatori binari supportati seguenti:  

|**Tipo di operatore**  | **Valori** |
|---------|---------|
|Aritmetico | +,-,*,/,% |
|Bit per bit    | \|, &, ^, <<, >>, >>> (spostamento a destra riempimento zero) |
|Logico    | AND, OR, NOT      |
|Confronto | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Stringa     |  \|\| (concatenazione) |

Nelle query seguenti vengono utilizzati operatori binari:

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

È anche possibile usare i riferimenti alle proprietà nelle query. Ad esempio, `SELECT * FROM Families f WHERE f.isRegistered` restituisce l'elemento JSON che contiene la `isRegistered` proprietà con valore uguale `true`a. Qualsiasi altro valore, ad esempio `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`o `<array>`, esclude l'elemento dal risultato.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva](sql-query-getting-started.md)
- [Parola chiave IN](sql-query-keywords.md#in)
- [Clausola FROM](sql-query-from.md)