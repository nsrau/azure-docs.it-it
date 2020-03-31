---
title: Monitor Azure virtual machines with Azure Monitor
description: Descrive come raccogliere e analizzare i dati di monitoraggio dalle macchine virtuali in Azure usando Monitoraggio di Azure.Describes how to collect and analyze monitoring data from virtual machines in Azure using Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283940"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitoraggio delle macchine virtuali di Azure con Monitoraggio di AzureMonitoring Azure virtual machines with Azure Monitor
Questo articolo descrive come usare Monitoraggio di Azure per raccogliere e analizzare i dati di monitoraggio dalle macchine virtuali di Azure per gestire l'integrità. Le macchine virtuali possono essere monitorate per la disponibilità e le prestazioni con Monitoraggio di Azure come qualsiasi altra risorsa di [Azure,](monitor-azure-resource.md)ma sono univoche rispetto alle altre risorse poiché è necessario monitorare anche il sistema operativo e il sistema guest e i carichi di lavoro in esecuzione in essa in essa in esecuzione. 

> [!NOTE]
> Questo articolo offre una panoramica completa dei concetti e delle opzioni per il monitoraggio delle macchine virtuali in Monitoraggio di Azure.This article provides a complete overview of the concepts and options for monitoring virtual machines in Azure Monitor. Per avviare rapidamente il monitoraggio delle macchine virtuali senza concentrarsi sui concetti sottostanti, vedere [Guida introduttiva: Monitorare](../learn/quick-monitor-azure-vm.md)una macchina virtuale di Azure con Monitoraggio di Azure.


## <a name="differences-from-other-azure-resources"></a>Differenze rispetto ad altre risorse di AzureDifferences from other Azure resources
[Monitoraggio delle risorse](monitor-azure-resource.md) di Azure con Monitoraggio di Azure descrive i dati di monitoraggio generati dalle risorse di Azure e come è possibile usare le funzionalità di Monitoraggio di Azure per analizzare e avvisare questi dati. È possibile raccogliere e agire sugli stessi dati di monitoraggio dalle macchine virtuali di Azure con le differenze seguenti:You can collect and act on the same monitoring data from Azure virtual machines with the following differences:

- [Le metriche](../platform/data-platform-metrics.md) della piattaforma vengono raccolte automaticamente per le macchine virtuali, ma solo per [l'host della macchina virtuale.](#monitoring-data) È necessario un agente per raccogliere i dati sulle prestazioni dal sistema operativo guest. 
- Le macchine virtuali non generano [log delle risorse](../platform/platform-logs-overview.md) che forniscono informazioni dettagliate sulle operazioni eseguite all'interno di una risorsa di Azure.Virtual machines don't generate resource logs that provide insight into operations that were performed within an Azure resource. Utilizzare un agente per raccogliere i dati di log dal sistema operativo guest.
- È possibile creare impostazioni di [diagnostica](../platform/diagnostic-settings.md) per una macchina virtuale per inviare metriche della piattaforma ad altre destinazioni, ad esempio hub di archiviazione e eventi, ma non è possibile configurare queste impostazioni di diagnostica nel portale di Azure.You can create diagnostic settings for a virtual machine to send platform metrics to other destinations such as storage and event hubs, but you can't configure these diagnostic settings in the Azure portal. 

## <a name="monitoring-data"></a>Dati di monitoraggio
Le macchine virtuali in Azure in Azure generano [log](../platform/data-platform-logs.md) e [metriche,](../platform/data-platform-metrics.md) illustrato il diagramma seguente.

![Panoramica](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host macchina virtuale
Le macchine virtuali in Azure generano i dati seguenti per l'host della macchina virtuale come le altre risorse di Azure descritte in [Dati di monitoraggio.](monitor-azure-resource.md#monitoring-data)

- [Metriche della piattaforma:](../platform/data-platform-metrics.md) valori numerici che vengono raccolti automaticamente a intervalli regolari e descrivono alcuni aspetti di una risorsa in un determinato momento. Le metriche della piattaforma vengono raccolte per l'host della macchina virtuale, ma è necessaria l'estensione di diagnostica per raccogliere le metriche per il sistema operativo guest.
- [Log attività:](../platform/platform-logs-overview.md) fornisce informazioni dettagliate sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (piano di gestione). Per una macchina virtuale, sono incluse informazioni quali l'ora dell'avvio e le eventuali modifiche alla configurazione.


### <a name="guest-operating-system"></a>Sistema operativo guest
To collect data from the guest operating system of a virtual machine, you require an agent, which runs locally on each virtual machine and sends data to Azure Monitor. Sono disponibili più agenti per Monitoraggio di Azure con ognuno dei quali raccoglie dati diversi e scrive dati in posizioni diverse. Per un confronto dettagliato dei diversi agenti, vedere [Overview of the Azure Monitor agents](../platform/agents-overview.md). 

- [Agente di Log Analytics:](../platform/agents-overview.md#log-analytics-agent) disponibile per le macchine virtuali in Azure, in altri ambienti cloud e in locale. Raccoglie i dati nei log di Monitoraggio di Azure.Collects data to Azure Monitor Logs. Supporta Monitoraggio di Azure per le macchine virtuali e le soluzioni di monitoraggio. Si tratta dello stesso agente utilizzato per System Center Operations Manager.
- [Agente di dipendenza:](../platform/agents-overview.md#dependency-agent) raccoglie dati sui processi in esecuzione nella macchina virtuale e sulle relative dipendenze. Si basa sull'agente log Analytics per trasmettere dati in Azure e supporta Monitoraggio di Azure per le macchine virtuali, la mappa del servizio e le soluzioni Wire Data 2.0.
- [Estensione diagnostica di Azure:](../platform/agents-overview.md#azure-diagnostics-extension) disponibile solo per le macchine virtuali di Monitoraggio di Azure.Azure Diagnostic extension - Available for Azure Monitor virtual machines only. Può raccogliere dati in più posizioni, ma principalmente utilizzato per raccogliere dati sulle prestazioni guest nelle metriche di Monitoraggio di Azure per le macchine virtuali Windows.Can collect data to multiple locations but primarily used to collect guest performance data into Azure Monitor Metrics for Windows virtual machines.
- [Agente Telegraf:](../platform/collect-custom-metrics-linux-telegraf.md) raccogliere i dati sulle prestazioni dalle macchine virtuali Linux nelle metriche di Monitoraggio di Azure.Telegraf agent - Collect performance data from Linux VMs into Azure Monitor Metrics.


## <a name="configuration-requirements"></a>Requisiti di configurazione
Per abilitare tutte le funzionalità di Monitoraggio di Azure per il monitoraggio di una macchina virtuale, è necessario raccogliere i dati di monitoraggio dall'host della macchina virtuale e dal sistema operativo guest sia alle [metriche](../platform/data-platform-logs.md) di monitoraggio di Azure che ai log di monitoraggio di [Azure.](../platform/data-platform-logs.md) Nella tabella seguente sono elencate le configurazioni che devono essere eseguite per abilitare questa raccolta. È possibile scegliere di non eseguire tutti questi passaggi a seconda dei requisiti specifici.

| Passaggio di configurazione | Azioni completate | Funzionalità abilitate |
|:---|:---|:---|
| Nessuna configurazione | - Metriche della piattaforma host raccolte per metriche.- Host platform metrics collected to Metrics.<br>- Registro attività raccolto. | - Esplora metriche per l'host.- Metrics explorer for host.<br>- Avvisi di metriche per l'host.- Metrics alerts for host.<br>- Avvisi del registro attività. |
| [Abilita Monitoraggio di Azure per le macchine virtuali](#enable-azure-monitor-for-vms) | - Agente Log Analytics installato.<br>- Agente di dipendenza installato.<br>- Dati sulle prestazioni degli ospiti raccolti in Registri.<br>- Dettagli di processo e dipendenza raccolti in Registri.- Process and dependency details collected to Logs. | - Grafici delle prestazioni e cartelle di lavoro per i dati sulle prestazioni degli ospiti.<br>- Registrare le query per i dati sulle prestazioni degli ospiti.- Log queries for guest performance data.<br>- Registrare gli avvisi per i dati sulle prestazioni degli ospiti.- Log alerts for guest performance data.<br>- Mappa delle dipendenze. |
| [Installare l'estensione di diagnostica e l'agente di telegraf](#enable-diagnostics-extension-and-telegraf-agent) | - Dati sulle prestazioni degli ospiti raccolti in Metriche.- Guest performance data collected to Metrics. | - Esplora metriche per gli ospiti.<br>- Avvisi di metriche per gli ospiti.  |
| [Configurare l'area di lavoro di Log Analytics](#configure-log-analytics-workspace) | - Eventi raccolti dall'ospite. | - Registrare le query per gli eventi guest.<br>- Registra gli avvisi per gli eventi degli ospiti. |
| [Creare l'impostazione di diagnostica per la macchina virtualeCreate diagnostic setting for virtual machine](#collect-platform-metrics-and-activity-log) | - Metriche della piattaforma raccolte nei log.<br>- Registro attività raccolto in Registri. | - Query Loq per le metriche host.<br>- Registrare gli avvisi per le metriche host.- Log alerts for host metrics.<br>- Registrare le query per il log attività.

Ognuno di questi passaggi di configurazione è descritto nelle sezioni seguenti.

### <a name="enable-azure-monitor-for-vms"></a>Abilita Monitoraggio di Azure per le macchine virtuali
Monitoraggio di Azure per le macchine virtuali è [un'analisi di](insights-overview.md) Monitoraggio di Azure che è lo strumento principale per il monitoraggio delle macchine virtuali in Monitoraggio di Azure.Azure [Monitor for VMs](vminsights-overview.md) is an insight in Azure Monitor that is the primary tool for monitoring virtual machines in Azure Monitor. Fornisce il valore aggiuntivo seguente rispetto alle funzionalità standard di Monitoraggio di Azure.It provides the following additional value over standard Azure Monitor features.

- Onboarding semplificato dell'agente log Analytics e dell'agente di dipendenza per consentire il monitoraggio del sistema operativo guest di una macchina virtuale e dei carichi di lavoro. 
- Grafici delle prestazioni di tendenza e cartelle di lavoro predefiniti che consentono di analizzare le metriche delle prestazioni di base dal sistema operativo guest della macchina virtuale.
- Mappa delle dipendenze che visualizza i processi in esecuzione in ogni macchina virtuale e i componenti interconnessi con altre macchine e origini esterne.

![Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/vminsights-01.png)

![Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/vminsights-02.png)


Abilitare Monitoraggio di Azure per le macchine virtuali dall'opzione **Informazioni dettagliate** nel menu della macchina virtuale del portale di Azure.Enable Azure Monitor for VMs from the Insights option in the virtual machine menu of the Azure portal. Per informazioni dettagliate e altri metodi di configurazione, vedere [Abilitare Monitoraggio di Azure per le macchine virtuali.](vminsights-enable-overview.md)

![Abilita Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configurare l'area di lavoro di Log Analytics
L'agente Log Analytics usato da Monitoraggio di Azure per le macchine virtuali invia dati a un'area di lavoro di [Log Analytics.](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured) È possibile abilitare la raccolta di dati sulle prestazioni aggiuntivi, eventi e altri dati di monitoraggio dall'agente configurando l'area di lavoro di Log Analytics.You can enable the collection of additional performance data, events, and other monitoring data from the agent by configuring the Log Analytics workspace. Deve essere configurato una sola volta, poiché qualsiasi agente che si connette all'area di lavoro scaricherà automaticamente la configurazione e inizierà immediatamente a raccogliere i dati definiti. 

È possibile accedere alla configurazione per l'area di lavoro direttamente da Monitoraggio di Azure per macchine virtuali selezionando **Configurazione area di lavoro** da **Introduzione**. Fare clic sul nome dell'area di lavoro per aprire il relativo menu.

![Configurazione dell'area di lavoro](media/monitor-vm-azure/workspace-configuration.png)

Selezionare **Impostazioni avanzate** dal menu dell'area di lavoro e quindi **Dati** per configurare le origini dati. Per gli agenti Windows, selezionare **Registri eventi di Windows** e aggiungere registri eventi comuni, ad esempio *Sistema* e *Applicazione*. Per gli agenti Linux, selezionare **Syslog** e aggiungere strutture comuni, ad esempio *crenatura* e *daemon*. Vedere [Origini dati agente in Monitoraggio di Azure](../platform/agent-data-sources.md) per un elenco delle origini dati disponibili e dettagli sulla configurazione. 

![Configurare gli eventi](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> È possibile configurare i contatori delle prestazioni da raccogliere dalla configurazione dell'area di lavoro, ma potrebbe essere ridondante con i contatori delle prestazioni raccolti da Monitoraggio di Azure per le macchine virtuali. Monitoraggio di Azure per macchine virtuali raccoglie il set di contatori più comune con una frequenza di una volta al minuto. Configurare i contatori delle prestazioni in modo che vengano raccolti dall'area di lavoro solo se si desidera raccogliere contatori non già raccolti da Monitoraggio di Azure per le macchine virtuali o se sono presenti query esistenti usando i dati sulle prestazioni.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Abilitare l'estensione diagnostica e l'agente TelegrafEnable diagnostics extension and Telegraf agent
Monitoraggio di Azure per le macchine virtuali è basato sull'agente log Analytics che raccoglie i dati in un'area di lavoro di Log Analytics.Azure Monitor for VMs is based on the Log Analytics agent that collects data into a Log Analytics workspace. In questo modo sono supportate [più funzionalità di Monitoraggio di Azure,](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) ad esempio query di [log,](../log-query/log-query-overview.md) [avvisi di log](../platform/alerts-log.md)e cartelle di [lavoro.](../platform/workbooks-overview.md) [L'estensione di diagnostica](../platform/diagnostics-extension-overview.md) raccoglie i dati sulle prestazioni dal sistema operativo guest delle macchine virtuali Windows ad Archiviazione di Azure e facoltativamente invia i dati sulle prestazioni alle metriche di Monitoraggio di Azure.The diagnostics extension collects performance data from the guest operating system of Windows virtual machines to Azure Storage and optionally sends performance data to [Azure Monitor Metrics](../platform/data-platform-metrics.md). Per le macchine virtuali Linux, [l'agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) è necessario per inviare dati alle metriche di Azure.For Linux virtual machines, the Telegraf agent is required to send data to Azure Metrics.  In questo modo vengono abilitate altre funzionalità di Monitoraggio di Azure, ad esempio [Esplora metriche](../platform/metrics-getting-started.md) e [avvisi relativi alle metriche.](../platform/alerts-metric.md) È anche possibile configurare l'estensione di diagnostica per inviare eventi e dati sulle prestazioni all'esterno di Monitoraggio di Azure usando Hub eventi di Azure.You can also configure the diagnostics extension to send events and performance data outside of Azure Monitor using Azure Event Hubs.

Installare l'estensione di diagnostica per una singola macchina virtuale Windows nel portale di Azure dall'opzione **dell'impostazione Diagnostica** nel menu della macchina virtuale. Selezionare l'opzione per abilitare **Monitoraggio di Azure** nella scheda **Sink.** Per abilitare l'estensione da un modello o da una riga di comando per più macchine virtuali, vedere [Installazione e configurazione](../platform/diagnostics-extension-overview.md#installation-and-configuration). A differenza dell'agente di Log Analytics, i dati da raccogliere sono definiti nella configurazione per l'estensione in ogni macchina virtuale.

![Impostazione di diagnostica](media/monitor-vm-azure/diagnostic-setting.png)

Per informazioni dettagliate sulla configurazione degli agenti Telegraf nelle macchine virtuali Linux, vedere [Installare e configurare Telegraf.See Install and configure Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) for details on configuring the Telegraf agents on Linux virtual machines. L'opzione di menu **Impostazioni di diagnostica** è disponibile per Linux, ma consente solo di inviare dati all'archiviazione di Azure.The Diagnostic setting menu option is available for Linux, but it will only allow you to send data to Azure storage.

### <a name="collect-platform-metrics-and-activity-log"></a>Raccogliere le metriche della piattaforma e il log attivitàCollect platform metrics and Activity log
È possibile visualizzare le metriche della piattaforma e il log attività raccolti per ogni host di macchine virtuali nel portale di Azure.You can view the platform metrics and Activity log collected for each virtual machine host in the Azure portal. Raccogliere questi dati nella stessa area di lavoro di Log Analytics di Monitoraggio di Azure per le macchine virtuali per analizzarli con gli altri dati di monitoraggio raccolti per la macchina virtuale. Questa raccolta è configurata con [un'impostazione di diagnostica](../platform/diagnostic-settings.md). Raccogliere il log attività con [un'impostazione di diagnostica per la sottoscrizione.](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)

Raccogliere le metriche della piattaforma con un'impostazione di diagnostica per la macchina virtuale. A differenza di altre risorse di Azure, non è possibile creare un'impostazione di diagnostica per una macchina virtuale nel portale di Azure, ma è necessario usare [un altro metodo.](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell) Gli esempi seguenti illustrano come raccogliere metriche per una macchina virtuale usando PowerShell e l'interfaccia della riga di comando.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Monitoraggio nel portale di Azure 
Dopo aver configurato la raccolta dei dati di monitoraggio per una macchina virtuale, sono disponibili più opzioni per l'accesso nel portale di Azure:Once you configure collection of monitoring data for a virtual machine, you have multiple options for accessing it in the Azure portal:

- Usare il menu **Monitoraggio di Azure** per accedere ai dati da tutte le risorse monitorate. 
- Usare Monitoraggio di Azure per le macchine virtuali per il monitoraggio di set di macchine virtuali su larga scala.
- Analizzare i dati per una singola macchina virtuale dal relativo menu nel portale di Azure.Analyze data for a single virtual machine from its menu in the Azure portal. Nella tabella seguente sono elencate diverse opzioni per il monitoraggio del menu delle macchine virtuali.

![Monitoraggio nel portale di Azure](media/monitor-vm-azure/monitor-menu.png)

| Opzione di menu | Descrizione |
|:---|:---|
| Panoramica | Visualizza le [metriche](../platform/data-platform-metrics.md) della piattaforma per l'host della macchina virtuale. Fare clic su un grafico per utilizzare questi dati in [Esplora metriche.](../platform/metrics-getting-started.md) |
| Log attività | [Voci del log attività](../platform/activity-log-view.md) filtrate per la macchina virtuale corrente. |
| Informazioni dettagliate | Apre [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) con la mappa per la macchina virtuale corrente selezionata. |
| Avvisi | Visualizza [gli avvisi](../platform/alerts-overview.md) per la macchina virtuale corrente.  |
| Metriche | Aprire [Esplora metriche](../platform/metrics-getting-started.md) con l'ambito impostato sulla macchina virtuale corrente. |
| Impostazioni di diagnostica | Abilitare e configurare [l'estensione di diagnostica](../platform/diagnostics-extension-overview.md) per la macchina virtuale corrente. |
| Elementi consigliati di Advisor | Consigli per la macchina virtuale corrente da [Azure Advisor.](/azure/advisor/) |
| Log | Aprire [Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) con l'ambito impostato sulla macchina virtuale corrente. [scope](../log-query/scope.md) |
| Monitoraggio connessione | Aprire [Network Watcher Connection Monitor](../../network-watcher/connection-monitor-preview.md) per monitorare le connessioni tra la macchina virtuale corrente e altre macchine virtuali. |


## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche
È possibile analizzare le metriche per le macchine virtuali usando Esplora metriche aprendo **Metriche** dal menu della macchina virtuale. Per informazioni dettagliate sull'uso di questo strumento, [vedere Introduzione a Azure Metrics Explorer.See Getting started with Azure Metrics Explorer](../platform/metrics-getting-started.md) for details on using this tool. 

Esistono due spazi dei nomi utilizzati dalle macchine virtuali per le metriche:There are two namespaces used by virtual machines for metrics:

| Spazio dei nomi | Descrizione |
|:---|:---|
| Host macchina virtuale | Metriche host raccolte automaticamente per tutte le macchine virtuali di Azure.Host metrics automatically collected for all Azure virtual machines. Elenco dettagliato delle metriche in [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Guest macchina virtuale | Metriche del sistema operativo guest raccolte dalle macchine virtuali con l'estensione di diagnostica installata e configurata per l'invio al sink di Monitoraggio di Azure.Guest operating system metrics collected from virtual machines with diagnostics extension installed and configured to send to Azure Monitor sink. |

![Metriche](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analisi dei dati di logAnalyzing log data
Azure virtual machines will collect the following data to Azure Monitor Logs. 

Monitoraggio di Azure per le macchine virtuali abilita la raccolta di un set predeterminato di contatori delle prestazioni scritti nella tabella *InsightsMetrics.Azure* Monitor for VMs enables the collection of a predetermined set of performance counters that are written to the InsightsMetrics table. Si tratta della stessa tabella usata da [Monitoraggio di Azure per i contenitori.](container-insights-overview.md) 

| Origine dati | Requisiti | Tabelle |
|:---|:---|:---|
| Monitoraggio di Azure per le macchine virtuali | Abilitare in ogni macchina virtuale. | InsightsMetrics<br>VMBoundPorta<br>Computer VM<br>VMConnection<br>VMProcess<br>Per informazioni [dettagliate,](vminsights-log-search.md) vedere Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali. |
| Log attività | Impostazione di diagnostica per la sottoscrizione. | AzureActivity |
| Metriche host | Impostazione di diagnostica per la macchina virtuale. | AzureMetrics |
| Origini dati dal sistema operativo guest | Abilitare l'agente Log Analytics e configurare le origini dati. | Vedere la documentazione per ogni origine dati. |


> [!NOTE]
> I dati sulle prestazioni raccolti dall'agente log Analytics scrivono nella tabella Perf mentre Monitoraggio di Azure per le macchine virtuali li raccoglierà nella tabella *InsightsMetrics.Performance* data collected by the Log Analytics agent writes to the *Perf* table while Azure Monitor for VMs will collect it to the InsightsMetrics table. Si tratta degli stessi dati, ma le tabelle hanno una struttura diversa. Se si dispone di query esistenti basate su *Perf*, sarà necessario riscrivere per utilizzare *InsightsMetrics*.


## <a name="alerts"></a>Avvisi
[Gli avvisi](../platform/alerts-overview.md) in Monitoraggio di Azure inviano una notifica proattiva quando vengono rilevate condizioni importanti nei dati di monitoraggio e potenzialmente avviano un'azione, ad esempio l'avvio di un'app per la logica o la chiamata a un webhook. Le regole di avviso definiscono la logica utilizzata per determinare quando deve essere creato un avviso. Monitoraggio di Azure raccoglie i dati usati dalle regole di avviso, ma è necessario creare regole per definire le condizioni di avviso nella sottoscrizione di Azure.Azure Monitor collects the data used by alert rules, but you need to create rules to define alerting conditions in your Azure subscription.

Nelle sezioni seguenti vengono descritti i tipi di regole di avviso e i suggerimenti su quando ognuno di essi deve essere utilizzato. Questa raccomandazione si basa sulla funzionalità e sul costo del tipo di regola di avviso. Per informazioni dettagliate sui prezzi degli avvisi, vedere Prezzi di [Monitoraggio di Azure.](https://azure.microsoft.com/pricing/details/monitor/)


### <a name="activity-log-alert-rules"></a>Regole di avviso del log attività
[Le regole](../platform/alerts-activity-log.md) di avviso del log attività vengono attivate quando nel log attività viene creata una voce corrispondente a criteri specifici. Non hanno alcun costo, quindi dovrebbero essere la tua prima scelta se la logica richiesta è nel log attività. 

La risorsa di destinazione per gli avvisi del log attività può essere una macchina virtuale specifica, tutte le macchine virtuali in un gruppo di risorse o tutte le macchine virtuali in una sottoscrizione.

Ad esempio, creare un avviso se una macchina virtuale critica viene arrestata selezionando la *macchina virtuale di spegnimento* per il nome del segnale.

![Avviso del log attività](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regole di avviso per le metriche
Le regole di [avviso delle metriche](../platform/alerts-metric.md) vengono attivate quando un valore di metrica supera una soglia. È possibile definire un valore di soglia specifico o consentire a Monitoraggio di Azure di determinare dinamicamente una soglia in base ai dati cronologici.  Utilizzare gli avvisi di metrica quando possibile con i dati delle metriche poiché costano meno e sono più reattivi rispetto alle regole di avviso di log. Sono anche con stato, il che significa che si risolveranno da soli quando la metrica scende al di sotto della soglia.

La risorsa di destinazione per gli avvisi del log attività può essere una macchina virtuale specifica o tutte le macchine virtuali in un gruppo di risorse.

Ad esempio, per creare un avviso quando il processore di una macchina virtuale supera un valore specifico, creare una regola di avviso metrica usando *Percentuale CPU* come tipo di segnale. Impostare un valore di soglia specifico o consentire ad Monitoraggio di Azure di impostare una soglia dinamica. 

![Avviso metrico](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Avvisi relativi ai log
[Le regole](../platform/alerts-log.md) di avviso del log vengono attivate quando i risultati di una query di log pianificata soddisfano determinati criteri. Gli avvisi di query di log sono i più costosi e meno reattivi delle regole di avviso, ma hanno accesso ai dati più diversi e possono eseguire una logica complessa che non può essere eseguita dalle altre regole di avviso. 

La risorsa di destinazione per una query di log è un'area di lavoro di Log Analytics.The target resource for a log query is a Log Analytics workspace. Filtrare per computer specifici nella query.

Ad esempio, per creare un avviso che controlla se le macchine virtuali in un determinato gruppo di risorse sono offline, usare la query seguente che restituisce un record per ogni computer che ha perso un heartbeat negli ultimi dieci minuti. Utilizzare una soglia pari a 1 che viene attivata se almeno un computer ha un heartbeat perso.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Registra avviso](media/monitor-vm-azure/log-alert-01.png)

Per creare un avviso se si è verificato un numero eccessivo di accessi non riusciti in qualsiasi macchina virtuale Windows nella sottoscrizione, utilizzare la query seguente che restituisce un record per ogni evento di accesso non riuscito nell'ultima ora. Utilizzare una soglia impostata sul numero di accessi non riusciti che verranno consentiti. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Registra avviso](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager (SCOM) fornisce un monitoraggio granulare dei carichi di lavoro nelle macchine virtuali. Vedere la Guida al [monitoraggio cloud](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) per un confronto tra piattaforme di monitoraggio e diverse strategie per l'implementazione.

Se si dispone di un ambiente SCOM esistente che si intende continuare a usare, è possibile integrarlo con Monitoraggio di Azure per fornire funzionalità aggiuntive. L'agente log Analytics usato da Monitoraggio di Azure è lo stesso usato per SCOM in modo che le macchine virtuali monitorate inviino i dati a entrambi. È comunque necessario aggiungere l'agente ad Monitoraggio di Azure per le macchine virtuali e configurare l'area di lavoro per raccogliere dati aggiuntivi come specificato in precedenza, ma le macchine virtuali possono continuare a eseguire i Management Pack esistenti in un ambiente SCOM senza modifiche.

Le funzionalità di Monitoraggio di Azure che aumentano le funzionalità SCOM esistenti includono quanto segue:Features of Azure Monitor that augment an existing SCOM features include the following:

- Usa Log Analytics per analizzare in modo interattivo i dati di log e le prestazioni.
- Usare gli avvisi di log per definire le condizioni di avviso in più macchine virtuali e usare tendenze a lungo termine che non sono possibili usando gli avvisi in SCOM.   

Per informazioni dettagliate sulla connessione del gruppo di gestione SCOM esistente all'area di lavoro di Log Analytics, vedere [Connect Operations Manager to Azure Monitor](../platform/om-agents.md) .


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come analizzare i dati nei log di Monitoraggio di Azure usando le query di log.](../log-query/get-started-queries.md)
* [Informazioni sugli avvisi usando metriche e log in Monitoraggio di Azure.Learn about alerts using metrics and logs in Azure Monitor.](../platform/alerts-overview.md)

