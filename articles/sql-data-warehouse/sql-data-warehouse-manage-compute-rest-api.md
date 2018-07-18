---
title: Sospendere, riprendere e ridimensionare con REST in Azure SQL Data Warehouse | Documentazione Microsoft
description: Gestire la potenza di calcolo in SQL Data Warehouse con le API REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 21423d69bf2cf06bcd208082ce492bf5dd038e29
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790662"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>API REST per Azure SQL Data Warehouse
API REST per la gestione del calcolo in Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo
Per modificare le unità Data Warehouse, usare l'API REST per [creare o aggiornare il database](/rest/api/sql/databases/createorupdate). L'esempio seguente imposta le unità Data Warehouse su DW1000 per il database MySQLDW, ospitato nel server MyServer. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Sospendere il calcolo

Per sospendere un database, usare l'API REST per [sospendere i database](/rest/api/sql/databases/pause). L'esempio seguente sospende il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Riavviare le risorse di calcolo

Per avviare un database, usare l'API REST per [riprendere i database](/rest/api/sql/databases/resume). L'esempio seguente avvia il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Controllare lo stato del database

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Gestire le risorse di calcolo](sql-data-warehouse-manage-compute-overview.md).

