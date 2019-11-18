---
title: Panoramica degli agenti di monitoraggio di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dettagliata degli agenti disponibili di Azure che supportano il monitoraggio delle macchine virtuali ospitate in Azure o in un ambiente ibrido.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: a01258799efa81c8d3ddba398facaa90c24c2513
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150035"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Panoramica degli agenti di monitoraggio di Azure 
Le risorse di calcolo, ad esempio le macchine virtuali, generano dati per monitorarne le prestazioni e la disponibilità esattamente come [le altre risorse cloud](../insights/monitor-azure-resource.md). Le risorse di calcolo dispongono anche di un sistema operativo guest e di carichi di lavoro che devono essere monitorati. La raccolta dei dati di monitoraggio dall'interno della risorsa richiede un agente. Questo articolo descrive gli agenti usati da monitoraggio di Azure e consente di determinare quale è necessario soddisfare i requisiti per un ambiente specifico.

## <a name="summary-of-agents"></a>Riepilogo degli agenti

> [!NOTE]
> Monitoraggio di Azure dispone attualmente di più agenti a causa del consolidamento recente di monitoraggio di Azure e Log Analytics. Entrambi gli agenti condividono alcune funzionalità, mentre altre funzionalità sono univoche per un agente specifico. A seconda dei requisiti, potrebbe essere necessario uno degli agenti o entrambi. 

Monitoraggio di Azure dispone di tre agenti che forniscono funzionalità specifiche. A seconda dei requisiti, è possibile installare un singolo agente o multiplo nelle macchine virtuali e in altre risorse di calcolo.

* [Estensione di Diagnostica di Azure](#azure-diagnostic-extension)
* [Agente di Log Analytics](#log-analytics-agent)
* [Dependency Agent](#dependency-agent)

Nella tabella seguente viene fornito un rapido confronto tra i diversi agenti. Per informazioni dettagliate, vedere la parte restante di questo articolo.

| | Estensione di diagnostica di Azure | Agente di Log Analytics | Dependency Agent |
|:---|:---|:---|:---|
| Ambienti supportati | Azure | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>Locale |
| Sistemi operativi | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Dipendenze agente  | nessuno | nessuno | Richiede Log Analytics Agent |
| Dati raccolti | Log eventi<br>Eventi ETW<br>Syslog<br>Prestazioni<br>Log di IIS<br>Log di output di traccia dell'app .NET<br>Dump di arresto anomalo del sistema | Log eventi<br>Syslog<br>Prestazioni<br>Log di IIS<br>Log personalizzati<br>Dati dalle soluzioni | Dettagli e dipendenze del processo<br>Metriche della connessione di rete |
| Dati inviati a | Archiviazione di Azure<br>Metriche di monitoraggio di Azure<br>Hub eventi | Log di Monitoraggio di Azure | Log di Monitoraggio di Azure |



## <a name="azure-diagnostic-extension"></a>Estensione Diagnostica di Azure
L' [estensione diagnostica di Azure](../../azure-monitor/platform/diagnostics-extension-overview.md) raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle risorse di calcolo di Azure. Raccoglie principalmente i dati in archiviazione di Azure. È possibile configurare monitoraggio di Azure per copiare i dati dalla risorsa di archiviazione in un'area di lavoro Log Analytics. È anche possibile raccogliere i dati sulle prestazioni Guest nelle metriche di monitoraggio di Azure.

L'estensione diagnostica di Azure è spesso denominata estensione diagnostica di Azure (WAD) o Linux Azure Diagnostic (LAD).


### <a name="scenarios-supported"></a>Scenari supportati

Gli scenari supportati dall'estensione Diagnostica di Azure includono i seguenti:

* Raccogliere i log e i dati sulle prestazioni dalle risorse di calcolo di Azure.
* Archiviare i log e i dati sulle prestazioni dal sistema operativo guest ad archiviazione di Azure.
* Visualizzare i dati di monitoraggio nell'archiviazione utilizzando uno strumento come [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
* Raccogliere dati sulle prestazioni in un database di metriche per sfruttare le funzionalità supportate dalle [metriche di monitoraggio di Azure](data-platform-metrics.md) , ad esempio gli [avvisi delle metriche](../../azure-monitor/platform/alerts-metric-overview.md) near Real Time e la [scalabilità](autoscale-overview.md)automatica. 
* Raccogliere i dati di monitoraggio dalla risorsa di [archiviazione a un'area di lavoro di log Analytics](azure-storage-iis-table.md) per sfruttare le funzionalità supportate dai [log di monitoraggio di Azure](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , ad esempio le query di [log](../log-query/log-query-overview.md).
* Inviare i dati di monitoraggio a strumenti di terze parti usando [Hub eventi di Azure](diagnostics-extension-stream-event-hubs.md).
* Esaminare i problemi di avvio delle macchine virtuali con [Diagnostica di avvio](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Copiare dati da applicazioni in esecuzione nella macchina virtuale [per Application Insights](diagnostics-extension-to-application-insights.md) per l'integrazione con altri monitor delle applicazioni.

## <a name="log-analytics-agent"></a>Agente di Log Analytics
L' [agente di log Analytics](log-analytics-agent.md) raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle macchine virtuali in Azure, in altri provider di servizi cloud e in locale. Raccoglie i dati in un'area di lavoro Log Analytics.

Il Log Analytics Agent è lo stesso agente usato da System Center Operations Manager e i computer multihome Agent sono in comunicazione con il gruppo di gestione e con monitoraggio di Azure simultaneamente. Questo agente è richiesto anche da alcune soluzioni in monitoraggio di Azure.

L'agente di Log Analytics per Windows è spesso definito Microsoft Management Agent (MMA). L'agente di Log Analytics per Linux è spesso definito agente OMS.


### <a name="scenarios-supported"></a>Scenari supportati

Gli scenari supportati dall'agente Log Analytics includono i seguenti:

* Raccogliere i log e i dati sulle prestazioni dalle macchine virtuali in Azure, altri provider di servizi cloud e in locale. 
* Raccogliere i dati di monitoraggio in un'area di lavoro di Log Analytics per sfruttare le funzionalità supportate dai [log di monitoraggio di Azure](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , ad esempio le query di [log](../log-query/log-query-overview.md).
* Usare le soluzioni di monitoraggio di monitoraggio di Azure, ad esempio [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md), [monitoraggio di Azure per contenitori](../insights/container-insights-overview.md)e così via.  
* Gestire la sicurezza delle macchine virtuali usando [Sentinel di Azure](../../sentinel/overview.md) che richiede l'agente di log Analytics.
* Gestire la sicurezza delle macchine virtuali con il [Centro sicurezza di Azure](../../security-center/security-center-intro.md) che richiede l'agente di log Analytics.
* Usare le funzionalità di [automazione di Azure](../../automation/automation-intro.md) per offrire una gestione completa delle macchine virtuali di Azure tramite il ciclo di vita.  Di seguito sono riportati alcuni esempi di queste funzionalità che richiedono l'agente Log Analytics:
  * [Gestione aggiornamenti di Automazione di Azure](../../automation/automation-update-management.md) per gli aggiornamenti del sistema operativo.
  * [Configurazione dello stato di automazione di Azure](../../automation/automation-dsc-overview.md) per mantenere lo stato di configurazione coerente.
  * [Rilevamento modifiche e inventario di Automazione di Azure](../../automation/change-tracking.md) per tenere traccia delle modifiche di configurazione.

## <a name="dependency-agent"></a>Dependency Agent
Dependency Agent raccoglie i dati individuati sui processi in esecuzione nella macchina virtuale e sulle dipendenze del processo esterno. Questo agente è necessario per [mapping dei servizi](../insights/service-map.md) e la funzionalità mappa [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md). Dependency Agent richiede l'agente di Log Analytics e scrive i dati in un'area di lavoro Log Analytics in monitoraggio di Azure.


## <a name="using-multiple-agents"></a>Uso di più agenti
È possibile che si disponga di requisiti specifici per l'uso dell'estensione diagnostica di Azure o dell'agente Log Analytics per una determinata macchina virtuale. Ad esempio, è possibile usare gli avvisi delle metriche che richiedono l'estensione diagnostica di Azure. Tuttavia, è anche possibile usare la funzionalità di mapping di Monitoraggio di Azure per le macchine virtuali che richiede Dependency Agent e l'agente di Log Analytics. In questo caso, è possibile utilizzare più agenti e si tratta di uno scenario comune per i clienti che necessitano di funzionalità.

### <a name="considerations"></a>Considerazioni

- Dependency Agent richiede l'installazione dell'agente Log Analytics nella stessa macchina virtuale.
- Nelle macchine virtuali Linux è necessario installare l'agente Log Analytics prima dell'estensione diagnostica di Azure.


## <a name="next-steps"></a>Passaggi successivi

- Vedere [Overview of the Log Analytics agent](../../azure-monitor/platform/log-analytics-agent.md) (Panoramica dell'agente di Log Analytics) per esaminare i requisiti e i metodi supportati per distribuire l'agente nei computer ospitati in Azure, nel data center o in un altro ambiente cloud.

