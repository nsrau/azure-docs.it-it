---
title: Monitoraggio e registrazione del firewall applicazione Web di Azure
description: Informazioni sul Web Application Firewall (WAF) con monitoraggio e registrazione FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115364"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoraggio e registrazione del firewall applicazione Web di Azure 

Il monitoraggio e la registrazione di Web Application Firewall (WAF) di Azure sono disponibili tramite registrazione e integrazione con i log di monitoraggio di Azure e di monitoraggio di Azure.

## <a name="azure-monitor"></a>Monitoraggio di Azure

WAF con il log FrontDoor è integrato con [monitoraggio di Azure](../../azure-monitor/overview.md). Monitoraggio di Azure consente di tenere traccia delle informazioni di diagnostica, inclusi i log e gli avvisi WAF. È possibile configurare il monitoraggio WAF all'interno della risorsa porta anteriore nel portale nella scheda **diagnostica** o direttamente tramite il servizio monitoraggio di Azure.

Da portale di Azure passare al tipo di risorsa porta anteriore. Dalla scheda**metriche** di **monitoraggio**/a sinistra è possibile aggiungere **WebApplicationFirewallRequestCount** per tenere traccia del numero di richieste corrispondenti alle regole di WAF. I filtri personalizzati possono essere creati in base ai tipi di azione e ai nomi delle regole.

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

Di seguito è riportato un esempio di una richiesta registrata in WAF log:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

La query di esempio seguente ottiene le voci AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Di seguito è riportato un esempio di una richiesta registrata nel log di accesso:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [sportello anteriore](../../frontdoor/front-door-overview.md).
