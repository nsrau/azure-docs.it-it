---
title: ST_ISVALID nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL ST_ISVALID in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9b20e57672e86c2b5a6a2a25151d779ea7bc92f3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335161"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce un valore booleano che indica se l'espressione GeoJSON punto, poligono o LineString specificata è valida.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*spatial_expr*  
   È un'espressione punto, poligono o LineString GeoJSON.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come controllare se un punto è valido usando ST_VALID.  
  
  Ad esempio, questo punto ha un valore di latitudine che non rientra nell'intervallo valido di valori [-90, 90], quindi la query restituisce false.  
  
  Per i poligoni, la specifica GeoJSON richiede che l'ultima coppia di coordinate indicata corrisponda alla prima, in modo da creare una forma chiusa. I punti all'interno di un poligono devono essere specificati in senso antiorario. Un poligono specificato in senso orario rappresenta l'inverso dell'area al suo interno.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Questo è il set di risultati.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni spaziali Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
