---
title: Sospendere, riprendere e ridimensionare con REST in Azure SQL Data Warehouse | Documentazione Microsoft
description: Gestire la potenza di calcolo in SQL Data Warehouse con le API REST.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: kfile
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/13/2018
ms.author: barbkess
ms.openlocfilehash: cb5b6221a5fc1d02ed1d93d56fd3db4858923307
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
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
Per altre attività di gestione, vedere [Panoramica della gestione](./sql-data-warehouse-overview-manage.md).

