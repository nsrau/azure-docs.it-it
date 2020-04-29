---
title: Operatori di query SQL per Azure Cosmos DB
description: Informazioni sugli operatori SQL come l'uguaglianza, il confronto e gli operatori logici supportati da Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063564"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatori in Azure Cosmos DB

Questo articolo illustra in dettaglio i vari operatori supportati da Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatori di uguaglianza e confronto

La tabella seguente illustra il risultato dei confronti di uguaglianza nell'API SQL tra due tipi JSON qualsiasi.

| **Op** | **Non definito** | **Null** | **Boolean** | **Numero** | **Stringa** | **Oggetto** | **Matrice** |
|---|---|---|---|---|---|---|---|
| **Non definito** | Non definito | Non definito | Non definito | Non definito | Non definito | Non definito | Non definito |
| **Null** | Non definito | **Ok** | Non definito | Non definito | Non definito | Non definito | Non definito |
| **Boolean** | Non definito | Non definito | **Ok** | Non definito | Non definito | Non definito | Non definito |
| **Numero** | Non definito | Non definito | Non definito | **Ok** | Non definito | Non definito | Non definito |
| **Stringa** | Non definito | Non definito | Non definito | Non definito | **Ok** | Non definito | Non definito |
| **Oggetto** | Non definito | Non definito | Non definito | Non definito | Non definito | **Ok** | Non definito |
| **Matrice** | Non definito | Non definito | Non definito | Non definito | Non definito | Non definito | **Ok** |

Per gli operatori di confronto `>`, `>=`ad `!=`esempio `<`,, `<=`, e, il confronto tra i tipi o tra due oggetti `Undefined`o matrici produce.  

Se il risultato dell'espressione scalare è `Undefined`, l'elemento non è incluso nel risultato, perché `Undefined` non è `true`uguale a.

## <a name="logical-and-or-and-not-operators"></a>Operatori logici (AND, OR e NOT)

Gli operatori logici funzionano con valori booleani. Le tabelle seguenti illustrano le tabelle di verità logiche per questi operatori:

**Operatore OR**

Restituisce `true` quando una delle condizioni è `true`.

|  | **True** | **False** | **Non definito** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Non definito |
| **Non definito** |True |Non definito |Non definito |

**Operatore AND**

Restituisce `true` quando entrambe le espressioni `true`sono.

|  | **True** | **False** | **Non definito** |
| --- | --- | --- | --- |
| **True** |True |False |Non definito |
| **False** |False |False |False |
| **Non definito** |Non definito |False |Non definito |

**Operatore NOT**

Inverte il valore di qualsiasi espressione booleana.

|  | **NOT** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Non definito** |Non definito |

**Ordine di precedenza degli operatori**

Gli operatori `OR`logici `AND`, e `NOT` hanno il livello di precedenza riportato di seguito:

| **Operatore** | **Priority** |
| --- | --- |
| **NOT** |1 |
| **E** |2 |
| **O** |3 |

## <a name="-operator"></a>* (operatore)

L'operatore speciale * proietta l'intero elemento così com'è. Quando usato, deve essere l'unico campo proiettato. Una query come `SELECT * FROM Families f` è valida, ma `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` non sono validi.

## <a name="-and--operators"></a>? e? operatori

È possibile usare gli operatori ternari (?) e COALESCE (??) per compilare espressioni condizionali, come nei linguaggi di programmazione come C# e JavaScript.

È possibile utilizzare il ? operatore per costruire nuove proprietà JSON in tempo reale. Ad esempio, la query seguente classifica i livelli di livello `elementary` in `other`o:

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
