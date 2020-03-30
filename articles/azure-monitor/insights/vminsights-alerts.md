---
title: Avvisi da Monitoraggio di Azure per macchine virtualiAlerts from Azure Monitor for VMs
description: Descrive come creare regole di avviso dai dati sulle prestazioni raccolti da Monitoraggio di Azure per le macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289602"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Come creare avvisi da Monitoraggio di Azure per le macchine virtualiHow to create alerts from Azure Monitor for VMs
[Gli avvisi in Monitoraggio di Azure](../platform/alerts-overview.md) notificano in modo proattivo dati e modelli interessanti nei dati di monitoraggio. Monitoraggio di Azure per le macchine virtuali non include regole di avviso preconfigurate, ma è possibile crearne di personalizzate in base ai dati raccolti. In questo articolo vengono fornite indicazioni sulla creazione di regole di avviso, incluso un set di query di esempio.


## <a name="alert-rule-types"></a>Tipi di regole di avviso
Monitoraggio di Azure include [diversi tipi di regole di avviso](../platform/alerts-overview.md#what-you-can-alert-on) in base ai dati usati per creare l'avviso. Tutti i dati raccolti da Monitoraggio di Azure per le macchine virtuali vengono archiviati nei log di Monitoraggio di Azure che supporta nogli [di log.](../platform/alerts-log.md) Non è attualmente possibile usare [gli avvisi di metrica](../platform/alerts-log.md) con i dati sulle prestazioni raccolti da Monitoraggio di Azure per le macchine virtuali perché i dati non vengono raccolti nelle metriche di Monitoraggio di Azure.You cannot currently use metric alerts with performance data collected from Azure Monitor for VMs because the data is not collected into Azure Monitor Metrics. Per raccogliere dati per gli avvisi di metrica, installare [l'estensione](../platform/diagnostics-extension-overview.md) di diagnostica per le macchine virtuali Windows o [l'agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) per le macchine virtuali Linux per raccogliere i dati sulle prestazioni nelle metriche.

Esistono due tipi di avvisi di log in Monitoraggio di Azure:There are two types of log alerts in Azure Monitor:

- [Numero di avvisi di risultati](../platform/alerts-unified-log.md#number-of-results-alert-rules) crea un singolo avviso quando una query restituisce almeno un numero specificato di record. Questi sono ideali per dati non numerici quali eventi Windows e Syslog raccolti [dall'agente di Log Analytics](../platform/log-analytics-agent.md) o per l'analisi delle tendenze delle prestazioni tra più computer.
- Gli avvisi di [misurazione metrica](../platform/alerts-unified-log.md#metric-measurement-alert-rules) creano un avviso separato per ogni record in una query con un valore che supera una soglia definita nella regola di avviso. Queste regole di avviso sono ideali per i dati sulle prestazioni raccolti da Monitoraggio di Azure per le macchine virtuali poiché possono creare singoli avvisi per ogni computer.


## <a name="alert-rule-walkthrough"></a>Procedura dettagliata della regola di avviso
Questa sezione illustra la creazione di una regola di avviso di misurazione metrica usando i dati sulle prestazioni di Monitoraggio di Azure per le macchine virtuali. È possibile utilizzare questo processo di base con un'ampia gamma di query di log per avvisare i contatori delle prestazioni diversi.

Iniziare creando una nuova regola di avviso seguendo la procedura descritta in Creare, visualizzare e gestire gli avvisi di [log tramite Monitoraggio di Azure.](../platform/alerts-log.md) Per la **risorsa**, selezionare l'area di lavoro di Log Analytics usata dalle macchine virtuali di Monitoraggio di Azure nella sottoscrizione. Poiché la risorsa di destinazione per le regole di avviso di log è sempre un'area di lavoro di Log Analytics, la query di log deve includere qualsiasi filtro per particolari macchine virtuali o set di scalabilità di macchine virtuali. 

Per la **condizione** della regola di avviso, utilizzare una delle query nella [sezione seguente](#sample-alert-queries) come query **di ricerca**. La query deve restituire una proprietà numerica *denominata AggregatedValue*. È necessario riepilogare i dati in base al computer in modo da poter creare un avviso separato per ogni macchina virtuale che supera la soglia.

Nella **logica di avviso**selezionare **Misurazione metrica** e quindi specificare un **valore Soglia**. In **Trigger Alert Based On**specificare quante volte la soglia deve essere superata prima della creazione di un avviso. Ad esempio, probabilmente non ti interessa se il processore supera una soglia una volta e poi torna alla normalità, ma ti interessa se continua a superare la soglia su più misurazioni consecutive.

La sezione **Valutato in base a** definisce la frequenza di esecuzione della query e l'intervallo di tempo per la query. Nell'esempio riportato di seguito, la query verrà eseguita ogni 15 minuti e valuterà i valori delle prestazioni raccolti negli ultimi 15 minuti.


![Regola di avviso di misurazione metrica](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Query di avviso di esempio
Le query seguenti possono essere usate con una regola di avviso di misurazione metrica usando i dati sulle prestazioni raccolti da Monitoraggio di Azure per le macchine virtuali. Ogni riepiloga i dati per computer in modo che venga creato un avviso per ogni computer con un valore che supera la soglia.

### <a name="cpu-utilization"></a>Uso della CPU

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Memoria disponibile in MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Memoria disponibile in percentuale

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Disco logico utilizzato - tutti i dischi in ogni computer

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Disco logico utilizzato - singoli dischi

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>Operazioni di I/O al secondo su disco logico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Frequenza dati disco logico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Interfacce di rete byte ricevuti - tutte le interfacce

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Interfacce di rete byte ricevuti - singole interfacce

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Byte delle interfacce di rete inviati - tutte le interfacce

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Byte delle interfacce di rete inviati - singole interfacce

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Set di scalabilità di macchine virtuali
Modificare con l'ID sottoscrizione, il gruppo di risorse e il nome del set di scalabilità di macchine virtuali.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Macchina virtuale specifica
Modificare con l'ID sottoscrizione, il gruppo di risorse e il nome della macchina virtuale.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Utilizzo della CPU per tutte le risorse di calcolo in una sottoscrizioneCPU utilization for all compute resources in a subscription
Modificare con l'ID sottoscrizione.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Utilizzo della CPU per tutte le risorse di calcolo in un gruppo di risorse
Modificare con l'ID sottoscrizione e il gruppo di risorse.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sugli [avvisi, vedere Monitoraggio di Azure.Learn](../platform/alerts-overview.md)more about alerts in Azure Monitor .
- Altre informazioni sulle query di [log usando i dati di Monitoraggio di Azure per le macchine virtuali.](vminsights-log-search.md)
