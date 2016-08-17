<properties
	pageTitle="Azure Insights: Metriche comuni per la scalabilità automatica di Azure Insights | Microsoft Azure"
	description="Informazioni su quali metriche vengono comunemente usate per la scalabilità automatica di servizi cloud, macchine virtuali e app Web."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring"
	documentationCenter="monitoring"/>

<tags
	ms.service="monitoring"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="ashwink"/>

# Metriche comuni per la scalabilità automatica di Azure Insights

La scalabilità automatica di Azure Insights consente di aumentare o ridurre il numero delle istanze in esecuzione in base ai dati di telemetria (metriche). Questo documento descrive le metriche comuni che è possibile usare. Nel portale di Azure per servizi cloud e le server farm è possibile scegliere la metrica della risorsa in base alla quale eseguire il ridimensionamento. È tuttavia possibile scegliere metriche da risorse diverse.

Di seguito sono riportate informazioni dettagliate su come trovare ed elencare le metriche in base alle quali eseguire il ridimensionamento. Le informazioni sono applicabili anche al ridimensionamento di set di scalabilità di macchine virtuali.

## Metriche di calcolo
Per impostazione predefinita, la versione 2 delle macchine virtuali di Azure viene fornita con l'estensione della diagnostica configurata e le metriche seguenti abilitate.

- [Metriche guest per macchine virtuali Windows versione 2](#compute-metrics-for-windows-vm-v2-as-a-guest-os).
- [Metriche guest per macchine virtuali Linux versione 2](#compute-metrics-for-linux-vm-v2-as-a-guest-os).

Per visualizzare le metriche disponibili per la risorsa del set di scalabilità di macchine virtuali è possibile usare l'interfaccia della riga di comando, PowerShell o l'API `Get MetricDefinitions`.

Se si usano i set di scalabilità di macchine virtuali e una metrica specifica non è elencata, potrebbe essere *disabilitata* nell'estensione della diagnostica.

Se una metrica specifica non viene campionata o trasferita con la frequenza prevista, è possibile aggiornare la configurazione della diagnostica.

Se si verificano entrambe le condizioni precedenti, vedere l'articolo relativo all'[uso di PowerShell per abilitare Diagnostica di Azure in una macchina virtuale con Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) riguardo PowerShell per configurare e aggiornare l'estensione della diagnostica delle macchine virtuali di Azure e abilitare la metrica. Questo articolo include anche un file di configurazione della diagnostica di esempio.

### Metriche di calcolo per la versione 2 delle macchine virtuali Windows come sistema operativo guest

Quando si crea una nuova macchina virtuale (versione 2) in Azure, la diagnostica viene abilitata con l'uso dell'estensione della diagnostica.

Per generare un elenco delle metriche è possibile usare il comando seguente in PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

È possibile creare un avviso per le metriche seguenti.

|Nome della metrica|	Unità|
|---|---|
|\\Processor(\_Totale)\\% Tempo processore |Percentuale|
|\\Processor(\_Total)\\% Privileged Time |Percentuale|
|\\Processor(\_Total)\\% User Time |Percentuale|
|\\Processor Information(\_Total)\\Processor Frequency |Numero|
|\\System\\Processes|	Numero|
|\\Process(\_Total)\\Thread Count|	Numero|
|\\Process(\_Total)\\Handle Count |Numero|
|\\Memory\\% Committed Bytes In Use |Percentuale|
|\\Memory\\Available Bytes|	Byte|
|\\Memory\\Committed Bytes |Byte|
|\\Memory\\Commit Limit|	Byte|
|\\Memory\\Pool Paged Bytes|	Byte|
|\\Memory\\Pool Nonpaged Bytes|	Byte|
|\\PhysicalDisk(\_Total)\\% Disk Time|	Percentuale|
|\\PhysicalDisk(\_Total)\\% Disk Read Time|	Percentuale|
|\\PhysicalDisk(\_Total)\\% Disk Write Time|	Percentuale|
|\\DiscoFisico(\_Totale)\\Trasferimenti disco/secondo |Conteggio al secondo|
|\\PhysicalDisk(\_Total)\\Disk Reads/sec |Conteggio al secondo|
|\\PhysicalDisk(\_Total)\\Disk Writes/sec |Conteggio al secondo|
|\\PhysicalDisk(\_Total)\\Disk Bytes/sec |Byte al secondo|
|\\PhysicalDisk(\_Total)\\Disk Read Bytes/sec|	Byte al secondo|
|\\PhysicalDisk(\_Total)\\Disk Write Bytes/sec |Byte al secondo|
|\\PhysicalDisk(\_Total)\\Avg. Lunghezza coda disco|	Numero|
|\\PhysicalDisk(\_Total)\\Avg. Disk Read Queue Length|	Numero|
|\\PhysicalDisk(\_Total)\\Avg. Disk Write Queue Length |Numero|
|\\LogicalDisk(\_Total)\\% Free Space|	Percentuale|
|\\LogicalDisk(\_Total)\\Free Megabytes|	Numero|



### Metriche di calcolo per la versione 2 delle macchine virtuali Linux come sistema operativo guest

Quando si crea una nuova macchina virtuale (versione 2) in Azure, la diagnostica viene abilitata per impostazione predefinita con l'uso dell'estensione della diagnostica.

Per generare un elenco delle metriche è possibile usare il comando seguente in PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 È possibile creare un avviso per le metriche seguenti.

|Nome della metrica|	Unità|
|---|---|
|\\Memory\\AvailableMemory |Byte|
|\\Memory\\PercentAvailableMemory|	Percentuale|
|\\Memory\\UsedMemory|	Byte|
|\\Memory\\PercentUsedMemory|	Percentuale|
|\\Memory\\PercentUsedByCache |Percentuale|
|\\Memory\\PagesPerSec|	Conteggio al secondo|
|\\Memory\\PagesReadPerSec|	Conteggio al secondo|
|\\Memory\\PagesWrittenPerSec |Conteggio al secondo|
|\\Memory\\AvailableSwap |Byte|
|\\Memory\\PercentAvailableSwap|	Percentuale|
|\\Memory\\UsedSwap |Byte|
|\\Memory\\PercentUsedSwap|	Percentuale|
|\\Processor\\PercentIdleTime|	Percentuale|
|\\Processor\\PercentUserTime|	Percentuale|
|\\Processor\\PercentNiceTime |Percentuale|
|\\Processor\\PercentPrivilegedTime |Percentuale|
|\\Processor\\PercentInterruptTime|	Percentuale|
|\\Processor\\PercentDPCTime|	Percentuale|
|\\Processor\\PercentProcessorTime |Percentuale|
|\\Processor\\PercentIOWaitTime |Percentuale|
|\\PhysicalDisk\\BytesPerSecond|	Byte al secondo|
|\\PhysicalDisk\\ReadBytesPerSecond|	Byte al secondo|
|\\PhysicalDisk\\WriteBytesPerSecond|	Byte al secondo|
|\\PhysicalDisk\\TransfersPerSecond |Conteggio al secondo|
|\\PhysicalDisk\\ReadsPerSecond |Conteggio al secondo|
|\\PhysicalDisk\\WritesPerSecond |Conteggio al secondo|
|\\PhysicalDisk\\AverageReadTime|	Secondi|
|\\PhysicalDisk\\AverageWriteTime |Secondi|
|\\PhysicalDisk\\AverageTransferTime|	Secondi|
|\\PhysicalDisk\\AverageDiskQueueLength|	Numero|
|\\NetworkInterface\\BytesTransmitted |Byte|
|\\NetworkInterface\\BytesReceived |Byte|
|\\NetworkInterface\\PacketsTransmitted |Numero|
|\\NetworkInterface\\PacketsReceived |Numero|
|\\NetworkInterface\\BytesTotal |Byte|
|\\NetworkInterface\\TotalRxErrors|	Numero|
|\\NetworkInterface\\TotalTxErrors|	Numero|
|\\NetworkInterface\\TotalCollisions|	Numero|




## Metriche Web (server farm) usate comunemente

È possibile eseguire la scalabilità automatica anche in base a metriche di server Web comuni, come la lunghezza della coda HTTP. Il nome della metrica è **HttpQueueLength**. La sezione seguente riporta un elenco delle metriche di server farm (app Web) disponibili.

### Metriche di app Web

Per generare un elenco delle metriche di app Web è possibile usare il comando seguente in PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

È possibile generare avvisi o ridimensionare in base a queste metriche.

|Nome della metrica|	Unità|
|---|---|
|CpuPercentage|	Percentuale|
|MemoryPercentage |Percentuale|
|DiskQueueLength|	Numero|
|HttpQueueLength|	Numero|
|BytesReceived|	Byte|
|BytesSent|	Byte|


## Metriche di archiviazione usate comunemente
È possibile eseguire il ridimensionamento in base alla lunghezza della coda di archiviazione, ovvero il numero di messaggi nella coda di archiviazione. La lunghezza della coda di archiviazione è una metrica speciale e la soglia applicata corrisponde al numero di messaggi per ogni istanza. Ciò significa che se sono presenti due istanze e la soglia è impostata su 100, il ridimensionamento viene eseguito quando il numero totale di messaggi nella coda è pari a 200. Ad esempio, 100 messaggi per ogni istanza.

È possibile configurare questa opzione nel pannello **Impostazioni** del portale di Azure. Per i set di scalabilità di macchine virtuali, è possibile aggiornare l'impostazione Scalabilità automatica nel modello di Azure Resource Manager per l'uso di *metricName* come *ApproximateMessageCount* e passare l'ID della coda di archiviazione come *metricResourceUri*.


```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

## Metriche del bus di servizio usate comunemente

È possibile eseguire il ridimensionamento in base alla lunghezza della coda del bus di servizio, ovvero il numero di messaggi nella coda del bus di servizio. La lunghezza della coda del bus di servizio è una metrica speciale e la soglia specificata che viene applicata corrisponde al numero di messaggi per ogni istanza. Ciò significa che se sono presenti due istanze e la soglia è impostata su 100, il ridimensionamento viene eseguito quando il numero totale di messaggi nella coda è pari a 200. Ad esempio, 100 messaggi per ogni istanza.

Per i set di scalabilità di macchine virtuali, è possibile aggiornare l'impostazione Scalabilità automatica nel modello di Azure Resource Manager per l'uso di *metricName* come *ApproximateMessageCount* e passare l'ID della coda di archiviazione come *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Per il bus di servizio il concetto di gruppo di risorse non esiste, ma Azure Resource Manager consente di creare un gruppo di risorse predefinito per ogni area. Il gruppo di risorse è in genere nel formato "Default-ServiceBus-[area]". Ad esempio, "Default-ServiceBus-EastUS", "Default-ServiceBus-WestUS", "Default-ServiceBus-AustraliaEast" e così via.

<!---HONumber=AcomDC_0803_2016-->