---
title: ST_INTERSECTS nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL ST_INTERSECTS in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2f39dbfc33bfc34e8f5e339a489d0c74bebb415e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082284"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce un'espressione booleana che indica se l'oggetto GeoJSON (punto, poligono o LineString) specificato nel primo argomento interseca l'oggetto GeoJSON (punto, poligono o LineString) nel secondo argomento.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*spatial_expr*  
   È un'espressione di oggetto GeoJSON Point, Polygon o LineString.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un valore booleano.  
  
## <a name="examples"></a>Esempio
  
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

Questa funzione di sistema trarrà vantaggio da un [Indice geospaziale](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni spaziali Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
