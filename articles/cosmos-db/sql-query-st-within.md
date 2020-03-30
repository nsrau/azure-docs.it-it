---
title: ST_WITHIN nel linguaggio di query del database Cosmos di AzureUse in Azure Cosmos DB query language
description: Informazioni sulla funzione del sistema SQL ST_WITHIN in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296117"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Restituisce un'espressione booleana che indica se l'oggetto GeoJSON (punto, poligono o LineString) specificato nel primo argomento è all'interno dell'oggetto GeoJSON (punto, poligono o LineString) nel secondo argomento.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*spatial_expr*  
   È un'espressione oggetto GeoJSON Point, Polygon o LineString.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un valore booleano.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come trovare `ST_WITHIN`tutti i documenti di famiglia all'interno di un poligono utilizzando .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Questo è il set di risultati.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema beneficerà di un [indice geospaziale.](index-policy.md#spatial-indexes)

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni spaziali Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
