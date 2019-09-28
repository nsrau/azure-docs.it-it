---
title: Accedi a Azure Cosmos DB linguaggio di query
description: Informazioni sul LOG delle funzioni di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 16f2321eb48afacbc9636b5c0588b1ea3a01a284
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349746"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 Restituisce il logaritmo naturale dell'espressione numerica specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
*base*  
   Argomento numerico facoltativo che imposta la base per il logaritmo.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="remarks"></a>Note
  
  Per impostazione predefinita, LOG() restituisce il logaritmo naturale. È possibile modificare la base del logaritmo e impostare un altro valore usando il parametro di base facoltativo.  
  
  Il logaritmo naturale è il logaritmo in base **e**, dove **e** è una costante irrazionale approssimativamente uguale a 2,718281828.  
  
  Il logaritmo naturale del valore esponenziale di un numero è il numero stesso: LOG( EXP( n ) ) = n. E il valore esponenziale del logaritmo naturale di un numero è il numero stesso: EXP( LOG( n ) ) = n.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene dichiarata una variabile e restituito il logaritmo della variabile specificata (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Questo è il set di risultati.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Nell'esempio seguente viene calcolato il `LOG` per l'esponente di un numero.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Questo è il set di risultati.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
