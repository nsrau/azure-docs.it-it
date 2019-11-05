---
title: Come eseguire query sui log da monitoraggio di Azure per i contenitori | Microsoft Docs
description: Il monitoraggio di Azure per i contenitori raccoglie le metriche e i dati di log e in questo articolo vengono descritti i record e sono incluse le query di esempio.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 787e9e6d0ae86568e1af74b4d67fb716841a02df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477095"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Come eseguire query sui log da monitoraggio di Azure per i contenitori

Monitoraggio di Azure per i contenitori raccoglie le metriche delle prestazioni, i dati di inventario e le informazioni sullo stato di integrità dagli host e dai contenitori del contenitore e li trasmette all'area di lavoro Log Analytics in monitoraggio di Azure. I dati vengono raccolti ogni tre minuti. Questi dati sono disponibili per le [query](../../azure-monitor/log-query/log-query-overview.md) in monitoraggio di Azure. Questi dati possono essere applicati a diversi scenari, tra cui la pianificazione della migrazione, l'analisi della capacità, l'individuazione e la risoluzione dei problemi di prestazioni on demand.

## <a name="container-records"></a>Record dei contenitori

La tabella seguente mostra esempi di record raccolti da Monitoraggio di Azure per contenitori e i tipi di dati visualizzati nei risultati della ricerca nei log:

| Tipo di dati | Tipo di dati in Ricerca log | Fields |
| --- | --- | --- |
| Prestazioni per host e contenitori | `Perf` | Computer, ObjectName, CounterName &#40;% tempo processore, MB di letture disco, MB di scritture disco MB utilizzo di memoria, byte di ricezione di rete, byte di invio di rete, utilizzo del processore in secondi, rete&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventario contenitori | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Log contenitori | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventario di nodi contenitore | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventario dei pod in un cluster Kubernetes | `KubePodInventory` | TimeGenerated, computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, controllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, clustername, PodIp, SourceSystem |
| Inventario dei nodi di un cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventi di Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Servizi nel cluster Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriche delle prestazioni per la parte dei nodi del cluster Kubernetes | Perf &#124; where ObjectName == “K8SNode” | Computer, NomeOggetto, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriche delle prestazioni per la parte dei contenitori del cluster Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, restartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriche personalizzate |`InsightsMetrics` | Computer, nome, spazio dei nomi, origine, SourceSystem, tag<sup>1</sup>, TimeGenerated, tipo, va, _ResourceId | 

<sup>1</sup> la proprietà *Tags* rappresenta [più dimensioni](../platform/data-platform-metrics.md#multi-dimensional-metrics) per la metrica corrispondente. Per altre informazioni sulle metriche raccolte e archiviate nella tabella `InsightsMetrics` e una descrizione delle proprietà dei record, vedere Panoramica di [InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>Il supporto per Prometheus è una funzionalità di anteprima pubblica al momento.
>

## <a name="search-logs-to-analyze-data"></a>Eseguire ricerche nei log per analizzare i dati

I log di monitoraggio di Azure consentono di individuare le tendenze, diagnosticare i colli di bottiglia, prevedere o correlare i dati che consentono di determinare se la configurazione corrente del cluster funziona in modo ottimale. Sono disponibili ricerche predefinite nei log che è possibile iniziare a usare immediatamente o personalizzare per restituire le informazioni nel modo che si preferisce.

È possibile eseguire l'analisi interattiva dei dati nell'area di lavoro selezionando l'opzione **Visualizza registri eventi Kubernetes** o **Visualizza log del contenitore** nel riquadro di anteprima dall'elenco **a discesa Visualizza in Analytics** . La pagina **Ricerca log** viene visualizzata sulla destra della pagina attiva del portale di Azure.

![Analizzare i dati in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Il contenitore registra l'output che viene inviato all'area di lavoro sono STDOUT e STDERR. Poiché Monitoraggio di Azure monitora il servizio Kubernetes gestito da Azure, i dati di Kube-system non vengono attualmente raccolti a causa della grande quantità di dati generati. 

### <a name="example-log-search-queries"></a>Esempio di query di ricerca log

Spesso è utile creare una query a partire da qualche esempio e quindi modificarla in base ai propri requisiti. Per creare query più avanzate, è possibile provare a usare le query di esempio seguenti:

| Query | Descrizione | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Elencare tutte le informazioni sul ciclo di vita di un contenitore| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Eventi di Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventario delle immagini | 
| **Selezionare l'opzione di visualizzazione corrispondente al grafico a linee**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU del contenitore | 
| **Selezionare l'opzione di visualizzazione corrispondente al grafico a linee**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memoria del contenitore |
| InsightsMetrics<br> &#124;dove name = = "requests_count"<br> &#124;riepilogare Val = any (Val) by TimeGenerated = bin (TimeGenerated, 1m)<br> &#124;Ordina per TimeGenerated ASC<br> &#124;Project RequestsPerMinute = Val-Prev (Val), TimeGenerated <br> &#124;Barchart di rendering  | Richieste al minuto con metriche personalizzate |

## <a name="query-prometheus-metrics-data"></a>Eseguire query sui dati di metrica Prometeo

L'esempio seguente è una query di metrica Prometeo che mostra le letture disco al secondo per ogni disco per nodo.

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

Per visualizzare le metriche Prometeo ricavate da monitoraggio di Azure filtrato in base allo spazio dei nomi, specificare "Prometeo". Ecco una query di esempio per visualizzare le metriche Prometheus dallo spazio dei nomi `default` kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

I dati Prometheus possono anche essere sottoposti a query direttamente in base al nome.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Errori di configurazione della query o di scrap

Per esaminare eventuali errori di configurazione o di frammentazione, la query di esempio seguente restituisce gli eventi informativi della tabella `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

L'output visualizzerà risultati simili ai seguenti:

![Registra i risultati della query degli eventi informativi dall'agente](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Passaggi successivi

Il monitoraggio di Azure per i contenitori non include un set predefinito di avvisi. Per informazioni su come creare avvisi consigliati per un utilizzo elevato della CPU e della memoria per supportare le procedure e i processi operativi, vedere la pagina [relativa alla creazione di avvisi di prestazioni con monitoraggio di Azure per i contenitori](container-insights-alerts.md) . 
