---
title: Monitoraggio dell'hub Azure
description: Iniziare da qui per informazioni su come monitorare l'hub Azure
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: a8f9c46487422deb4513768dff04f559af952f7b
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926258"
---
# <a name="monitoring-azure-iot-hub"></a>Monitoraggio dell'hub Azure

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati dall'hub Azure e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare e generare avvisi su questi dati.

## <a name="monitor-overview"></a>Panoramica di Monitoraggio

La pagina di **Panoramica** nel portale di Azure per ogni hub degli elementi del tutto include grafici che forniscono alcune metriche di utilizzo, ad esempio il numero di messaggi usati e il numero di dispositivi connessi all'hub.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Grafici metrici predefiniti nella pagina Panoramica dell'hub Internet.":::

Queste informazioni sono utili, ma rappresentano solo una piccola quantità di dati di monitoraggio disponibili per un hub Internet. Alcuni dati di monitoraggio vengono raccolti automaticamente ed è disponibile per l'analisi non appena si crea l'hub Internet delle cose. Con alcune configurazioni è possibile abilitare altri tipi di raccolta dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure

Hub Azure per la creazione di dati di monitoraggio tramite [monitoraggio di Azure](/azure/azure-monitor/overview), un servizio di monitoraggio completo dello stack in Azure, che offre un set completo di funzionalità per il monitoraggio delle risorse di Azure, oltre alle risorse in altri cloud e in locale.

Iniziare con l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](/azure/azure-monitor/insights/monitor-azure-resource), che descrive i concetti seguenti:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti per l'hub Azure Internet e fornendo esempi per la configurazione della raccolta di dati e l'analisi di questi dati con gli strumenti di Azure.

## <a name="monitoring-data"></a>Dati di monitoraggio

Hub di Azure è in grado di raccogliere gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritte in [monitoraggio dei dati dalle risorse di Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources).

Per informazioni dettagliate sulle metriche e i log creati dall'hub Internet di Azure, vedere [monitoraggio dei dati di riferimento dell'hub Azure](monitor-iot-hub-reference.md) .

> [!IMPORTANT]
> Gli eventi generati dal servizio hub Internet delle cose usando i log delle risorse di monitoraggio di Azure non sono necessariamente affidabili o ordinati. Alcuni eventi possono andare persi o recapitati non in ordine. Anche i log delle risorse non sono pensati per essere in tempo reale e potrebbero essere necessari alcuni minuti prima che gli eventi vengano registrati nella destinazione scelta.

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma e il log attività vengono raccolti e archiviati automaticamente, ma possono essere indirizzati ad altri percorsi tramite un'impostazione di diagnostica.

I log delle risorse non vengono raccolti e archiviati fino a quando non si crea un'impostazione di diagnostica e la si instrada a una o più posizioni.

Le metriche e i log possono essere indirizzati a diverse posizioni, tra cui:
- L'archivio dei log di monitoraggio di Azure viene archiviato tramite un'area di lavoro Log Analytics associata. È possibile analizzarli con Log Analytics.
- Archiviazione di Azure per l'archiviazione e l'analisi offline 
- Un endpoint di hub eventi in cui possono essere letti da applicazioni esterne, ad esempio strumenti SIEM di terze parti.

In portale di Azure, è possibile selezionare **le impostazioni di diagnostica** in **monitoraggio** nel riquadro sinistro dell'hub Internet e quindi aggiungere l' **impostazione di diagnostica** per creare le impostazioni di diagnostica con ambito per i log e le metriche della piattaforma emessi dall'hub Internet delle cose.

Lo screenshot seguente mostra un'impostazione di diagnostica per il routing delle *operazioni di connessione* del tipo di log delle risorse e di tutte le metriche della piattaforma a un'area di lavoro log Analytics.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Grafici metrici predefiniti nella pagina Panoramica dell'hub Internet.":::

Vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](/azure/azure-monitor/platform/diagnostic-settings) per il processo dettagliato per la creazione di un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell. Quando si crea un'impostazione di diagnostica, è necessario specificare le categorie di log da raccogliere. Le categorie per l'hub Azure Internet sono elencate in [log delle risorse nel riferimento ai dati dell'hub Azure Internet Monitoring](monitor-iot-hub-reference.md#resource-logs).

Quando si indirizzano le metriche della piattaforma dell'hub Internet ad altre località, tenere presente quanto segue:

- Le metriche della piattaforma seguenti non possono essere esportate tramite le impostazioni di diagnostica: *dispositivi connessi (anteprima)* e *totale dispositivi (anteprima)* .

- Le metriche multidimensionali, ad esempio alcune metriche di [routing](monitor-iot-hub-reference.md#routing-metrics), vengono attualmente esportate come metriche unidimensionali bidimensionali aggregate tra i valori delle dimensioni. Per informazioni dettagliate, vedere [esportazione di metriche della piattaforma in altre posizioni](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche per l'hub Internet Azure con le metriche di altri servizi di Azure usando Esplora metriche aprendo le **metriche** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](/azure/azure-monitor/platform/metrics-getting-started).

In portale di Azure è possibile selezionare le **metriche** in **monitoraggio** nel riquadro a sinistra dell'hub Internet per aprire Esplora metriche con ambito, per impostazione predefinita, sulle metriche della piattaforma emesse dall'hub Internet delle cose:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Grafici metrici predefiniti nella pagina Panoramica dell'hub Internet.":::

Per un elenco delle metriche della piattaforma raccolte per l'hub Azure, vedere [metriche nel monitoraggio informazioni di riferimento sui dati dell'hub Azure](monitor-iot-hub-reference.md#metrics). Per un elenco delle metriche della piattaforma raccolte per tutti i servizi di Azure, vedere [metriche supportate con monitoraggio di Azure](/azure/azure-monitor/platform/metrics-supported).

Per le metriche della piattaforma dell'hub Internet che vengono raccolte in unità di conteggio, alcune aggregazioni potrebbero non essere disponibili o utilizzabili. Per altre informazioni, vedere [le aggregazioni supportate nel monitoraggio riferimento ai dati dell'hub Azure](monitor-iot-hub-reference.md#supported-aggregations).

Alcune metriche dell'hub, come le [metriche di routing](monitor-iot-hub-reference.md#routing-metrics), sono multidimensionali. Per queste metriche, è possibile applicare [filtri](/azure-monitor/platform/metrics-charts#apply-filters-to-charts) e [dividere](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart) i grafici in base a una dimensione.

## <a name="analyzing-logs"></a>Analisi dei log

I dati nei log di monitoraggio di Azure vengono archiviati in tabelle in cui ogni tabella dispone di un proprio set di proprietà univoche. I dati di queste tabelle sono associati a un'area di lavoro di Log Analytics e possono essere sottoposti a query in Log Analytics. Per altre informazioni sui log di monitoraggio di Azure, vedere [Panoramica dei log di monitoraggio](/azure/azure-monitor/platform/data-platform-logs) di Azure nella documentazione di monitoraggio di Azure. 

Per indirizzare i dati ai log di monitoraggio di Azure, è necessario creare un'impostazione di diagnostica per inviare i log delle risorse o le metriche della piattaforma a un'area di lavoro Log Analytics. Per altre informazioni, vedere [raccolta e routing](#collection-and-routing).

In portale di Azure è possibile selezionare i **log** sotto il **monitoraggio** nel riquadro sinistro dell'hub Internet per eseguire log Analytics query con ambito, per impostazione predefinita, sui log e sulle metriche raccolti nei log di monitoraggio di Azure per l'hub Internet.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Grafici metrici predefiniti nella pagina Panoramica dell'hub Internet.":::

Per un elenco delle tabelle usate dai log di monitoraggio di Azure e di cui è necessario eseguire query per Log Analytics, vedere log di monitoraggio di Azure [tabelle nel monitoraggio informazioni di riferimento sui dati dell'hub Azure](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Tutti i log delle risorse in monitoraggio di Azure hanno gli stessi campi seguiti dai campi specifici del servizio. Lo schema comune è illustrato nello schema del [log delle risorse di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-schema#top-level-resource-logs-schema). È possibile trovare lo schema e le categorie dei log delle risorse raccolti per l'hub Azure Internet nei [log delle risorse nel monitoraggio riferimento ai dati dell'hub Azure](monitor-iot-hub-reference.md#resource-logs).

Il [log attività](/azure/azure-monitor/platform/activity-log) è un log di piattaforma in Azure che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione. È possibile visualizzarlo in modo indipendente o instradarlo ai log di monitoraggio di Azure, in cui è possibile eseguire query molto più complesse usando Log Analytics.  

Quando si indirizzano le metriche della piattaforma dell'hub Internet ai log di monitoraggio di Azure, tenere presente quanto segue:

- Le metriche della piattaforma seguenti non possono essere esportate tramite le impostazioni di diagnostica: *dispositivi connessi (anteprima)* e *totale dispositivi (anteprima)* .

- Le metriche multidimensionali, ad esempio alcune metriche di [routing](monitor-iot-hub-reference.md#routing-metrics), vengono attualmente esportate come metriche unidimensionali bidimensionali aggregate tra i valori delle dimensioni. Per informazioni dettagliate, vedere [esportazione di metriche della piattaforma in altre posizioni](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations).

Per alcune query comuni con l'hub Internet, vedere [esempi di query kusto](#sample-kusto-queries). Per informazioni dettagliate sull'uso delle query di Log Analytics, vedere [Panoramica delle query di log in monitoraggio di Azure](/azure/azure-monitor/log-query/log-query-overview).

### <a name="sdk-version-in-iot-hub-logs"></a>Versione dell'SDK nei log dell'hub Internet

Alcune operazioni nei log delle risorse dell'hub Internet che restituiscono una `sdkVersion` proprietà nell' `properties` oggetto. Per queste operazioni, quando un dispositivo o un'app back-end usa uno degli SDK di Azure, questa proprietà contiene informazioni sull'SDK usato, la versione dell'SDK e la piattaforma in cui è in esecuzione l'SDK. Nell'esempio seguente viene illustrata la `sdkVersion` proprietà generata per un' [`deviceConnect`](monitor-iot-hub-reference.md#connections) operazione quando si usa l'SDK del dispositivo Node.js: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Di seguito è riportato un esempio del valore generato per .NET (C#) SDK: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

La tabella seguente mostra il nome dell'SDK usato per i diversi SDK di Azure:

| Nome dell'SDK nella proprietà sdkVersion | Linguaggio |
|----------|----------|
| .NET | .NET (C#) |
| Microsoft. Azure. Devices | .NET (C#) Service SDK |
| Microsoft. Azure. Devices. client | SDK per dispositivi .NET (C#) |
| iothubclient | C o Python V1 (deprecato) SDK per dispositivi |
| iothubserviceclient | C o Python V1 (deprecato) Service SDK |
| Azure-Azure-Device-iothub-py | SDK per dispositivi Python |
| azure-iot-device | SDK per dispositivi Node.js |
| azure-iothub | SDK del servizio Node.js |
| com. Microsoft. Azure. iothub-Java-client | SDK per dispositivi Java |
| com. Microsoft. Azure. iothub. Service. SDK | Java Service SDK |
| com. Microsoft. Azure. Sdk. Internet | SDK per dispositivi Java |
| com. Microsoft. Azure. Sdk. Internet | Java Service SDK |
| C | C incorporato |
| C + (OSSimplified = Azure RTO) | Azure RTOS |

È possibile estrarre la proprietà versione SDK quando si eseguono query sui log delle risorse dell'hub Internet. Ad esempio, la query seguente estrae la proprietà della versione dell'SDK (e l'ID del dispositivo) dalle proprietà restituite dalle operazioni di connessione. Queste due proprietà vengono scritte nei risultati con l'ora dell'operazione e l'ID della risorsa dell'hub Internet a cui si connette il dispositivo.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Query Kusto di esempio

> [!IMPORTANT]
> Quando si selezionano i **log** dal menu dell'hub Internet delle cose, log Analytics viene aperto con l'ambito della query impostato sull'hub degli eventi Internet corrente. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Se si vuole eseguire una query che includa dati da altri hub o dati di altri servizi di Azure, selezionare **log** dal menu **monitoraggio di Azure** . Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](/azure/azure-monitor/log-query/scope/).

Di seguito sono riportate le query che è possibile usare per monitorare l'hub Internet.

- Errori di connettività: identificare gli errori di connessione del dispositivo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Errori di limitazione: identificare i dispositivi che hanno effettuato il maggior numero di richieste causando errori di limitazione.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Endpoint non recapitabili: identificare gli endpoint inattivi o non integri per il numero di volte in cui è stato segnalato il problema, nonché il motivo per cui.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Riepilogo errori: numero di errori in tutte le operazioni per tipo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Dispositivi connessi di recente: elenco di dispositivi in cui l'hub Internet ha visto la connessione nel periodo di tempo specificato.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- Versione SDK dei dispositivi: l'elenco dei dispositivi e le relative versioni SDK per le connessioni del dispositivo o per le operazioni del dispositivo per il cloud gemello.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Leggere i log da Hub eventi di Azure

Dopo aver configurato la registrazione degli eventi tramite le impostazioni di diagnostica, è possibile creare applicazioni che leggono i log in modo che sia possibile eseguire azioni sulla base delle informazioni in essi contenuti. Questo codice di esempio recupera i log da un hub eventi:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Avvisi

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi nel sistema prima che i clienti li notino. È possibile impostare avvisi su [metriche](/azure/azure-monitor/platform/alerts-metric-overview), [log](/azure/azure-monitor/platform/alerts-unified-log)e [log attività](/azure/azure-monitor/platform/activity-log-alerts). Tipi diversi di avvisi presentano vantaggi e svantaggi.

Quando si crea una regola di avviso in base alle metriche della piattaforma, tenere presente che per le metriche della piattaforma dell'hub tutto raccolte in unità di conteggio, alcune aggregazioni potrebbero non essere disponibili o utilizzabili. Per altre informazioni, vedere [le aggregazioni supportate nel monitoraggio riferimento ai dati dell'hub Azure](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="next-steps"></a>Passaggi successivi

- Vedere [monitoraggio del riferimento ai dati dell'hub Internet Azure per informazioni](monitor-iot-hub-reference.md) di riferimento su metriche, log e altri importanti valori creati da [nome servizio].

- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](/azure/azure-monitor/insights/monitor-azure-resource).
