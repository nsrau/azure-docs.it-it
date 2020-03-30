---
title: Creare avvisi di prestazioni per Monitoraggio di Azure per i contenitori. Documenti Microsoft
description: Questo articolo descrive come creare avvisi personalizzati in base alle query di log per l'utilizzo della memoria e della CPU da Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730926"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Come configurare gli avvisi relativi ai problemi di prestazioni in Monitoraggio di Azure per contenitori

Monitoraggio di Azure per i contenitori consente di monitorare le prestazioni dei carichi di lavoro dei contenitori distribuiti nelle istanze del contenitore di Azure o nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes (AKS).

In questo articolo viene descritto come abilitare gli avvisi per le situazioni seguenti:This article describes how to enable alerts for the following situations:

- Quando l'utilizzo della CPU o della memoria nei nodi del cluster supera una soglia
- Quando l'utilizzo della CPU o della memoria in qualsiasi contenitore all'interno di un controller supera una soglia rispetto a un limite impostato sulla risorsa corrispondente
- Conteggio dei nodi di stato *NotReady*
- *In* *attesa*, *Sconosciuto*, *In esecuzione*o Conteggi fase pod *riusciti*
- Quando lo spazio libero su disco nei nodi del cluster supera una soglia 

Per avvisare per un utilizzo elevato della CPU o della memoria o per un utilizzo su disco insufficiente nei nodi del cluster, usare le query fornite per creare un avviso metrico o un avviso di misurazione delle metriche. Gli avvisi di metrica hanno una latenza inferiore rispetto agli avvisi di log. Tuttavia, gli avvisi di log forniscono query avanzate e una maggiore sofisticazione. Le query di avvisi di log confrontano un datetime con il presente utilizzando l'operatore *now* e tornando indietro di un'ora. (Monitoraggio di Azure per i contenitori archivia tutte le date nel formato UTC (Coordinated Universal Time).

Se non si ha familiarità con gli avvisi di Monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure](../platform/alerts-overview.md) prima di iniziare. Per altre informazioni sugli avvisi che usano query di log, vedere [Registrare avvisi in Monitoraggio di Azure.To](../platform/alerts-unified-log.md)learn more about alerts that use log queries, see Log alerts in Azure Monitor. Per altre informazioni sugli avvisi di metrica, vedere [Avvisi di metrica in Monitoraggio di Azure.For](../platform/alerts-metric-overview.md)more about metric alerts, see Metric alerts in Azure Monitor.

## <a name="resource-utilization-log-search-queries"></a>Query di ricerca log di utilizzo delle risorse

Le query in questa sezione supportano ogni scenario di avviso. Vengono utilizzati nel passaggio 7 della sezione [Create alert](#create-an-alert-rule) di questo articolo.

La query seguente calcola l'utilizzo medio della CPU come media dell'utilizzo della CPU dei nodi membri ogni minuto.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

La query seguente calcola l'utilizzo medio della memoria come media dell'utilizzo della memoria dei nodi membri ogni minuto.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Le query seguenti usano \<i valori segnaposto \<nome-cluster> e il nome del controller> per rappresentare il cluster e il controller. Sostituirli con valori specifici dell'ambiente quando si impostano gli avvisi.

La query seguente calcola l'utilizzo medio della CPU di tutti i contenitori in un controller come media dell'utilizzo della CPU di ogni istanza del contenitore in un controller ogni minuto. La misurazione è una percentuale del limite impostato per un contenitore.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

La query seguente calcola l'utilizzo medio della memoria di tutti i contenitori in un controller come una media dell'utilizzo della memoria di ogni istanza del contenitore in un controller ogni minuto. La misurazione è una percentuale del limite impostato per un contenitore.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

La query seguente restituisce tutti i nodi e i conteggi con stato *Ready* e *NotReady*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
La query seguente restituisce i conteggi delle fasi del pod in base a tutte le fasi: *Failed*, *Pending*, *Unknown*, *Running*o *Succeeded*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Per avvisare alcune fasi del pod, ad esempio *In sospeso*, *Non riuscito*o *Sconosciuto*, modificare l'ultima riga della query. Ad esempio, per avvisare in modo da usare *FailedCount:For* example, to alert on FailedCount use: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

La query seguente restituisce i dischi dei nodi del cluster che superano il 90% di spazio libero utilizzato. Per ottenere l'ID del cluster, eseguire innanzitutto `ClusterId` la query seguente e copiare il valore dalla proprietà:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Creare una regola di avviso

Seguire questi passaggi per creare un avviso di log in Monitoraggio di Azure usando una delle regole di ricerca log fornite in precedenza. Per creare usando un modello ARM, vedere [Creazione di avvisi del log di esempio con Azure Resource Template.](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)

>[!NOTE]
>La procedura seguente per creare una regola di avviso per l'utilizzo delle risorse del contenitore richiede il passaggio a una nuova API per gli avvisi di log, come descritto in [Cambia API preferenza per gli avvisi di log](../platform/alerts-log-api-switch.md).
>

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Nel portale di Azure cercare e selezionare **Aree di lavoro**di Log Analytics .
3. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro che supporta Monitoraggio di Azure per i contenitori. 
4. Nel riquadro a sinistra selezionare Log per aprire la pagina Log di Monitoraggio di Azure.In the pane on the left side, select **Logs** to open the Azure Monitor logs page. Utilizzare questa pagina per scrivere ed eseguire query di Azure Log Analytics.You use this page to write and execute Azure Log Analytics queries.
5. Nella pagina **Registri** incollare una delle [query](#resource-utilization-log-search-queries) fornite in precedenza nel campo Query di **ricerca** e quindi selezionare **Esegui** per convalidare i risultati. Se non si esegue questo passaggio, l'opzione **Nuovo avviso** non è disponibile per la selezione.
6. Per creare un avviso di log, selezionare **Nuovo avviso.**
7. Nella sezione **Condizione** selezionare la casella **Ogni volta che la ricerca log personalizzata è \<logica non definita>** condizione di log personalizzato predefinita. Il tipo di segnale **di ricerca log personalizzato** viene selezionato automaticamente perché si sta creando una regola di avviso direttamente dalla pagina Log di Monitoraggio di Azure.The custom log search signal type is automatically selected because we're creating an alert rule directly from the Azure Monitor logs page.  
8. Incollare una delle [query](#resource-utilization-log-search-queries) fornite in precedenza nel campo Query di **ricerca.**
9. Configurare l'avviso come segue:

    1. Nell'elenco a discesa **Basato su** selezionare **Misura metrica**. Una misurazione metrica crea un avviso per ogni oggetto nella query con un valore superiore alla soglia specificata.
    1. Per **Condizione**, selezionare **Maggiore di**e immettere **75** come **soglia** di base iniziale per gli avvisi relativi all'utilizzo della CPU e della memoria. Per l'avviso di spazio su disco insufficiente, immettere **90**. In alternativa, immettere un valore diverso che soddisfi i criteri specificati.
    1. Nella sezione **Attiva avviso in base** a selezionare **Violazioni consecutive**. Nell'elenco a discesa selezionare **Maggiore di**e immettere **2**.
    1. Per configurare un avviso per l'utilizzo della CPU o della memoria del contenitore, in **Aggregazione su**selezionare **NomeContenitore**. Per configurare l'avviso di disco insufficiente del nodo del cluster, selezionare **ClusterId**.
    1. Nella sezione **Valutato in base** a impostare il valore **Periodo** su **60 minuti**. La regola verrà eseguita ogni 5 minuti e restituirà i record creati nell'ultima ora dall'ora corrente. L'impostazione del periodo di tempo su una finestra ampia tiene conto della potenziale latenza dei dati. Garantisce inoltre che la query restituisca dati per evitare un falso negativo in cui l'avviso non viene mai generato.

10. Selezionare **Fatto** per completare la regola di avviso.
11. Immettere un nome nel campo **Nome regola di avviso.** Specificare una **descrizione** che fornisce i dettagli sull'avviso. E selezionare un livello di gravità appropriato tra le opzioni fornite.
12. Per attivare immediatamente la regola di avviso, accettare il valore predefinito per **Abilita regola al momento della creazione**.
13. Selezionare un **gruppo di azioni** esistente o crearne uno nuovo. Questo passaggio garantisce che le stesse azioni vengano eseguite ogni volta che viene attivato un avviso. Configura in base alla modalità di gestione degli eventi imprevisti da parte del team IT o DevOps.
14. Selezionare **Crea regola** di avviso per completare la regola di avviso. L'esecuzione inizia immediatamente.

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare esempi di query di [log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query predefinite ed esempi per valutare o personalizzare gli avvisi, la visualizzazione o l'analisi dei cluster.
- Per altre informazioni su Monitoraggio di Azure e su come monitorare altri aspetti del cluster Kubernetes, vedere [Visualizzare le prestazioni del cluster Kubernetes](container-insights-analyze.md) e Visualizzare l'integrità del cluster [Kubernetes](container-insights-health.md).
