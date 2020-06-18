---
title: Contains nel linguaggio di query Azure Cosmos DB
description: Informazioni su come la funzione di sistema SQL CONTAINS in Azure Cosmos DB restituisce un valore booleano che indica se la prima espressione stringa contiene la seconda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a08fe47122d7e9ddd1c9038bb5f15ebbb0be30fa
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848975"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)

 Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo.  
  
## <a name="syntax"></a>Sintassi
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Espressione stringa in cui eseguire la ricerca.  
  
*str_expr2*  
   Espressione stringa da cercare.  

*bool_expr* Valore facoltativo per ignorare la distinzione tra maiuscole e minuscole. Se impostato su true, CONTAINS eseguirà una ricerca senza distinzione tra maiuscole e minuscole. Se non è specificato, questo valore è false.
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente si verifica se "abc" contiene "ab" e se "abc" contiene "A".  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 Questo è il set di risultati.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

Il consumo di UR di Contains aumenta in modo proporzionale alla cardinalità della proprietà nella funzione di sistema. In altre parole, nel caso in cui si stia verificando se il valore di una proprietà contiene una determinata stringa, l'addebito di UR della query dipenderà dal numero di valori possibili per la proprietà.

Si considerino, ad esempio, due proprietà: Town e Country. La cardinalità di Town è 5.000, mentre la cardinalità di Country è 200. Di seguito sono riportate due query di esempio:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

La prima query userà probabilmente più RU della seconda, poiché la cardinalità di Town è superiore a quella di Country.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
