---
title: Usare Azure Log Analitica per esaminare i log di Web Application Firewall del Gateway applicazione
description: Questo articolo illustra come è possibile usare Azure Log Analitica per esaminare i log di Web Application Firewall del Gateway applicazione
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712176"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Utilizzare Log Analitica per esaminare i log del Firewall applicazione Web Gateway applicazione

Una volta il WAF del Gateway applicazione è operativo, è possibile abilitare i log esaminare ciò che accade con ogni richiesta. Firewall log consentono di informazione che il firewall WAF è la valutazione, corrispondenza e il blocco. Con Log Analitica, è possibile esaminare i dati all'interno i registri firewall contenere anche maggiori dettagli. Per altre informazioni sulla creazione di un'area di lavoro di Log Analitica, vedere [creare un'area di lavoro di Log Analitica nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md). Per altre informazioni sulle query di log, vedere [Panoramica del log di query in Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importare i log WAF

Per importare i log del firewall in Log Analitica, vedere [integrità Back-end, log di diagnostica e metriche per il Gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging). Quando sono disponibili i log del firewall nell'area di lavoro di Log Analitica, è possibile visualizzare i dati, scrivere query, creare visualizzazioni e aggiungerli al dashboard del portale.

## <a name="explore-data-with-examples"></a>Esplorare i dati con gli esempi

Per visualizzare i dati non elaborati nel log di firewall, è possibile eseguire la query seguente:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Ciò avrà un aspetto simile alla query seguente:

![Query di log Analitica](media/log-analytics/log-query.png)

È possibile eseguire il drill-nei dati e tracciare i grafici o creare visualizzazioni da qui. Vedere le query seguenti come punto di partenza:

### <a name="matchedblocked-requests-by-ip"></a>Richieste corrispondenti/bloccato dall'IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Richieste corrispondenti/bloccato dall'URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Regole di corrispondenza superiore

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Prime cinque gruppi di regole corrispondente

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Aggiungi al dashboard

Dopo aver creato una query, è possibile aggiungerlo al dashboard.  Selezionare il **Aggiungi al dashboard** in alto a destra dell'area di lavoro di analitica di log. Con le query precedenti quattro aggiunte a un dashboard di esempio, si tratta dei dati che è possibile visualizzare a colpo d':

![dashboard](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

[Integrità back-end, log di diagnostica e metriche per il Gateway applicazione](application-gateway-diagnostics.md)
