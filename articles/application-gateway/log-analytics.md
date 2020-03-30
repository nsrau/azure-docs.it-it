---
title: Esaminare i log WAF usando Azure Log Analytics
titleSuffix: Azure Application Gateway
description: Questo articolo illustra come usare Azure Log Analytics per esaminare i log di Application Gateway Web Application Application Application Firewall
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9fe4462a71852e5f66268f798f6f0418f2dd39c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048110"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Usare l'analisi dei log per esaminare i log di web application firewall del gateway applicazione

Una volta che il WAF del gateway applicazione è operativo, è possibile abilitare i log per controllare ciò che accade con ogni richiesta. I log del firewall forniscono informazioni dettagliate su ciò che il WAF sta valutando, abbinando e bloccando. Con Log Analytics, è possibile esaminare i dati all'interno dei log del firewall per fornire ulteriori informazioni. Per altre informazioni sulla creazione di un'area di lavoro di Log Analytics, vedere [Creare un'area](../azure-monitor/learn/quick-create-workspace.md)di lavoro di Log Analytics nel portale di Azure.For more information about creating a Log Analytics workspace, see Create a Log Analytics workspace in the Azure portal. Per altre informazioni sulle query di log, vedere [Panoramica delle query di log in Monitoraggio di Azure.For](../azure-monitor/log-query/log-query-overview.md)more information about log queries, see Overview of log queries in Azure Monitor .

## <a name="import-waf-logs"></a>Importare i registri WAF

Per importare i log del firewall in Log Analytics, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging). Quando i log del firewall sono presenti nell'area di lavoro di Log Analytics, è possibile visualizzare i dati, scrivere query, creare visualizzazioni e aggiungerle al dashboard del portale.

## <a name="explore-data-with-examples"></a>Esplora i dati con esempi

Per visualizzare i dati non elaborati nel registro del firewall, è possibile eseguire la query seguente:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Sarà simile alla query seguente:

![Query di Log Analytics](media/log-analytics/log-query.png)

È possibile eseguire il drill-down dei dati e tracciare grafici o creare visualizzazioni da qui. Vedere le query seguenti come punto di partenza:See the following queries as a starting point:

### <a name="matchedblocked-requests-by-ip"></a>Richieste corrispondenti/bloccate da IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Richieste corrispondenti/bloccate dall'URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Principali regole corrispondenti

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Primi cinque gruppi di regole corrispondenti

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Aggiungi al dashboard

Dopo aver creato una query, è possibile aggiungerla al dashboard.  Selezionare Aggiungi **al dashboard** in alto a destra nell'area di lavoro di analisi dei log. Con le quattro query precedenti aggiunte a un dashboard di esempio, questi sono i dati che è possibile visualizzare a colpo d'occhio:With the previous four queries pinned to an example dashboard, this are the data you can see at a glance:

![Dashboard](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

[Integrità back-end, log di diagnostica e metriche per il gateway applicazioneBack-end health, diagnostic logs, and metrics for Application Gateway](application-gateway-diagnostics.md)
