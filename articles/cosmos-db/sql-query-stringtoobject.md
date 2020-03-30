---
title: StringToObject nel linguaggio di query del database Cosmos di AzureStringToObject in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL StringToObject in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296382"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Restituisce l'espressione convertita in un oggetto Object. Se l'espressione non può essere convertita, restituisce undefined.  
  
## <a name="syntax"></a>Sintassi
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa da analizzare come espressione oggetto JSON. Si noti che i valori stringa nidificati devono essere scritti con virgolette doppie per essere validi. Per informazioni dettagliate sul formato JSON, vedere [json.org](https://json.org/)  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di oggetto o non definito.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente `StringToObject` viene illustrato come si comporta in diversi tipi. 
  
 Di seguito sono riportati esempi con input valido.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Questo è il set di risultati.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Di seguito sono riportati esempi con input non valido.
Anche se sono validi all'interno di una query, non verranno analizzati in oggetti validi. Le stringhe all'interno della stringa\\di\\oggetto\\devono\\essere precedute da un carattere di escape "" " " " , "str " " o la citazione circostante deve essere singola '"a": "str" ' .

Virgolette singole che racchiudono i nomi delle proprietà non sono JSON validi.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Questo è il set di risultati.

```json
[{}]
```  

I nomi delle proprietà senza virgolette circostanti non sono JSON validi.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Questo è il set di risultati.

```json
[{}]
``` 

Di seguito sono riportati esempi con input non valido.

 L'espressione passata verrà analizzata come oggetto JSON. questi input non restituiscono il tipo object e pertanto restituiscono undefined.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
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
