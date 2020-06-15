---
title: EndsWith nel linguaggio di query di Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL ENDSWITH in Azure Cosmos DB per restituire un valore booleano che indica se la prima espressione stringa termina con la seconda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0cd927af50eca04aa8162d9d8f292077d9e4165c
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844965"
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

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
