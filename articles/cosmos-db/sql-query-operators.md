---
title: Operatori di query SQL per Azure Cosmos DB
description: Informazioni sugli operatori SQL per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 899355ad7331a3df8cd5d647a573dc15e3a0bb14
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003392"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatori in Azure Cosmos DB

Questo articolo illustra in dettaglio i vari operatori supportati da Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatori di uguaglianza e confronto

La tabella seguente illustra il risultato dei confronti di uguaglianza nell'API SQL tra due tipi JSON qualsiasi.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Non definita | Non definita | Non definita | Non definita | Non definita | Non definita | Non definita |
| **Null** | Non definita | **Ok** | Non definita | Non definita | Non definita | Non definita | Non definita |
| **Boolean** | Non definita | Non definita | **Ok** | Non definita | Non definita | Non definita | Non definita |
| **Number** | Non definita | Non definita | Non definita | **Ok** | Non definita | Non definita | Non definita |
| **String** | Non definita | Non definita | Non definita | Non definita | **Ok** | Non definita | Non definita |
| **Object** | Non definita | Non definita | Non definita | Non definita | Non definita | **Ok** | Non definita |
| **Array** | Non definita | Non definita | Non definita | Non definita | Non definita | Non definita | **Ok** |

Per gli operatori di confronto `>`, `>=`ad `!=`esempio `<`,, `<=`, e, il confronto tra i tipi o tra due oggetti `Undefined`o matrici produce.  

Se il risultato dell'espressione scalare è `Undefined`, l'elemento non è incluso nel risultato, perché `Undefined` non è `true`uguale a.

## <a name="logical-and-or-and-not-operators"></a>Operatori logici (AND, OR e NOT)

Gli operatori logici funzionano con valori booleani. Le tabelle seguenti illustrano le tabelle di verità logiche per questi operatori:

**Operator OR**

| Oppure | True | False | Non definita |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Non definita |
| Non definita |True |Non definita |Non definita |

**Operatore AND**

| AND | True | False | Non definita |
| --- | --- | --- | --- |
| True |True |False |Non definita |
| False |False |False |False |
| Non definita |Non definita |False |Non definita |

**Operatore NOT**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Non definita |Non definita |


## <a name="-operator"></a>operatore *

L'operatore speciale * proietta l'intero elemento così com'è. Quando usato, deve essere l'unico campo proiettato. Una query come `SELECT * FROM Families f` è valida, ma `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` non sono validi.

## <a name="-and--operators"></a>? e? Operatori

È possibile usare gli operatori ternari (?) e COALESCE (??) per compilare espressioni condizionali, come nei linguaggi di C# programmazione come e JavaScript. 

È possibile usare? operatore per costruire nuove proprietà JSON in tempo reale. Ad esempio, la query seguente classifica i livelli di livello `elementary` in `other`o:

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
