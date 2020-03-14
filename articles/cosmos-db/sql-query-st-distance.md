---
title: ST_DISTANCE nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL ST_DISTANCE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 972712d37c146ce288c49af7832919946f5503cd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297119"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Restituisce la distanza tra le due espressioni punto GeoJSON, poligono, multipoligono o LineString. Per altre informazioni, vedere l'articolo relativo ai [dati sulla posizione geospaziale e GeoJSON](sql-query-geospatial-intro.md) .
  
## <a name="syntax"></a>Sintassi
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*spatial_expr*  
   È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica che contiene la distanza. È espressa in metri per il sistema di riferimento predefinito.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come restituire tutti i documenti della famiglia che rientrano in 30 km del percorso specificato utilizzando la `ST_DISTANCE` funzione incorporata. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Set di risultati:  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Note

Questa funzione di sistema trarrà vantaggio da un [Indice geospaziale](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni spaziali Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
