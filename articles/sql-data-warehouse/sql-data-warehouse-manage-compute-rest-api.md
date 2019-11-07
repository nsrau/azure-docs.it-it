---
title: Sospendere, riprendere e ridimensionare con le API REST
description: Gestire la potenza di calcolo in Azure SQL Data Warehouse tramite le API REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f72b3fd1024a68a6f48d2e9e676fc7ca23bf2a4f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686050"
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

Per attivare un database, usare l'API REST [Resume Database](/rest/api/sql/databases/resume) (Riprendere il database). L'esempio seguente avvia il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Controllare lo stato del database

> [!NOTE]
> Attualmente controllare lo stato del database potrebbe restituire ONLINE mentre il database sta completando il flusso di lavoro online, causando errori di connessione. Se si usa questa chiamata API per attivare i tentativi di connessione, potrebbe essere necessario aggiungere un ritardo da 2 a 3 minuti nel codice dell'applicazione.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Ottenere la pianificazione della manutenzione
Controllare la pianificazione di manutenzione impostata per un data warehouse. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Imposta pianificazione di manutenzione
Per impostare e aggiornare una pianificazione di maintnenance in un data warehouse esistente.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Gestire le risorse di calcolo](sql-data-warehouse-manage-compute-overview.md).

