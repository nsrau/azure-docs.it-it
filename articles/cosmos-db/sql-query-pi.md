---
title: PI nel linguaggio di query del database Cosmos di Azure
description: Informazioni sulla funzione di sistema SQL PI in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349658"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Restituisce il valore della costante pi greco.  
  
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
- [Introduzione ad Azure Cosmos DB](introduction.md)
