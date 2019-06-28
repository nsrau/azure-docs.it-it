---
title: Operatori di query SQL per Azure Cosmos DB
description: Informazioni sugli operatori di SQL per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342591"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatori in Azure Cosmos DB

Questo articolo illustra in dettaglio i vari operatori supportati da Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatori di confronto e uguaglianza

La tabella seguente illustra il risultato dei confronti di uguaglianza nell'API SQL tra due tipi JSON qualsiasi.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

Per gli operatori di confronto, ad esempio `>`, `>=`, `!=`, `<`, e `<=`, il confronto tra i tipi o tra due oggetti o matrici produce `Undefined`.  

Se il risultato dell'espressione scalare `Undefined`, l'elemento non è incluso nel risultato, in quanto `Undefined` non è uguale `true`.

## <a name="logical-and-or-and-not-operators"></a>Operatori logici (AND, OR e NOT)

Gli operatori logici funzionano con valori booleani. Le tabelle seguenti illustrano le tabelle di veridicità logica per questi operatori:

**Operator OR**

| Oppure | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**Operatore AND**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**Operatore NOT**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |


## <a name="-operator"></a>* operatore

L'operatore speciale * proietta l'intero elemento come è. Quando usato, deve essere l'unico campo proiettato. Una query come `SELECT * FROM Families f` valido, ma `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` nejsou platné.

## <a name="-and--operators"></a>? e?? Operatori

È possibile usare il Ternary (?) e Coalesce (?) gli operatori per compilare espressioni condizionali, come in linguaggi di programmazione quali C# e JavaScript. 

È possibile usare il? operatore per creare nuove proprietà JSON in tempo reale. Ad esempio, la query seguente classifica i livelli di categoria nella `elementary` o `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

È inoltre possibile annidare le chiamate al? operatore, come nella query seguente: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Come con altri operatori di query, il? operatore esclude gli elementi se le proprietà di cui si fa riferimento sono mancante o i tipi confrontati sono diversi.

Uso di?? operatore per verificare se una proprietà in un elemento quando si eseguono query su dati semistrutturati o di tipo misto. Ad esempio, la query seguente restituisce `lastName` se presente, oppure `surname` se `lastName` non è presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Parole chiave](sql-query-keywords.md)
- [Clausola SELECT](sql-query-select.md)
