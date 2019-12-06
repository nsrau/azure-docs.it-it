---
title: Operatori di query SQL per Azure Cosmos DB
description: Informazioni sugli operatori SQL come l'uguaglianza, il confronto e gli operatori logici supportati da Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870939"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatori in Azure Cosmos DB

Questo articolo illustra in dettaglio i vari operatori supportati da Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatori di uguaglianza e confronto

La tabella seguente illustra il risultato dei confronti di uguaglianza nell'API SQL tra due tipi JSON qualsiasi.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **Stringa** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **Stringa** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

Per gli operatori di confronto, ad esempio `>`, `>=`, `!=`, `<`e `<=`, il confronto tra i tipi o tra due oggetti o matrici produce `Undefined`.  

Se il risultato dell'espressione scalare è `Undefined`, l'elemento non è incluso nel risultato perché `Undefined` non è uguale `true`.

## <a name="logical-and-or-and-not-operators"></a>Operatori logici (AND, OR e NOT)

Gli operatori logici funzionano con valori booleani. Le tabelle seguenti illustrano le tabelle di verità logiche per questi operatori:

**Operator OR**

| Oppure | Vero | Falso | Undefined |
| --- | --- | --- | --- |
| Vero |Vero |Vero |Vero |
| Falso |Vero |Falso |Undefined |
| Undefined |Vero |Undefined |Undefined |

**Operatore AND**

| AND | Vero | Falso | Undefined |
| --- | --- | --- | --- |
| Vero |Vero |Falso |Undefined |
| Falso |Falso |Falso |Falso |
| Undefined |Undefined |Falso |Undefined |

**Operatore NOT**

| NOT |  |
| --- | --- |
| Vero |Falso |
| Falso |Vero |
| Undefined |Undefined |


## <a name="-operator"></a>* (operatore)

L'operatore speciale * proietta l'intero elemento così com'è. Quando usato, deve essere l'unico campo proiettato. Una query come `SELECT * FROM Families f` è valida, ma `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` non sono valide.

## <a name="-and--operators"></a>? e? operatori

È possibile usare gli operatori ternari (?) e COALESCE (??) per compilare espressioni condizionali, come nei linguaggi di C# programmazione come e JavaScript. 

È possibile usare? operatore per costruire nuove proprietà JSON in tempo reale. Ad esempio, la query seguente classifica i livelli di grado in `elementary` o `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

È anche possibile annidare le chiamate a? , come nella query seguente: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Come per gli altri operatori di query, il? l'operatore esclude gli elementi se le proprietà a cui si fa riferimento sono mancanti o i tipi confrontati sono diversi.

Usare?? operatore per verificare in modo efficiente una proprietà in un elemento quando si eseguono query su dati semistrutturati o di tipo misto. Ad esempio, la query seguente restituisce `lastName` se presente oppure `surname` se `lastName` non è presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Parole chiave](sql-query-keywords.md)
- [Clausola SELECT](sql-query-select.md)
