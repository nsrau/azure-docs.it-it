---
title: SOSTITUISCi nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL SOSTITUISCi in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349591"
---
# <a name="replace-azure-cosmos-db"></a>SOSTITUISCi (Azure Cosmos DB)
 Sostituisce tutte le occorrenze di un valore stringa specificato con un altro valore stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Espressione stringa da cercare.  
  
*str_expr2*  
   Espressione stringa da trovare.  
  
*str_expr3*  
   Espressione stringa che sostituisce le occorrenze di *str_expr2* in *str_expr1*.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come utilizzare `REPLACE` in una query.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Questo è il set di risultati.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
