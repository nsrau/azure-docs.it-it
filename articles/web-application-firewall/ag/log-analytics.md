---
title: Usare Log Analytics di Azure per esaminare i log del firewall applicazione Web del gateway applicazione
description: Questo articolo illustra come usare Azure Log Analytics per esaminare i log del Web Application Firewall del gateway applicazione
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: e1bc3b58f425b374e4cae1da6e9800579e503f0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516591"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Usare l'analisi dei log per esaminare i log di web application firewall del gateway applicazione

Quando il gateway applicazione WAF è operativo, è possibile abilitare i log per controllare cosa accade con ogni richiesta. I log del firewall forniscono informazioni sulle attività di valutazione, corrispondenza e blocco del WAF. Con Log Analytics, è possibile esaminare i dati all'interno dei log del firewall per ottenere informazioni ancora più dettagliate. Per ulteriori informazioni sulla creazione di un'area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../../azure-monitor/learn/quick-create-workspace.md). Per altre informazioni sulle query di log, vedere [Panoramica delle query di log in monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importa log WAF

Per importare i log del firewall in Log Analytics, vedere [integrità back-end, log di diagnostica e metriche per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging). Quando i log del firewall si trovino nell'area di lavoro Log Analytics, è possibile visualizzare i dati, scrivere query, creare visualizzazioni e aggiungerle al dashboard del portale.

## <a name="explore-data-with-examples"></a>Esplorare i dati con esempi

Per visualizzare i dati non elaborati nel registro del firewall, è possibile eseguire la query seguente:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Questo aspetto sarà simile alla query seguente:

![Query Log Analytics](../media/log-analytics/log-query.png)

È possibile eseguire il drill-down dei dati, tracciare i grafici o creare visualizzazioni da qui. Vedere le query seguenti come punto di partenza:

### <a name="matchedblocked-requests-by-ip"></a>Richieste corrispondenti/bloccate per IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Richieste corrispondenti/bloccate in base all'URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Regole corrispondenti principali

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

Dopo aver creato una query, è possibile aggiungerla al dashboard.  Selezionare il **Aggiungi al dashboard** nella parte superiore destra dell'area di lavoro di log Analytics. Con le quattro query precedenti aggiunte a un dashboard di esempio, si tratta dei dati che è possibile visualizzare a colpo d'occhio:

![Dashboard](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

[Integrità back-end, log di diagnostica e metriche per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md)