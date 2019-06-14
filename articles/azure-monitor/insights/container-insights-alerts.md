---
title: Creare avvisi sulle prestazioni con monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo descrive come usare monitoraggio di Azure per contenitori per creare avvisi personalizzati basati su query di log per l'utilizzo della CPU e memoria.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2019
ms.author: magoedte
ms.openlocfilehash: 46ac6794272728069d50479f8cd097185bfeeb1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072398"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Come impostare gli avvisi per problemi di prestazioni in Monitoraggio di Azure per contenitori
Monitoraggio di Azure per contenitori consente di monitorare le prestazioni dei carichi di lavoro contenitore che vengono distribuiti in istanze di contenitore di Azure o gestire i cluster Kubernetes che sono ospitati in Azure Kubernetes Service (AKS).

Questo articolo descrive come abilitare gli avvisi per le situazioni seguenti:

- Quando l'utilizzo della CPU o memoria nei nodi del cluster supera una soglia
- Quando utilizzo della CPU o memoria in qualsiasi contenitore all'interno di un controller superi una soglia rispetto a un limite impostato sulla risorsa corrispondente
- *Non pronto* conta nodo stato
- *Non è riuscita*, *in sospeso*, *sconosciuto*, *esecuzione*, o *Succeeded* conta pod-fase
- Quando lo spazio su disco disponibile nei nodi cluster supera una soglia 

Per generare un avviso per l'utilizzo elevato della CPU o utilizzo della memoria o spazio su disco insufficiente nei nodi del cluster, usare le query che vengono fornite per creare un avviso di metrica o un avviso di misurazione delle metriche. Gli avvisi delle metriche hanno una latenza più bassa rispetto agli avvisi di log. Ma gli avvisi di log forniscono l'esecuzione di query avanzate e maggiore complessità. Le query confrontano un valore datetime a quella attuale utilizzando gli avvisi del log di *ora* operatore e l'inizio del backup di un'ora. (Monitoraggio di azure per contenitori archivia tutte le date nel formato Coordinated Universal Time (UTC)).

Se non si ha familiarità con gli avvisi di monitoraggio di Azure, vedere [panoramica degli avvisi in Microsoft Azure](../platform/alerts-overview.md) prima di iniziare. Per altre informazioni sugli avvisi che usano query di log, vedere [gli avvisi del Log in Monitoraggio di Azure](../platform/alerts-unified-log.md). Per altre informazioni su avvisi delle metriche, vedere [gli avvisi delle metriche in Monitoraggio di Azure](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Query di ricerca log di utilizzo delle risorse
Le query in questa sezione supportano ogni scenario avviso. Nel passaggio 7 di cui sono abituati i [crea avviso](#create-an-alert-rule) sezione di questo articolo.

La query seguente calcola l'utilizzo della CPU medio come una media di utilizzo della CPU i nodi membro ogni minuto.  

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

La query seguente calcola l'utilizzo medio della memoria come una media di utilizzo della memoria dei nodi membri ogni minuto.

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
>Le query seguenti usano i valori segnaposto \<your-cluster-name > e \<your-controller-name > per rappresentare il cluster e il controller. Sostituirli con valori specifici dell'ambiente quando si impostano gli avvisi.

La query seguente calcola l'utilizzo medio della CPU di tutti i contenitori in un controller come una media di utilizzo della CPU per ogni istanza del contenitore in un controller di ogni minuto. La misura è una percentuale del limite impostato per un contenitore.

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

La query seguente calcola l'utilizzo della memoria medio di tutti i contenitori in un controller come una media di utilizzo della memoria di ogni istanza del contenitore in un controller di ogni minuto. La misura è una percentuale del limite impostato per un contenitore.

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

La query seguente restituisce tutti i nodi e i conteggi che hanno lo stato *pronti* e *non pronto*.

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
La query seguente restituisce fase pod conta basato su tutte le fasi: *Non è riuscita*, *in sospeso*, *sconosciuto*, *esecuzione*, o *ha avuto esito positivo*.  

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
>Per generare un avviso su alcune fasi di pod, ad esempio *in sospeso*, *Failed*, o *sconosciuto*, modificare l'ultima riga della query. Ad esempio, per inviare avvisi sullo *FailedCount* usare: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

La query seguente restituisce i dischi di nodi del cluster che superano 90% dello spazio utilizzato. Per ottenere l'ID del cluster, prima di tutto eseguire la query seguente e copiare il valore dal `ClusterId` proprietà:

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
| where Namespace == 'disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Creare una regola di avviso
Seguire questi passaggi per creare un avviso di log in Monitoraggio di Azure usando una delle regole di ricerca log che è stato fornito in precedenza.  

>[!NOTE]
>La procedura seguente per creare una regola di avviso per l'utilizzo delle risorse di contenitore richiede di passare a un nuovo log degli avvisi API come descritto in [preferenza API passare per gli avvisi del log](../platform/alerts-log-api-switch.md).
>

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Monitor** dal riquadro a sinistra. Sotto **Insights**, selezionare **contenitori**.
3. Nel **i cluster monitorati** scheda, selezionare un cluster dall'elenco.
4. Nel riquadro a sinistra sotto **Monitoring**, selezionare **log** per aprire la pagina dei log di monitoraggio di Azure. Utilizzare questa pagina per scrivere ed eseguire query Analitica di Log di Azure.
5. Nel **registri** pagina, selezionare **+ nuova regola di avviso**.
6. Nel **condizione** selezionare la **ogni volta che il Registro ricerca personalizzata viene \<logica non definita >** predefiniti condizione del registro personalizzato. Il **ricerca log personalizzata** tipo di segnale viene selezionato automaticamente perché viene creata una regola di avviso direttamente dalla pagina dei log di monitoraggio di Azure.  
7. Incollare uno del [query](#resource-utilization-log-search-queries) fornito in precedenza il **query di ricerca** campo.
8. Configurare l'avviso come indicato di seguito:

    1. Nell'elenco a discesa **In base a** selezionare **Unità di misura della metrica**. Una misura della metrica consente di creare un avviso per ogni oggetto nella query che ha il valore supera la soglia specificata.
    1. Per **Condition**, selezionare **maggiore**, quindi immettere **75** come una linea di base iniziale **soglia** per gli avvisi di utilizzo della CPU e memoria . Per l'avviso di spazio su disco insufficiente, immettere **90**. Oppure immettere un valore diverso che soddisfi i criteri definiti.
    1. Nel **Trigger degli avvisi in base** sezione, selezionare **violazioni Consecutive**. Nell'elenco a discesa, selezionare **maggiore**, quindi immettere **2**.
    1. Per configurare un avviso per contenitore della CPU o utilizzo della memoria, in **Aggregate sulla**, selezionare **ContainerName**. Per configurare l'avviso di spazio su disco insufficiente del nodo cluster, selezionare **ClusterId**.
    1. Nel **valutati in base** sezione, impostare il **periodo** valore **60 minuti**. La regola verrà eseguita ogni 5 minuti e restituire i record creati nell'ultima ora dall'ora corrente. Impostazione del periodo di tempo su un account di finestra temporale potenziale latenza dei dati. Inoltre, garantisce che la query restituisce i dati per evitare un falso negativo in cui l'avviso viene mai attivato.

9. Selezionare **per completare** la regola di avviso.
10. Immettere un nome per il **nome regola di avviso** campo. Specificare una **descrizione** che fornisce i dettagli sull'avviso. Selezionare un livello di gravità adeguato tra le opzioni fornite.
11. Per attivare immediatamente la regola di avviso, accettare il valore predefinito per **Abilita regola alla creazione**.
12. Selezionare un oggetto esistente **gruppo di azioni** o creare un nuovo gruppo. Questo passaggio assicura che le stesse azioni vengono eseguite ogni volta che viene attivato un avviso. Configurazione basata su come il reparto IT o DevOps operations team gestisce gli eventi imprevisti.
13. Selezionare **Crea regola di avviso** per completare la regola di avviso. L'esecuzione inizia immediatamente.

## <a name="next-steps"></a>Passaggi successivi

- Vista [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query predefinito ed esempi per valutare o personalizzare per gli avvisi, la visualizzazione o l'analisi dei cluster.
- Per altre informazioni sul monitoraggio di Azure e su come monitorare altri aspetti del cluster servizio contenitore di AZURE, vedere [integrità vista Azure Kubernetes Service](container-insights-analyze.md).
