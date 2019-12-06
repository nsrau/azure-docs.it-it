---
title: PIANO in Azure Cosmos DB linguaggio di query
description: Informazioni sulla funzione di sistema di SQL FLOOR in Azure Cosmos DB per restituire l'intero più grande minore o uguale all'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8faf223a4871f6f109f9d133716989b1bcfdee08
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871177"
---
# <a name="floor-azure-cosmos-db"></a>PIANO (Azure Cosmos DB)
 Restituisce il valore integer più alto, minore di o uguale all'espressione numerica specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>esempi
  
  L'esempio seguente mostra valori numerici positivi, negativi e zero con la funzione `FLOOR`.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Questo è il set di risultati.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
