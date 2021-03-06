---
title: GRADI nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema di SQL in Azure Cosmos DB per restituire l'angolo corrispondente in gradi per un angolo specificato in radianti
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5c7aa5496533a42ad726f9ed8efd8ed7429375fc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338901"
---
# <a name="degrees-azure-cosmos-db"></a>GRADI (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce l'angolo corrispondente in gradi di un angolo specificato in radianti.  
  
## <a name="syntax"></a>Sintassi
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce il numero di gradi di un angolo di PI/2 radianti.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Questo è il set di risultati.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
