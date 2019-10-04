---
title: Monitoraggio e registrazione di Azure web application firewall
description: Informazioni web application firewall (WAF) con FrontDoor monitoraggio e registrazione
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 675d06f3d2071022da3867a4c45137efb818980d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849133"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoraggio e registrazione di Azure web application firewall 

Il monitoraggio e la registrazione di Azure web application firewall (WAF) vengono forniti tramite registrazione e integrazione con monitoraggio di Azure e i log di monitoraggio di Azure.

## <a name="azure-monitor"></a>Monitoraggio di Azure

WAF con il log FrontDoor è integrato con [monitoraggio di Azure](../azure-monitor/overview.md). Monitoraggio di Azure consente di tenere traccia delle informazioni di diagnostica, inclusi i log e gli avvisi WAF. È possibile configurare il monitoraggio WAF all'interno della risorsa porta anteriore nel portale nella scheda **diagnostica** o direttamente tramite il servizio monitoraggio di Azure.

Da portale di Azure passare al tipo di risorsa porta anteriore. Dalla scheda**metriche** di **monitoraggio**/a sinistra è possibile aggiungere **WebApplicationFirewallRequestCount** per tenere traccia del numero di richieste corrispondenti alle regole di WAF. I filtri personalizzati possono essere creati in base ai tipi di azione e ai nomi delle regole.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Log e diagnostica

WAF con sportello anteriore fornisce report dettagliati su ogni minaccia rilevata. La registrazione è integrata con i log di diagnostica di Azure e gli avvisi vengono registrati in formato JSON. Questi log possono essere integrati con i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registra tutte le richieste che vengono inviate ai back-end dei clienti. FrontdoorWebApplicationFirewallLog registra tutte le richieste che corrispondono a una regola WAF.

La query di esempio seguente ottiene i log WAF per le richieste bloccate:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

La query di esempio seguente ottiene le voci AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [sportello anteriore](front-door-overview.md).

