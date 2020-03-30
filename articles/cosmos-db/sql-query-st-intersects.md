---
title: ST_INTERSECTS nel linguaggio di query del database Cosmos di Azure
description: Informazioni sulla funzione del sistema SQL ST_INTERSECTS in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303138"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Restituisce un'espressione booleana che indica se l'oggetto GeoJSON (punto, poligono o LineString) specificato nel primo argomento interseca l'oggetto GeoJSON (punto, poligono o LineString) nel secondo argomento.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*spatial_expr*  
   È un'espressione oggetto GeoJSON Point, Polygon o LineString.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un valore booleano.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente indica come individuare tutte le aree che intersecano il poligono dato.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Questo è il set di risultati.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema beneficerà di un [indice geospaziale.](index-policy.md#spatial-indexes)

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni spaziali Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
