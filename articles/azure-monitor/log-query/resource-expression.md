---
title: espressione Resource () nella query del log di monitoraggio di Azure | Microsoft Docs
description: L'espressione di risorsa viene usata in una query di log di monitoraggio di Azure incentrata sulle risorse per recuperare dati da più risorse.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: deca6e7ef1c231a82a73067971d86a6e9cdd0599
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817410"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>espressione Resource () nella query del log di monitoraggio di Azure

L'espressione `resource` viene usata in una query di monitoraggio di Azure con [ambito a una risorsa](scope.md#query-scope) per recuperare i dati da altre risorse. 


## <a name="syntax"></a>Sintassi

`resource(`*Identificatore*`)`

## <a name="arguments"></a>Argomenti

- *Identificatore*: ID risorsa di una risorsa.

| Identificatore | Descrizione | Esempio
|:---|:---|:---|
| Resource | Include i dati per la risorsa. | risorsa ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/VirtualMachines/MyVM") |
| Gruppo di risorse o sottoscrizione | Include i dati per la risorsa e tutte le risorse in esso contenute.  | risorsa ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Note

* È necessario avere accesso in lettura alla risorsa.


## <a name="examples"></a>Esempi

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate su un ambito di query [, vedere ambito della query di log e intervallo di tempo in monitoraggio di Azure log Analytics](scope.md) .
- Accedere alla documentazione completa relativa al [linguaggio di query Kusto](/azure/kusto/query/).
