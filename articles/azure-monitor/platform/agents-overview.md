---
title: Panoramica degli agenti di monitoraggio di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dettagliata degli agenti disponibili di Azure che supportano il monitoraggio delle macchine virtuali ospitate in Azure o in un ambiente ibrido.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: c6aea3be5782c967c5816a1e40dc5443306671b3
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445295"
---
# <a name="overview-of-azure-monitor-agents"></a>Panoramica degli agenti di monitoraggio di Azure

Per le macchine virtuali e altre risorse di calcolo è necessario che un agente raccolga i dati di monitoraggio per misurare le prestazioni e la disponibilità del sistema operativo guest e dei carichi di lavoro. Questo articolo descrive gli agenti usati da monitoraggio di Azure e consente di determinare quale è necessario soddisfare i requisiti per un ambiente specifico.

> [!NOTE]
> Monitoraggio di Azure dispone attualmente di più agenti a causa del consolidamento recente di monitoraggio di Azure e Log Analytics. Sebbene sia possibile che si verifichi una sovrapposizione delle funzionalità, ognuna presenta funzionalità univoche. A seconda dei requisiti, potrebbe essere necessario disporre di uno o più agenti nelle macchine virtuali. 

È possibile che si disponga di un set specifico di requisiti che non possono essere soddisfatti completamente da un singolo agente per una determinata macchina virtuale. Ad esempio, si consiglia di usare gli avvisi delle metriche che richiedono l'estensione diagnostica di Azure, ma anche di sfruttare le funzionalità di Monitoraggio di Azure per le macchine virtuali che richiedono l'agente Log Analytics e Dependency Agent. In casi come questo, è possibile usare più agenti e si tratta di uno scenario comune per i clienti che necessitano di funzionalità.

## <a name="summary-of-agents"></a>Riepilogo degli agenti

Le tabelle seguenti forniscono un rapido confronto degli agenti di monitoraggio di Azure per Windows e Linux. Ulteriori dettagli su ciascuno di essi sono disponibili nella sezione seguente. 

### <a name="windows-agents"></a>Agenti di Windows

| | Diagnostica<br>estensione (WAD) | Log Analytics<br>agente | Dipendenza<br>agente |
|:---|:---|:---|:---|
| **Ambienti supportati** | Azure | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>In locale | 
| **Requisiti dell'agente**  | Nessuna | Nessuna | Richiede Log Analytics Agent |
| **Dati raccolti** | Log eventi<br>eventi ETW<br>Prestazioni<br>Log basati su file<br>Log di IIS<br>Log delle app .NET<br>Dump di arresto anomalo<br>Log di diagnostica agente | Log eventi<br>Prestazioni<IIS logs><br>Log basati su file<br>Informazioni dettagliate e soluzioni<br>Altri servizi | Dettagli e dipendenze del processo<br>Metriche della connessione di rete |
| **Dati inviati a** | Archiviazione di Azure<br>Metriche di Monitoraggio di Azure<br>Hub eventi | Log di Monitoraggio di Azure | Log di Monitoraggio di Azure |


### <a name="linux-agents"></a>Agenti Linux

| | Diagnostica<br>estensione (LAD) | Telegraf<br>agente | Log Analytics<br>agente | Dipendenza<br>agente |
|:---|:---|:---|:---|:---|
| **Ambienti supportati** | Azure | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>In locale |
| **Requisiti dell'agente**  | Nessuna | Nessuna | Nessuna | Richiede Log Analytics Agent |
| **Dati raccolti** | syslog<br>Prestazioni | Prestazioni | syslog<br>Prestazioni| Dettagli e dipendenze del processo<br>Metriche della connessione di rete |
| **Dati inviati a** | Archiviazione di Azure<br>Hub eventi | Metriche di Monitoraggio di Azure | Log di Monitoraggio di Azure | Log di Monitoraggio di Azure |

## <a name="log-analytics-agent"></a>Agente di Log Analytics

L' [agente di log Analytics](log-analytics-agent.md) raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle macchine virtuali in Azure, in altri provider di servizi cloud e in locale. Raccoglie i dati in un'area di lavoro Log Analytics. L'agente di Log Analytics è lo stesso agente usato da System Center Operations Manager ed è possibile usare i computer multihome Agent per comunicare simultaneamente con il gruppo di gestione e con monitoraggio di Azure. Questo agente è anche necessario per determinate informazioni e soluzioni in monitoraggio di Azure.


> [!NOTE]
> L'agente di Log Analytics per Windows è spesso indicato come Microsoft Monitoring Agent (MMA). L'agente di Log Analytics per Linux è spesso definito agente OMS.



Usare l'agente di Log Analytics se è necessario:

* Raccogliere i log e i dati sulle prestazioni da computer fisici o virtuali all'esterno di Azure. 
* Inviare dati a un'area di lavoro di Log Analytics per sfruttare le funzionalità supportate dai [log di monitoraggio di Azure](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , ad esempio le query di [log](../log-query/log-query-overview.md).
* Usare [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) , che consente di monitorare le macchine virtuali su larga scala e monitora i processi e le dipendenze da altre risorse e processi esterni.  
* Gestire la sicurezza delle macchine virtuali usando il [Centro sicurezza di Azure](../../security-center/security-center-intro.md) o [Azure Sentinel](../../sentinel/overview.md).
* Usare la [gestione degli aggiornamenti di automazione](../../automation/update-management/update-mgmt-overview.md)di Azure, la [configurazione dello stato di automazione](../../automation/automation-dsc-overview.md)di Azure o [rilevamento modifiche e l'inventario di automazione di Azure](../../automation/change-tracking.md) per offrire una gestione completa delle VM di Azure
* Utilizzare diverse [soluzioni](../monitor-reference.md#insights-and-core-solutions) per monitorare un servizio o un'applicazione specifica.

Le limitazioni dell'agente Log Analytics includono:

- Non è possibile inviare dati a metriche di monitoraggio di Azure, archiviazione di Azure o hub eventi di Azure.

## <a name="azure-diagnostics-extension"></a>Estensione Diagnostica di Azure

L' [estensione diagnostica di Azure](diagnostics-extension-overview.md) raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle macchine virtuali di Azure e di altre risorse di calcolo. Raccoglie principalmente i dati in archiviazione di Azure, ma consente anche di definire i sink di dati per inviare dati ad altre destinazioni, ad esempio le metriche di monitoraggio di Azure e hub eventi di Azure.

Usare l'estensione diagnostica di Azure se è necessario:

- Inviare i dati ad archiviazione di Azure per l'archiviazione o analizzarli con strumenti come [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Inviare dati alle [metriche di monitoraggio di Azure](data-platform-metrics.md) per analizzarli con [Esplora metriche](metrics-getting-started.md) e per sfruttare i vantaggi offerti dalle funzionalità, ad esempio gli [avvisi delle metriche](./alerts-metric-overview.md) near Real Time e la [scalabilità](autoscale-overview.md) automatica (solo Windows).
- Inviare dati a strumenti di terze parti usando [Hub eventi di Azure](diagnostics-extension-stream-event-hubs.md).
- Raccogliere la [diagnostica di avvio](../../virtual-machines/troubleshooting/boot-diagnostics.md) per esaminare i problemi di avvio della macchina virtuale.

Le limitazioni dell'estensione diagnostica di Azure includono:

- Può essere usato solo con le risorse di Azure.
- Possibilità limitata di inviare dati ai log di monitoraggio di Azure.

## <a name="telegraf-agent"></a>Agente Telegraf

L' [agente Telegraf InfluxData](collect-custom-metrics-linux-telegraf.md) viene usato per raccogliere i dati sulle prestazioni dai computer Linux alle metriche di monitoraggio di Azure.

Usare l'agente Telegraf se è necessario:

* Inviare dati alle [metriche di monitoraggio di Azure](data-platform-metrics.md) per analizzarli con [Esplora metriche](metrics-getting-started.md) e per sfruttare le funzionalità, ad esempio gli [avvisi delle metriche](./alerts-metric-overview.md) near Real Time e la [scalabilità](autoscale-overview.md) automatica (solo Linux). 



## <a name="dependency-agent"></a>Dependency Agent

Dependency Agent raccoglie i dati individuati sui processi in esecuzione nella macchina virtuale e sulle dipendenze del processo esterno. 

Usare Dependency Agent se è necessario:

* Usare la funzione map [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) o la soluzione [mapping dei servizi](../insights/service-map.md) .

Quando si usa Dependency Agent, tenere presente quanto segue:

- Dependency Agent richiede l'installazione dell'agente Log Analytics nella stessa macchina virtuale.
- Nelle macchine virtuali Linux è necessario installare l'agente Log Analytics prima dell'estensione diagnostica di Azure.

## <a name="extensions-compared-to-agents"></a>Estensioni rispetto agli agenti

L'estensione Log Analytics per [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) installa l'agente di log Analytics in macchine virtuali di Azure. L'estensione di dipendenza di monitoraggio di Azure per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installa Dependency Agent in macchine virtuali di Azure. Si tratta degli stessi agenti descritti sopra, ma consentono di gestirli tramite le [estensioni delle macchine virtuali](../../virtual-machines/extensions/overview.md). È consigliabile utilizzare le estensioni per installare e gestire gli agenti quando possibile.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ciascuno degli agenti, fare quanto segue:

- [Panoramica dell'agente di Log Analytics](log-analytics-agent.md)
- [Panoramica dell’estensione Diagnostica di Azure](diagnostics-extension-overview.md)
- [Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)

