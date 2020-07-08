---
title: IS_NULL nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL IS_NULL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4dbf21c3052ddd5ebdd62925e65a854c47f59017
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303835"
---
# <a name="is_null-azure-cosmos-db"></a>IS_NULL (Azure Cosmos DB)
 Restituisce un valore booleano che indica se il tipo di espressione specificata è nulla.  
  
## <a name="syntax"></a>Sintassi
  
```sql
IS_NULL(<expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*expr*  
   Qualsiasi espressione.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito usando la `IS_NULL` funzione.  
  
```sql
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Questo è il set di risultati.  
  
```json
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di controllo dei tipi Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
