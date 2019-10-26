---
title: espressione Resource () nella query del log di monitoraggio di Azure | Microsoft Docs
description: L'espressione di risorsa viene usata in una query di log di monitoraggio di Azure incentrata sulle risorse per recuperare dati da più risorse.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 4787dc8a8cc06e0ad0be88597dab3481284fb58b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900222"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>espressione Resource () nella query del log di monitoraggio di Azure

L'espressione `resource` viene usata in una query di monitoraggio di Azure con [ambito a una risorsa](scope.md#query-scope) per recuperare i dati da altre risorse. 


## <a name="syntax"></a>Sintassi

`resource(`*Identificatore*`)`

## <a name="arguments"></a>Argomenti

- *Identificatore*: ID risorsa di una risorsa.

| Identificatore | Description | Esempio
|:---|:---|:---|
| Gruppi | Include i dati per la risorsa. | risorsa ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/VirtualMachines/MyVM") |
| Gruppo di risorse o sottoscrizione | Include i dati per la risorsa e tutte le risorse in esso contenute.  | risorsa ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Note

* È necessario avere accesso in lettura alla risorsa.


## <a name="examples"></a>esempi

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate su un ambito di query [, vedere ambito della query di log e intervallo di tempo in monitoraggio di Azure log Analytics](scope.md) .
- Accedere alla documentazione completa relativa al [linguaggio di query Kusto](/azure/kusto/query/).
