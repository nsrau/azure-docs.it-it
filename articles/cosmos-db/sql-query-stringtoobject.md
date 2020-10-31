---
title: StringToObject in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL StringToObject in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3492d463133c15bd5e6cabb8a173ff806712125
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097091"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce l'espressione convertita in un oggetto. Se l'espressione non può essere convertita, restituisce undefined.  
  
## <a name="syntax"></a>Sintassi
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da analizzare come espressione di oggetto JSON. Si noti che i valori di stringa annidati devono essere scritti con virgolette doppie per essere validi. Per informazioni dettagliate sul formato JSON, vedere [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di oggetto o non definita.  
  
## <a name="examples"></a>Esempio
  
  Nell'esempio seguente viene illustrato il comportamento di `StringToObject` tra tipi diversi. 
  
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
Anche se sono valide all'interno di una query, non verranno analizzate in oggetti validi. Le stringhe all'interno della stringa dell'oggetto devono essere precedute da un carattere di escape "{ \\ " a \\ ": \\ " Str \\ "}" oppure la virgoletta circostante deve essere singola ' {"a": "Str"}'.

Le virgolette singole che racchiudono i nomi delle proprietà non sono JSON valide.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Questo è il set di risultati.

```json
[{}]
```  

I nomi di proprietà senza virgolette circostanti non sono JSON validi.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Questo è il set di risultati.

```json
[{}]
``` 

Di seguito sono riportati esempi con input non valido.

 L'espressione passata verrà analizzata come un oggetto JSON. questi input non restituiscono un oggetto di tipo e pertanto restituiscono undefined.

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

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
