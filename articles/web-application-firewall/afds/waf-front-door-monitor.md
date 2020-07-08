---
title: Monitoraggio e registrazione del firewall applicazione Web di Azure
description: Informazioni sul Web Application Firewall (WAF) con monitoraggio e registrazione FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808947"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoraggio e registrazione del firewall applicazione Web di Azure

Il monitoraggio e la registrazione di Web Application Firewall (WAF) di Azure sono disponibili tramite registrazione e integrazione con i log di monitoraggio di Azure e di monitoraggio di Azure.

## <a name="azure-monitor"></a>Monitoraggio di Azure

WAF con il log FrontDoor è integrato con [monitoraggio di Azure](../../azure-monitor/overview.md). Monitoraggio di Azure consente di tenere traccia delle informazioni di diagnostica, inclusi i log e gli avvisi WAF. È possibile configurare il monitoraggio WAF all'interno della risorsa porta anteriore nel portale nella scheda **diagnostica** o direttamente tramite il servizio monitoraggio di Azure.

Da portale di Azure passare al tipo di risorsa porta anteriore. Dalla **Monitoring** / scheda**metriche** di monitoraggio a sinistra è possibile aggiungere **WebApplicationFirewallRequestCount** per tenere traccia del numero di richieste corrispondenti alle regole di WAF. I filtri personalizzati possono essere creati in base ai tipi di azione e ai nomi delle regole.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics":::

## <a name="logs-and-diagnostics"></a>Log e diagnostica

WAF con sportello anteriore fornisce report dettagliati su ogni minaccia rilevata. La registrazione è integrata con i log di diagnostica di Azure e gli avvisi vengono registrati in formato JSON. Questi log possono essere integrati con i [log di Monitoraggio di Azure](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) registra tutte le richieste. FrontdoorWebApplicationFirewallLog registra tutte le richieste che corrispondono a una regola WAF con lo schema seguente:

| Proprietà  | Descrizione |
| ------------- | ------------- |
|Action|Azione eseguita sulla richiesta|
| ClientIp | Indirizzo IP del client che ha eseguito la richiesta. Se nella richiesta è presente un'intestazione X-Inoltred-for, l'indirizzo IP del client viene selezionato dal campo di intestazione. |
| ClientPort | Porta IP del client che ha effettuato la richiesta. |
| Dettagli|Ulteriori dettagli sulla richiesta corrispondente |
|| matchVariableName: nome del parametro http della richiesta corrispondente, ad esempio, nomi di intestazione|
|| matchVariableValue: valori che hanno attivato la corrispondenza|
| Host | Intestazione host della richiesta corrispondente |
| Policy | Nome del criterio WAF a cui corrisponde la richiesta. |
| PolicyMode | Modalità operativa del criterio WAF. I valori possibili sono "Prevention" e "Detection" |
| RequestUri | URI completo della richiesta corrispondente. |
| RuleName | Nome della regola WAF a cui corrisponde la richiesta. |
| SocketIp | Indirizzo IP di origine visualizzato da WAF. Questo indirizzo IP è basato sulla sessione TCP, indipendentemente dalle intestazioni di richiesta.|
| TrackingReference | Stringa di riferimento univoca che identifica una richiesta fornita da Frontdoor, inviata anche come intestazione X-Azure-Ref al client. Obbligatoria per la ricerca di dettagli nei log di accesso per una richiesta specifica. |

Nell'esempio di query seguente vengono restituiti i registri di WAF per le richieste bloccate:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Di seguito è riportato un esempio di una richiesta registrata in WAF log:

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

La query di esempio seguente restituisce le voci AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Di seguito è riportato un esempio di una richiesta registrata nel log di accesso:

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [sportello anteriore](../../frontdoor/front-door-overview.md).
