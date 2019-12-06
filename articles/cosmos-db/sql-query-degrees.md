---
title: GRADI nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema di SQL in Azure Cosmos DB per restituire l'angolo corrispondente in gradi per un angolo specificato in radianti
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f432f5ef57c8eccc0cf629f00d2231364785b9a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871330"
---
# <a name="degrees-azure-cosmos-db"></a>GRADI (Azure Cosmos DB)
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
  
## <a name="examples"></a>esempi
  
  L'esempio seguente restituisce il numero di gradi di un angolo di PI/2 radianti.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Questo è il set di risultati.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
