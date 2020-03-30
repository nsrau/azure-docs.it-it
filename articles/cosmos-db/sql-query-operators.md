---
title: SQL query operators for Azure Cosmos DB
description: Informazioni sugli operatori SQL, ad esempio l'uguaglianza, il confronto e gli operatori logici supportati da Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063564"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatori in Azure Cosmos DBOperators in Azure Cosmos DB

Questo articolo descrive in dettaglio i vari operatori supportati da Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatori di uguaglianza e confrontoEquality and Comparison Operators

La tabella seguente illustra il risultato dei confronti di uguaglianza nell'API SQL tra due tipi JSON qualsiasi.

| **Op** | **Undefined** | **Null** | **Boolean** | **Numero** | **Stringa** | **Oggetto** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Non definito | Non definito | Non definito | Non definito | Non definito | Non definito | Non definito |
| **Null** | Non definito | **Ok** | Non definito | Non definito | Non definito | Non definito | Non definito |
| **Boolean** | Non definito | Non definito | **Ok** | Non definito | Non definito | Non definito | Non definito |
| **Numero** | Non definito | Non definito | Non definito | **Ok** | Non definito | Non definito | Non definito |
| **Stringa** | Non definito | Non definito | Non definito | Non definito | **Ok** | Non definito | Non definito |
| **Oggetto** | Non definito | Non definito | Non definito | Non definito | Non definito | **Ok** | Non definito |
| **Array** | Non definito | Non definito | Non definito | Non definito | Non definito | Non definito | **Ok** |

Per gli operatori `>` `>=`di `!=` `<`confronto `<=`quali , , , e , `Undefined`il confronto tra tipi o tra due oggetti o matrici produce .  

Se il risultato dell'espressione scalare è `Undefined`, l'elemento `Undefined` non è `true`incluso nel risultato, perché non è uguale a .

## <a name="logical-and-or-and-not-operators"></a>Operatori logici (AND, OR e NOT)

Gli operatori logici funzionano con valori booleani. Le tabelle seguenti mostrano le tabelle di verità logica per questi operatori:The following tables show the logical truth tables for these operators:

**Operator OR**

Restituisce `true` quando una `true`delle condizioni è .

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Non definito |
| **Undefined** |True |Non definito |Non definito |

**Operatore AND**

Restituisce `true` quando `true`entrambe le espressioni sono .

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |False |Non definito |
| **False** |False |False |False |
| **Undefined** |Non definito |False |Non definito |

**Operatore NOT**

Inverte il valore di qualsiasi espressione booleana.

|  | **Non** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Undefined** |Non definito |

**Ordine di precedenza degli operatori**

Gli operatori `OR` `AND`logici `NOT` , e hanno il livello di precedenza illustrato di seguito:

| **Operatore** | **Priority** |
| --- | --- |
| **Non** |1 |
| **E** |2 |
| **O** |3 |

## <a name="-operator"></a>* (operatore)

L'operatore speciale: proietta l'intero articolo così com'è. Quando usato, deve essere l'unico campo proiettato. Una query `SELECT * FROM Families f` come è `SELECT VALUE * FROM Families f` `SELECT *, f.id FROM Families f` valida, ma e non è valida.

## <a name="-and--operators"></a>? E?? operatori

È possibile usare gli operatori Ternary (?) e Coalesce (??) per compilare espressioni condizionali, come nei linguaggi di programmazione come C .NET e JavaScript.

È possibile utilizzare il ? per costruire nuove proprietà JSON in tempo reale. Ad esempio, la query seguente classifica `elementary` `other`i livelli di pendenza in o :

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

È inoltre possibile nidificare le chiamate al ? come nella query seguente: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Come con altri operatori di query, il ? operatore esclude gli elementi se le proprietà di riferimento sono mancanti o i tipi confrontati sono diversi.

Utilizzare il ?? operatore per verificare in modo efficiente la presenza di una proprietà in un elemento durante l'esecuzione di query su dati semistrutturati o di tipo misto. Ad esempio, la `lastName` query seguente `surname` restituisce se presente o se `lastName` non è presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Parole chiave](sql-query-keywords.md)
- [Clausola SELECT](sql-query-select.md)
