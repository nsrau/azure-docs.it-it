---
title: TAN nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL TAN in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70636dbc00307ced8ad0cf84f41fe025c759997e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349154"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Restituisce la tangente dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente calcola la tangente di PI()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Questo è il set di risultati.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
