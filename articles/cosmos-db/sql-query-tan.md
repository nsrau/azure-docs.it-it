---
title: TAN nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL TAN in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301982"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Restituisce la tangente dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   Espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente calcola la tangente di PI()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Set di risultati:  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
