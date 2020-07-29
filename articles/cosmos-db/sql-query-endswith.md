---
title: EndsWith nel linguaggio di query di Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL ENDSWITH in Azure Cosmos DB per restituire un valore booleano che indica se la prima espressione stringa termina con la seconda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3d37786c7364b07228d1d8d6540e7b6d8a174eb5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84322687"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Espressione stringa.  
  
*str_expr2*  
   Espressione stringa da confrontare con la fine di *str_expr1*.

*bool_expr* Valore facoltativo per ignorare la distinzione tra maiuscole e minuscole. Se impostato su true, ENDSWITH eseguirà una ricerca senza distinzione tra maiuscole e minuscole. Se non è specificato, questo valore è false.
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
L'esempio seguente verifica se la stringa "abc" finisce con "b" e "bC".  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Questo è il set di risultati.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

Il consumo di UR di EndsWith aumenta in modo proporzionale alla cardinalità della proprietà nella funzione di sistema. In altre parole, nel caso in cui si stia verificando se il valore di una proprietà termina con una determinata stringa, l'addebito di UR della query dipenderà dal numero di valori possibili per la proprietà.

Si considerino, ad esempio, due proprietà: Town e Country. La cardinalità di Town è 5.000, mentre la cardinalità di Country è 200. Di seguito sono riportate due query di esempio:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

La prima query userà probabilmente più RU della seconda, poiché la cardinalità di Town è superiore a quella di Country.

Se la dimensione della proprietà in EndsWith è maggiore di 1 KB per alcuni documenti, il motore di query dovrà caricare tali documenti. In questo caso, il motore di query non sarà in grado di valutare completamente EndsWith con un indice. L'addebito per le UR per EndsWith sarà elevato se si dispone di un numero elevato di documenti con dimensioni di proprietà maggiori di 1 KB.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
