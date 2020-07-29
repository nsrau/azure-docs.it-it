---
title: Accedi a Azure Cosmos DB linguaggio di query
description: Informazioni sulla funzione di sistema LOG SQL in Azure Cosmos DB per restituire il logaritmo naturale dell'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302509"
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
  
## <a name="remarks"></a>Osservazioni
  
  Per impostazione predefinita, LOG() restituisce il logaritmo naturale. È possibile modificare la base del logaritmo e impostare un altro valore usando il parametro di base facoltativo.  
  
  Il logaritmo naturale è il logaritmo per la base **e**, in cui **e** è una costante irrazionale equivalente approssimativamente a 2,718281828.  
  
  Il logaritmo naturale dell'esponente di un numero è il numero stesso: LOG ( EXP ( n ) ) = n. E il valore esponenziale del logaritmo naturale di un numero è il numero stesso: EXP( LOG( n ) ) = n.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene dichiarata una variabile e restituito il logaritmo della variabile specificata (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Questo è il set di risultati.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Nell'esempio seguente viene calcolato il valore `LOG` per l'esponente di un numero.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Questo è il set di risultati.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
