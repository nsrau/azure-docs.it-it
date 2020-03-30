---
title: Come eseguire query sui log da Monitoraggio di Azure per i contenitori . Documenti Microsoft
description: Monitoraggio di Azure per i contenitori raccoglie metriche e i dati di log e questo articolo descrive i record e include query di esempio.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333467"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Come eseguire query sui log da Monitoraggio di Azure per i contenitoriHow to query logs from Azure Monitor for containers

Monitoraggio di Azure per i contenitori raccoglie le metriche delle prestazioni, i dati di inventario e le informazioni sullo stato di integrità dagli host e dai contenitori dei contenitori e le inoltra all'area di lavoro Log Analytics in Monitoraggio di Azure.Azure Monitor for containers collects performance metrics, inventory data, and health state information from container hosts and containers, and forwards to the Log Analytics workspace in Azure Monitor. I dati vengono raccolti ogni tre minuti. Questi dati sono disponibili per [la query](../../azure-monitor/log-query/log-query-overview.md) in Monitoraggio di Azure.This data is available for query in Azure Monitor. Questi dati possono essere applicati a diversi scenari, tra cui la pianificazione della migrazione, l'analisi della capacità, l'individuazione e la risoluzione dei problemi di prestazioni on demand.

## <a name="container-records"></a>Record dei contenitori

La tabella seguente mostra esempi di record raccolti da Monitoraggio di Azure per contenitori e i tipi di dati visualizzati nei risultati della ricerca nei log:

| Tipo di dati | Tipo di dati in Ricerca log | Campi |
| --- | --- | --- |
| Prestazioni per host e contenitori | `Perf` | Computer, ObjectName, CounterName &#40;% tempo processore, MB di letture disco, MB di scritture disco MB utilizzo di memoria, byte di ricezione di rete, byte di invio di rete, utilizzo del processore in secondi, rete&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventario contenitori | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Log contenitori | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventario di nodi contenitore | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventario dei pod in un cluster Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventario dei nodi di un cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventi di Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Servizi nel cluster Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriche delle prestazioni per la parte dei nodi del cluster Kubernetes | Perf &#124; in cui NomeOggetto : "K8SNode" | Computer, NomeOggetto, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriche delle prestazioni per la parte dei contenitori del cluster Kubernetes | Perf &#124; in cui NomeOggetto è "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriche personalizzate |`InsightsMetrics` | Computer, Nome, Spazio dei nomi, Origine, SourceSystem, Tag<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> La proprietà *Tags* rappresenta [più dimensioni](../platform/data-platform-metrics.md#multi-dimensional-metrics) per la metrica corrispondente. Per ulteriori informazioni sulle metriche `InsightsMetrics` raccolte e archiviate nella tabella e una descrizione delle proprietà dei record, vedere Panoramica di [InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Eseguire ricerche nei log per analizzare i dati

I log di Monitoraggio di Azure consentono di cercare tendenze, diagnosticare colli di bottiglia, prevedere o correlare i dati che consentono di determinare se la configurazione del cluster corrente funziona in modo ottimale. Sono disponibili ricerche predefinite nei log che è possibile iniziare a usare immediatamente o personalizzare per restituire le informazioni nel modo che si preferisce.

È possibile eseguire l'analisi interattiva dei dati nell'area di lavoro selezionando l'opzione **Visualizza registri eventi Kubernetes** o **Visualizza log contenitore** nel riquadro di anteprima dall'elenco a discesa **Visualizza nell'analisi.** La pagina **Ricerca log** viene visualizzata sulla destra della pagina attiva del portale di Azure.

![Analizzare i dati in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Il contenitore registra l'output inoltrato all'area di lavoro sono STDOUT e STDERR. Poiché Monitoraggio di Azure monitora il servizio Kubernetes gestito da Azure, i dati di Kube-system non vengono attualmente raccolti a causa della grande quantità di dati generati. 

### <a name="example-log-search-queries"></a>Esempio di query di ricerca log

Spesso è utile creare una query a partire da qualche esempio e quindi modificarla in base ai propri requisiti. Per creare query più avanzate, è possibile provare a usare le query di esempio seguenti:

| Query | Descrizione | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Elencare tutte le informazioni sul ciclo di vita di un contenitore| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Eventi di Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventario delle immagini | 
| **Selezionare l'opzione di visualizzazione corrispondente al grafico a linee**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU del contenitore | 
| **Selezionare l'opzione di visualizzazione corrispondente al grafico a linee**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memoria del contenitore |
| InsightsMetrics<br> &#124; di sistema, dove nome : "requests_count"<br> &#124; riepilogare Val (val) per TimeGenerated , bin (TimeGenerated, 1m)<br> &#124; ordinano in base a TimeGenerated asc<br> &#124; progetto RequestsPerMinute - Val - prev(Val), TimeGenerated <br> &#124; rendering a barre  | Richieste al minuto con metriche personalizzateRequests Per Minute with Custom Metrics |

## <a name="query-prometheus-metrics-data"></a>Dati delle metriche di Prometeo di queryQuery Prometheus metrics data

L'esempio seguente è una query di metriche Prometheus che mostra le letture del disco al secondo per disco per nodo.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Per visualizzare le metriche di Prometheus raschiate da Azure Monitor filtrate in base allo spazio dei nomi, specificare "prometheus". Ecco una query di esempio per visualizzare `default` le metriche Prometheus dallo spazio dei nomi kubernetes.Here is a sample query to view Prometheus metrics from the kubernetes namespace.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

I dati di Prometheus possono anche essere interrogati direttamente per nome.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Errori di configurazione delle query o di raschiatura

Per analizzare eventuali errori di configurazione o eliminazione, la `KubeMonAgentEvents` query di esempio seguente restituisce eventi informativi dalla tabella.

```
KubeMonAgentEvents | where Level != "Info" 
```

L'output mostrerà risultati simili ai seguenti:

![Registrare i risultati delle query degli eventi informativi dall'agenteLog query results of informational events from agent](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Passaggi successivi

Monitoraggio di Azure per i contenitori non include un set predefinito di avvisi. Esaminare la sezione Creare avvisi di prestazioni con Monitoraggio di Azure per i [contenitori per](container-insights-alerts.md) informazioni su come creare avvisi consigliati per un utilizzo elevato della CPU e della memoria per supportare le procedure e i processi devOps o operativi. 
