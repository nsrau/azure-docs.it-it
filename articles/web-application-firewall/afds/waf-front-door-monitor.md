---
title: Monitoraggio e registrazione del firewall applicazione Web di Azure
description: Informazioni sul Web Application Firewall (WAF) con monitoraggio e registrazione FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512483"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoraggio e registrazione del firewall applicazione Web di Azure 

Il monitoraggio e la registrazione di Web Application Firewall (WAF) di Azure sono disponibili tramite registrazione e integrazione con i log di monitoraggio di Azure e di monitoraggio di Azure.

## <a name="azure-monitor"></a>Monitoraggio di Azure

WAF con il log FrontDoor è integrato con [monitoraggio di Azure](../../azure-monitor/overview.md). Monitoraggio di Azure consente di tenere traccia delle informazioni di diagnostica, inclusi i log e gli avvisi WAF. È possibile configurare il monitoraggio WAF all'interno della risorsa porta anteriore nel portale nella scheda **diagnostica** o direttamente tramite il servizio monitoraggio di Azure.

Da portale di Azure passare al tipo di risorsa porta anteriore. Dalla scheda **monitoraggio**/**metrica** a sinistra è possibile aggiungere **WebApplicationFirewallRequestCount** per tenere traccia del numero di richieste corrispondenti alle regole WAF. I filtri personalizzati possono essere creati in base ai tipi di azione e ai nomi delle regole.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Log e diagnostica

WAF con sportello anteriore fornisce report dettagliati su ogni minaccia rilevata. La registrazione è integrata con i log di diagnostica di Azure e gli avvisi vengono registrati in formato JSON. Questi log possono essere integrati con i [log di Monitoraggio di Azure](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

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

- Altre informazioni su [sportello anteriore](../../frontdoor/front-door-overview.md).

