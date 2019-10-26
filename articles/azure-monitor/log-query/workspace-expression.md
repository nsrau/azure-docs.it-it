---
title: Espressione workspace() nelle query di log di Monitoraggio di Azure | Microsoft Docs
description: L'espressione workspace viene usata in una query di log di Monitoraggio di Azure per recuperare dati da un'area di lavoro specifica nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: f26b9534fbf95cc301ae782d47ab7030988fa469
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932844"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>Espressione workspace() nelle query di log di Monitoraggio di Azure

L'espressione `workspace` viene usata in una query di Monitoraggio di Azure per recuperare dati da un'area di lavoro specifica nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione. È utile per includere dati di log in una query di Application Insights e per eseguire query sui dati in più aree di lavoro in una query di log.


## <a name="syntax"></a>Sintassi

`workspace(`*Identificatore*`)`

## <a name="arguments"></a>Argomenti

- *Identificatore*: identifica l'area di lavoro usando uno dei formati indicati nella tabella seguente.

| Identificatore | Description | Esempio
|:---|:---|:---|
| Nome risorsa | Nome leggibile dell'area di lavoro (noto anche come "nome componente") | workspace("contosoretail") |
| Nome completo | Nome completo dell'area di lavoro nel formato: "subscriptionName/resourceGroup/componentName" | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | Identificatore univoco dell'area di lavoro | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID risorssa di Azure | Identificatore della risorsa di Azure | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Note

* È necessario disporre dell'accesso in lettura all'area di lavoro.
* Un'espressione correlata è `app`, che consente di eseguire query nelle applicazioni di Application Insights.

## <a name="examples"></a>esempi

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

- Vedere l' [espressione app](app-expression.md) per fare riferimento a un'app Application Insights.
- Accedere alle informazioni su come vengono archiviati i [dati di Monitoraggio di Azure](log-query-overview.md).
- Accedere alla documentazione completa relativa al [linguaggio di query Kusto](/azure/kusto/query/).
