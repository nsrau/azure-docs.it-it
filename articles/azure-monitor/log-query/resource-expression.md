---
title: Espressione resource() nella query del log di Monitoraggio di Azure Documenti Microsoft
description: L'espressione di risorsa viene usata in una query di log di Monitoraggio di Azure incentrata sulle risorse per recuperare i dati da più risorse.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665700"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Espressione resource() nella query log di Monitoraggio di AzureResource() expression in Azure Monitor log query

L'espressione `resource` viene usata in una query di Monitoraggio di Azure [con ambito a una risorsa](scope.md#query-scope) per recuperare dati da altre risorse. 


## <a name="syntax"></a>Sintassi

`resource(`*Identificatore*`)`

## <a name="arguments"></a>Argomenti

- *Identificatore:* ID risorsa di una risorsa.

| Identificatore | Descrizione | Esempio
|:---|:---|:---|
| Risorsa | Include i dati per la risorsa. | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Gruppo di risorse o sottoscrizioneResource Group or Subscription | Include i dati per la risorsa e tutte le risorse in essa contenute.  | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Note

* È necessario disporre dell'accesso in lettura alla risorsa.


## <a name="examples"></a>Esempi

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate su un ambito di query, vedere [Registrare l'ambito e l'intervallo](scope.md) di tempo della query in Analisi dei log di Azure.See Log query scope and time range in Azure Monitor Log Analytics for details on a query scope.
- Accedere alla documentazione completa relativa al [linguaggio di query Kusto](/azure/kusto/query/).
