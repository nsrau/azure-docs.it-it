---
title: PI nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione PI del sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349658"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Restituisce il valore costante di pi greco.  
  
## <a name="syntax"></a>Sintassi
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene restituito il valore di `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 Questo è il set di risultati.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
