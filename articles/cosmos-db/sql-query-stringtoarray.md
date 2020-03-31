---
title: StringToArray nel linguaggio di query di Azure Cosmos DBStringToArray in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL StringToArray in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302917"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Restituisce l'espressione convertita in una matrice. Se l'espressione non può essere convertita, restituisce undefined.  
  
## <a name="syntax"></a>Sintassi
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa da analizzare come espressione JSON Array. 
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di matrice o non definita. 
  
## <a name="remarks"></a>Osservazioni
  I valori stringa annidati devono essere scritti tra virgolette doppie per essere JSON validi. Per informazioni dettagliate sul formato JSON, vedere [json.org](https://json.org/)
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente `StringToArray` viene illustrato come si comporta in diversi tipi. 
  
 Di seguito sono riportati esempi con input valido.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Questo è il set di risultati.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Di seguito è riportato un esempio di input non valido. 
   
 Le virgolette singole all'interno della matrice non sono JSON valido.
Anche se sono validi all'interno di una query, non verranno analizzati in matrici valide. Le stringhe all'interno della stringa\\\\di matrice devono essere sottoposte a escape "[ "]" o la virgoletta circostante deve essere singola '[""]'.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Questo è il set di risultati.

```json
[{}]
```

Di seguito sono riportati esempi di input non valido.
   
 L'espressione passata verrà analizzata come matrice JSON. di seguito non restituiscono la matrice di tipo e pertanto restituiscono undefined.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Questo è il set di risultati.

```json
[{}]
```

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
