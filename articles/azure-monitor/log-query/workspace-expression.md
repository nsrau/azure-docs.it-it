---
title: Espressione workspace() nelle query di Azure Log Analytics | Microsoft Docs
description: L'espressione workspace viene usata in una query di Log Analytics per recuperare dati da un'area di lavoro specifica nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione.
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
ms.openlocfilehash: 24a737a728b0a249fda76cbff481bea284ac24aa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182945"
---
# <a name="workspace-expression-in-log-analytics-query"></a>Espressione workspace() nelle query di Azure Log Analytics

L'espressione `workspace` viene usata in una query di Log Analytics per recuperare dati da un'area di lavoro specifica nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione. È utile per includere dati di log in una query di Application Insights e per eseguire query sui dati in più aree di lavoro in una query di log.


## <a name="syntax"></a>Sintassi

`workspace(`*Identificatore*`)`

## <a name="arguments"></a>Argomenti

- *Identificatore*: identifica l'area di lavoro usando uno dei formati indicati nella tabella seguente.

| Identificatore | DESCRIZIONE | Esempio
|:---|:---|:---|
| Nome risorsa | Nome leggibile dell'area di lavoro (noto anche come "nome componente") | workspace("contosoretail") |
| Nome completo | Nome completo dell'area di lavoro nel formato: "subscriptionName/resourceGroup/componentName" | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | Identificatore univoco dell'area di lavoro | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID risorsa di Azure | Identificatore della risorsa di Azure | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Note

* È necessario disporre dell'accesso in lettura all'area di lavoro.
* Un'espressione correlata è `app`, che consente di eseguire query nelle applicazioni di Application Insights.

## <a name="examples"></a>Esempi

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere l'articolo sull'[espressione app](workspace-expression.md) per fare riferimento all'app Application Insights.
- Altre informazioni sulla modalità di archiviazione dei [dati di Log Analytics](../../azure-monitor/log-query/log-query-overview.md).