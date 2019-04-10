---
title: Firewall applicazione web di Azure di monitoraggio e registrazione
description: Informazioni su firewall applicazione web (WAF) con FrontDoor monitoraggio e registrazione
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363156"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Firewall applicazione web di Azure di monitoraggio e registrazione 

La registrazione e monitoraggio di Azure web application firewall (WAF) vengono forniti tramite la registrazione e l'integrazione con monitoraggio di Azure e monitoraggio di Azure i log.

> [!IMPORTANT]
> Il WAF, monitoraggio e la funzionalità di registrazione per l'ingresso principale di Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="azure-monitor"></a>Monitoraggio di Azure

WAF con log FrontDoor è integrato con [monitoraggio di Azure](../azure-monitor/overview.md). Monitoraggio di Azure consente di tenere traccia delle informazioni diagnostiche, inclusi i log e gli avvisi WAF. È possibile configurare il monitoraggio WAF all'interno della risorsa di ingresso principale nel portale sotto il **diagnostica** scheda o tramite il monitoraggio di Azure direttamente nel servizio.

Dal portale di Azure, passare al tipo di risorsa di ingresso principale. Dal **Monitoring**/**metriche** scheda a sinistra, è possibile aggiungere **WebApplicationFirewallRequestCount** per tenere traccia di numero di richieste che corrispondono alle regole di WAF. È possibile creare filtri personalizzati basati sui tipi di azione e i nomi delle regole.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>I log e diagnostica

WAF con porta d'ingresso fornisce rapporti dettagliati su ogni minaccia rilevata. La registrazione è integrata con i log di diagnostica di Azure e gli avvisi vengono registrati in formato JSON. Questi log possono essere integrati con i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registra tutte le richieste vengono inoltrate al back-end di cliente. FrontdoorWebApplicationFirewallLog registra tutte le richieste che corrisponde a una regola WAF.

La query di esempio seguente ottiene i log WAF in richieste bloccate:

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

- Altre informazioni sulle [porta d'ingresso](front-door-overview.md).

