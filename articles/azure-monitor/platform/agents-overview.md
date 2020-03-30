---
title: Panoramica degli agenti di monitoraggio di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dettagliata degli agenti disponibili di Azure che supportano il monitoraggio delle macchine virtuali ospitate in Azure o in un ambiente ibrido.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249087"
---
# <a name="overview-of-azure-monitor-agents"></a>Panoramica degli agenti di Monitoraggio di AzureOverview of Azure Monitor agents

Le macchine virtuali e altre risorse di calcolo richiedono che un agente raccolga dati di monitoraggio per misurare le prestazioni e la disponibilità del sistema operativo guest e dei carichi di lavoro. Questo articolo descrive gli agenti usati da Monitoraggio di Azure e consente di determinare quali è necessario soddisfare i requisiti per l'ambiente specifico.

> [!NOTE]
> Azure Monitor currently has multiple agents because of recent consolidation of Azure Monitor and Log Analytics. Mentre ci possono essere sovrapposizioni nelle loro caratteristiche, ognuno ha capacità uniche. A seconda dei requisiti, potrebbero essere necessari uno o più agenti nelle macchine virtuali. 

Si potrebbe avere un set specifico di requisiti che non può essere completamente soddisfatto con un singolo agente per una particolare macchina virtuale. Ad esempio, è possibile usare avvisi di metrica che richiedono l'estensione di diagnostica di Azure ma che vogliono anche sfruttare la funzionalità di Monitoraggio di Azure per le macchine virtuali che richiede l'agente log Analytics e l'agente di dipendenza. In casi come questo, è possibile utilizzare più agenti, e questo è uno scenario comune per i clienti che richiedono funzionalità da ciascuno di essi.

## <a name="summary-of-agents"></a>Riepilogo degli agenti

The following tables provide a quick comparison of the Azure Monitor agents for Windows and Linux. Ulteriori dettagli su ciascuno di essi sono forniti nella sezione seguente. 

### <a name="windows-agents"></a>Agenti di Windows

| | Diagnostica<br>estensione (WAD) | Log Analytics<br>agente | Dipendenza<br>agente |
|:---|:---|:---|:---|
| Ambienti supportati | Azure | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>Locale | 
| Requisiti dell'agente  | nessuno | nessuno | Richiede l'agente log Analytics |
| Dati raccolti | Log eventi<br>eventi ETW<br>Prestazioni<br>Registri basati su file<br>Log di IIS<br>Log dell'app .NET<br>Dump di arresto anomalo<br>Registri di diagnostica dell'agente | Log eventi<br>Prestazioni<IIS logs><br>Registri basati su file<br>Informazioni dettagliate e soluzioni<br>Altri servizi | Dettagli e dipendenze del processo<br>Metriche di connessione di rete |
| Dati inviati a | Archiviazione di Azure<br>Metriche di Monitoraggio di AzureAzure Monitor Metrics<br>Hub eventi | Log di Monitoraggio di Azure | Log di Monitoraggio di Azure |


### <a name="linux-agents"></a>Agenti Linux

| | Diagnostica<br>estensione (LAD) | Telegraf<br>agente | Log Analytics<br>agente | Dipendenza<br>agente |
|:---|:---|:---|:---|:---|
| Ambienti supportati | Azure | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>Locale | Azure<br>Altro cloud<br>Locale |
| Requisiti dell'agente  | nessuno | nessuno | nessuno | Richiede l'agente log Analytics |
| Dati raccolti | syslog<br>Prestazioni | Prestazioni | syslog<br>Prestazioni| Dettagli e dipendenze del processo<br>Metriche di connessione di rete |
| Dati inviati a | Archiviazione di Azure<br>Hub eventi | Metriche di Monitoraggio di AzureAzure Monitor Metrics | Log di Monitoraggio di Azure | Log di Monitoraggio di Azure |

## <a name="log-analytics-agent"></a>Agente di Log Analytics

[L'agente di Log Analytics](log-analytics-agent.md) raccoglie i dati di monitoraggio dal sistema operativo guest e dai carichi di lavoro delle macchine virtuali in Azure, altri provider cloud e locali. Raccoglie dati in un'area di lavoro di Log Analytics.It collects data into a Log Analytics workspace. L'agente di Log Analytics è lo stesso agente utilizzato da System Center Operations Manager ed è possibile multihome computer agente per comunicare con il gruppo di gestione e Monitor di Azure contemporaneamente. Questo agente è richiesto anche da alcune informazioni dettagliate e soluzioni in Monitoraggio di Azure.This agent is also required by certain insights and solutions in Azure Monitor.


> [!NOTE]
> L'agente di Log Analytics per Windows viene spesso indicato come Microsoft Monitoring Agent (MMA). L'agente di Log Analytics per Linux viene spesso definito agente OMS.



Utilizzare l'agente di Log Analytics se è necessario:Use the Log Analytics agent if you need to:

* Raccogliere log e dati sulle prestazioni da macchine virtuali o fisiche all'esterno di Azure.Collect logs and performance data from virtual or physical machines outside of Azure. 
* Inviare dati a un'area di lavoro di Log Analytics per sfruttare le funzionalità supportate dai log di monitoraggio di [Azure,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) ad esempio le query di [log.](../log-query/log-query-overview.md)
* Usare Monitoraggio di Azure per le macchine virtuali che consente di monitorare le macchine virtuali su larga scala e ne monitora i processi e le dipendenze da altre risorse e processi esterni.Use Azure Monitor for [VMs](../insights/vminsights-overview.md) which allows you to monitor your virtual machines at scale and monitors their processes and dependencies on other resources and external processes..  
* Gestire la sicurezza delle macchine virtuali usando [il Centro sicurezza](../../security-center/security-center-intro.md) di Azure o Azure [Sentinel.](../../sentinel/overview.md)
* Usare la gestione degli aggiornamenti di Automazione di [Azure,](../../automation/automation-update-management.md)la configurazione dello stato di automazione di Azure o il rilevamento delle modifiche e l'inventario di Automazione di Azure per fornire una gestione completa delle macchine virtuali di AzureUse Azure Automation Update management , [Azure Automation State Configuration,](../../automation/automation-dsc-overview.md)or Azure Automation Change Tracking and [Inventory](../../automation/change-tracking.md) to deliver comprehensive management of your Azure VMs
* Utilizzare [soluzioni](../monitor-reference.md#insights-and-core-solutions) diverse per monitorare un servizio o un'applicazione specifica.

Le limitazioni dell'agente di Log Analytics includono:

- Impossibile inviare dati a Metriche di Monitoraggio di Azure, Archiviazione di Azure o Hub eventi di Azure.Cannot send data to Azure Monitor Metrics, Azure Storage, or Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Estensione Diagnostica di Azure

[L'estensione Diagnostica di Azure](diagnostics-extension-overview.md) raccoglie i dati di monitoraggio dal sistema operativo guest e dai carichi di lavoro delle macchine virtuali di Azure e di altre risorse di calcolo. Raccoglie principalmente dati in Archiviazione di Azure, ma consente anche di definire sink di dati per inviare dati anche ad altre destinazioni, ad esempio metriche di Monitoraggio di Azure e Hub di eventi di Azure.It primarily collects data into Azure Storage but also allows you to define data sinks to also send data to other destinations such as Azure Monitor Metrics and Azure Event Hubs.

Usare l'estensione diagnostica di Azure se è necessario:Use Azure diagnostic extension if you need to:

- Inviare dati ad Archiviazione di Azure per l'archiviazione o per analizzarli con strumenti come [Azure Storage Explorer.](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
- Inviare dati a [Metriche di Monitoraggio](data-platform-metrics.md) di Azure per analizzarli con [Esplora metriche](metrics-getting-started.md) e sfruttare le funzionalità, ad esempio [gli avvisi di metrica](../../azure-monitor/platform/alerts-metric-overview.md) quasi in tempo reale e la [scalabilità automatica](autoscale-overview.md) (solo Windows).
- Inviare dati a strumenti di terze parti usando Hub eventi di [Azure.](diagnostics-extension-stream-event-hubs.md)
- Raccogliere la diagnostica di [avvio](../../virtual-machines/troubleshooting/boot-diagnostics.md) per analizzare i problemi di avvio della macchina virtuale.

Le limitazioni dell'estensione di diagnostica di Azure includono:Limitations of Azure diagnostics extension include:

- Può essere usato solo con le risorse di Azure.Can only be used with Azure resources.
- Possibilità limitata di inviare dati ai log di Monitoraggio di Azure.Limited ability to send data to Azure Monitor Logs.

## <a name="telegraf-agent"></a>Agente Telegraf

[L'agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) viene usato per raccogliere dati sulle prestazioni dai computer Linux alle metriche di Monitoraggio di Azure.The InfluxData Telegraf agent is used to collect performance data from Linux computers to Azure Monitor Metrics.

Utilizzare l'agente Telegraf se è necessario:

* Inviare dati a [Metriche di Monitoraggio](data-platform-metrics.md) di Azure per analizzarli con [Esplora metriche](metrics-getting-started.md) e sfruttare le funzionalità, ad esempio [gli avvisi di metrica](../../azure-monitor/platform/alerts-metric-overview.md) quasi in tempo reale e la [scalabilità automatica](autoscale-overview.md) (solo Linux). 



## <a name="dependency-agent"></a>Dependency Agent

L'agente di dipendenza raccoglie i dati individuati sui processi in esecuzione nella macchina virtuale e le dipendenze dei processi esterni. 

Utilizzare l'agente di dipendenza se è necessario:Use the Dependency agent if you need to:

* Usare la funzionalità mappa [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) o la soluzione Mappa del [servizio.](../insights/service-map.md)

Quando si usa l'agente di dipendenza, tenere presente quanto segue:Consider the following when using the Dependency agent:

- L'agente di dipendenza richiede l'installazione dell'agente log Analytics nella stessa macchina virtuale.
- Nelle macchine virtuali Linux l'agente log Analytics deve essere installato prima dell'estensione di diagnostica di Azure.On Linux VMs, the Log Analytics agent must be installed before the Azure Diagnostic Extension.

## <a name="extensions-compared-to-agents"></a>Estensioni rispetto agli agenti

L'estensione Log Analytics per Windows e Linux installa l'agente di Log Analytics nelle macchine virtuali di Azure.The Log Analytics extension for [Windows](../../virtual-machines/extensions/oms-windows.md) and [Linux](../../virtual-machines/extensions/oms-linux.md) install the Log Analytics agent on Azure virtual machines. L'estensione Dipendenza di Monitoraggio di Azure per Windows e Linux installa l'agente di dipendenza nelle macchine virtuali di Azure.The Azure Monitor Dependency extension for [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) and [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) install the Dependency agent on Azure virtual machines. Si tratta degli stessi agenti descritti in precedenza, ma che consentono di gestirli tramite le estensioni delle [macchine virtuali.](../../virtual-machines/extensions/overview.md) È consigliabile utilizzare le estensioni per installare e gestire gli agenti quando possibile.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ciascuno degli agenti, vedere:

- [Panoramica dell'agente di Log Analytics](log-analytics-agent.md)
- [Panoramica dell'estensione Diagnostica di AzureAzure Diagnostics extension overview](diagnostics-extension-overview.md)
- [Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
