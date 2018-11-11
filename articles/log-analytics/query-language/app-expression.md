---
title: espressione app() nelle query di Azure Log Analytics | Microsoft Docs
description: L'espressione app viene usata in una query di Log Analytics per recuperare dati da un'app Application Insights specifica nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: dda137ecba91e3ac59be635eed22d18e5bcadcce
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277308"
---
# <a name="app-expression-in-log-analytics-query"></a>espressione app() nelle query di Log Analytics

L'espressione `app` viene usata in una query di Log Analytics per recuperare dati da un'app Application Insights specifica nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione. È utile per includere dati dell'applicazione in una query di Log Analytics e per eseguire query di dati in più applicazioni in una query di Application Insights.



## <a name="syntax"></a>Sintassi

`app(`*Identificatore*`)`


## <a name="arguments"></a>Argomenti

- *Identificatore*: identifica l'app usando uno dei formati indicati nella tabella seguente.

| Identificatore | DESCRIZIONE | Esempio
|:---|:---|:---|
| Nome risorsa | Nome leggibile dell'app (anche noto come "nome componente") | app("fabrikamapp") |
| Nome completo | Nome completo dell'app nel formato: "subscriptionName/resourceGroup/componentName" | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | GUID dell'app | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID risorssa di Azure | Identificatore della risorsa di Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Note

* È necessario disporre dell'accesso in lettura all'applicazione.
* L'identificazione di un'applicazione in base al nome presuppone che quest'ultimo sia univoco in tutte le sottoscrizioni accessibili. Se sono presenti più applicazioni con il nome specificato, la query avrà esito negativo a causa dell'ambiguità. In questo caso è necessario usare uno degli altri identificatori.
* Usare l'espressione correlata [area di lavoro](workspace-expression.md) per eseguire query tra aree di lavoro di Log Analytics.

## <a name="examples"></a>Esempi

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Vedere l'[espressione area di lavoro](workspace-expression.md) per fare riferimento all'area di lavoro di Log Analytics.
- Altre informazioni sulla modalità di archiviazione dei [dati di Log Analytics](../../log-analytics/log-analytics-queries.md).