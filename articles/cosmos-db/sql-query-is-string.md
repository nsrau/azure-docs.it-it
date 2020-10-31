---
title: IS_STRING nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL IS_STRING in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 17832704b69d35ab9800f06558fac50db2577241
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097941"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce un valore booleano che indica se il tipo di espressione specificata è una stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*expr*  
   Qualsiasi espressione.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la `IS_STRING` funzione.  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Questo è il set di risultati.  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di controllo dei tipi Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
