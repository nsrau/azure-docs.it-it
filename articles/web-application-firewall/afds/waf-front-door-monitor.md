---
title: Monitoraggio e registrazione di Firewall applicazione Web di AzureAzure Web Application Firewall monitoring and logging
description: Informazioni su Web Application Firewall (WAF) con il monitoraggio e la registrazione FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284144"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoraggio e registrazione di Firewall applicazione Web di AzureAzure Web Application Firewall monitoring and logging 

Il monitoraggio e la registrazione di Firewall applicazione Web di Azure vengono forniti tramite la registrazione e l'integrazione con i log di Monitoraggio di Azure e Monitoraggio di Azure.Azure Web Application Firewall (WAF) monitoring and logging are provided through logging and integration with Azure Monitor and Azure Monitor logs.

## <a name="azure-monitor"></a>Monitoraggio di Azure

WAF con il log FrontDoor è integrato con [Monitor di Azure.](../../azure-monitor/overview.md) Monitoraggio di Azure consente di tenere traccia delle informazioni di diagnostica, inclusi gli avvisi e i log WAF. È possibile configurare il monitoraggio WAF all'interno della risorsa Porta frontale nel portale nella scheda Diagnostica o direttamente tramite il servizio Monitoraggio di Azure.You can configure WAF monitoring within the Front Door resource in the portal under the **Diagnostics** tab or through the Azure Monitor service directly.

Dal portale di Azure passare al tipo di risorsa Front Door.From Azure portal, go to Front Door resource type. Dalla scheda**Metriche** di **monitoraggio**/a sinistra, è possibile aggiungere **WebApplicationFirewallRequestCount** per tenere traccia del numero di richieste che soddisfano le regole WAF. È possibile creare filtri personalizzati in base ai tipi di azione e ai nomi delle regole.

![COPERTURa](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Registri e diagnostica

WAF with Front Door fornisce report dettagliati su ogni minaccia rilevata. La registrazione è integrata con i log di diagnostica di Azure e gli avvisi vengono registrati in formato JSON. Questi log possono essere integrati con i [log di Monitoraggio di Azure](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registra tutte le richieste inoltrate ai back-end dei clienti. FrontdoorWebApplicationFirewallLog registra qualsiasi richiesta che corrisponde a una regola WAF.

La query di esempio seguente consente di ottenere i registri WAF per le richieste bloccate:The following example query obtains WAF logs on blocked requests:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

Di seguito è riportato un esempio di richiesta registrata nel registro WAF:

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

Di seguito è riportato un esempio di richiesta registrata nel registro di accesso:Here is an example of a logged request in Access log:

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

- Ulteriori informazioni su [Front Door](../../frontdoor/front-door-overview.md).
