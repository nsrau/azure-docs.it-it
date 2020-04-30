---
title: Ottenere le metriche in monitoraggio di Azure Application Insights
description: Informazioni su come usare efficacemente la chiamata getmetric () per acquisire metriche pre-aggregate localmente per le applicazioni .NET e .NET Core con monitoraggio di Azure Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/28/2020
ms.openlocfilehash: 6d0d05f13f592fc981d3df52d107b385bdbbb21e
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515287"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Raccolta di metriche personalizzate in .NET e .NET Core

Il monitoraggio di Azure Application Insights .NET e gli SDK di .NET Core hanno due metodi diversi per raccogliere le `TrackMetric()`metriche personalizzate `GetMetric()`, e. La differenza principale tra questi due metodi è l'aggregazione locale. `TrackMetric()`manca la pre-aggregazione `GetMetric()` mentre presenta una pre-aggregazione. L'approccio consigliato consiste nell'usare l'aggregazione, `TrackMetric()` pertanto non è più il metodo preferito per la raccolta di metriche personalizzate. Questo articolo illustra l'uso del metodo getmetric () e alcune delle ragioni di base del funzionamento.

## <a name="trackmetric-versus-getmetric"></a>TrackMetric rispetto a getmetric

`TrackMetric()`Invia dati di telemetria non elaborati che indicano una metrica. Non è efficiente inviare un singolo elemento di telemetria per ogni valore. `TrackMetric()`Invia dati di telemetria non elaborati che indicano una metrica. Non è efficiente inviare un singolo elemento di telemetria per ogni valore. `TrackMetric()`è anche inefficiente in termini di prestazioni, perché `TrackMetric(item)` ogni passa attraverso la pipeline SDK completa degli inizializzatori e dei processori di telemetria. A differenza `TrackMetric()`di `GetMetric()` , gestisce la pre-aggregazione locale per l'utente e quindi invia solo una metrica di riepilogo aggregata a un intervallo fisso di un minuto. Quindi, se è necessario monitorare attentamente alcune metriche personalizzate al secondo o anche al livello di millisecondo, è possibile eseguire questa operazione, mentre solo il costo del traffico di archiviazione e di rete è sufficiente per il monitoraggio ogni minuto. Questo consente anche di ridurre notevolmente il rischio di limitazione delle richieste poiché il numero totale di elementi di telemetria che devono essere inviati per una metrica aggregata è notevolmente ridotto.

In Application Insights la metrica personalizzata raccolta tramite `TrackMetric()` e `GetMetric()` non è soggetta al [campionamento](https://docs.microsoft.com/azure/azure-monitor/app/sampling). Il campionamento di metriche importanti può causare scenari in cui l'invio di avvisi potrebbe essere stato inaffidabile. Se non si campionano mai le metriche personalizzate, in genere è possibile essere certi che, quando vengono violate le soglie di avviso, viene generato un avviso.  Tuttavia, poiché le metriche personalizzate non vengono campionate, è possibile che si verifichino alcune problematiche.

Se è necessario tenere traccia delle tendenze in una metrica ogni secondo o a un intervallo ancora più granulare, questo può causare:

- Aumento dei costi di archiviazione dei dati. È previsto un costo associato alla quantità di dati inviati a monitoraggio di Azure. (Maggiore è il numero di dati inviati, maggiore è il costo complessivo del monitoraggio).
- Aumento del traffico di rete/sovraccarico delle prestazioni. In alcuni scenari questo potrebbe avere un costo in termini di prestazioni in termini di valuta e di applicazione.
- Rischio di limitazione dell'inserimento. Il servizio monitoraggio di Azure Elimina i punti dati ("limitazioni") quando l'app invia una frequenza molto elevata di dati di telemetria in un intervallo di tempo breve.

La limitazione delle richieste è particolarmente importante in quanto, ad esempio, il campionamento, la limitazione può causare la mancata presenza di avvisi perché la condizione per attivare un avviso potrebbe verificarsi localmente e quindi essere eliminata in corrispondenza dell'endpoint di inserimento a causa di una quantità eccessiva di dati inviati. Per questo motivo, per .NET e .NET Core non è consigliabile `TrackMetric()` usare, a meno che non sia stata implementata la logica di aggregazione locale. Se si sta provando a tenere traccia di ogni istanza di un evento in un determinato periodo di tempo, è [`TrackEvent()`](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics#trackevent) possibile trovare una soluzione migliore. Si tenga tuttavia presente che, a differenza delle metriche personalizzate, gli eventi personalizzati sono soggetti al campionamento. Ovviamente, è ancora possibile usare `TrackMetric()` anche senza scrivere la pre-aggregazione locale, ma se si è consapevoli dei problemi.

In sintesi `GetMetric()` , l'approccio consigliato è quello di pre-aggregazione, accumula i valori di tutte le chiamate Track () e invia un riepilogo/aggregazione una volta al minuto. Questo consente di ridurre significativamente il costo e il sovraccarico delle prestazioni inviando un minor numero di punti dati, raccogliendo comunque tutte le informazioni rilevanti.

> [!NOTE]
> Solo gli SDK .NET e .NET Core hanno un metodo getmetric (). Se si usa Java è possibile usare le [metriche del micrometro](https://docs.microsoft.com/azure/azure-monitor/app/micrometer-java) o `TrackMetric()`. Per Python è possibile usare [OpenCensus. stats](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#metrics) per inviare metriche personalizzate. Per JavaScript e node. js, è ancora possibile `TrackMetric()`usare, ma tenere presenti le avvertenze descritte nella sezione precedente.

## <a name="getting-started-with-getmetric"></a>Introduzione a getmetric

Per gli esempi, verrà usata un'applicazione di servizio di base di .NET Core 3,1 Worker. Se si vuole replicare esattamente l'ambiente di test usato con questi esempi, seguire i passaggi 1-6 dell' [articolo monitoraggio del servizio Worker](https://docs.microsoft.com/azure/azure-monitor/app/worker-service#net-core-30-worker-service-application) per aggiungere Application Insights a un modello di progetto di servizio di lavoro di base. Questi concetti si applicano a qualsiasi applicazione generale in cui è possibile usare l'SDK, tra cui app Web e app console.

### <a name="sending-metrics"></a>Invio di metriche

Sostituire il contenuto del `worker.cs` file con il codice seguente:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("computersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Se si esegue il codice precedente e si osservano i dati di telemetria inviati tramite la finestra di output di Visual Studio o uno strumento come Fiddler di Telerik, il ciclo while verrà visualizzato ripetutamente senza inviare dati di telemetria e quindi verrà inviato un singolo elemento di telemetria intorno al contrassegno di 60 secondi, che nel caso del test è simile al seguente. :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"computersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Questo singolo elemento di telemetria rappresenta un'aggregazione di 41 misure di metrica DISTINCT. Poiché lo stesso valore è stato inviato più volte, abbiamo una *deviazione standard (stDev)* pari a 0 con valori *massimi identici (max)* e *minimo (min)* . La proprietà *value* rappresenta una somma di tutti i singoli valori aggregati.

Se esaminiamo la risorsa Application Insights nell'esperienza log (Analytics), questo elemento di telemetria singolo avrà un aspetto simile al seguente:

![Visualizzazione Query Log Analytics](./media/get-metric/log-analytics.png)

> [!NOTE]
> Mentre l'elemento di telemetria non elaborato non contiene una proprietà o un campo Sum esplicito dopo l'inserimento, ne viene creato uno. In questo caso, entrambe `value` le `valueSum` proprietà e rappresentano la stessa cosa.

È anche possibile accedere ai dati di telemetria delle metriche personalizzati nella sezione [_metrica_](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) del portale. Sia come [metrica basata su log che come metrica personalizzata](pre-aggregated-metrics-log-metrics.md). Lo screenshot seguente è un esempio di basato su log. ![Visualizzazione Esplora metriche](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Riferimento alle metriche per la memorizzazione nella cache per l'utilizzo con velocità effettiva elevata

In alcuni casi, i valori delle metriche vengono osservati molto spesso. Ad esempio, un servizio con velocità effettiva elevata che elabora 500 richieste al secondo potrebbe voler emettere 20 metriche di telemetria per ogni richiesta. Ciò significa tenere traccia dei valori di 10.000 al secondo. In scenari con velocità effettiva elevata, gli utenti potrebbero dover aiutare l'SDK evitando alcune ricerche.

Ad esempio, in questo caso, nell'esempio precedente è stata eseguita una ricerca per un handle per la metrica "ComputersSold" e quindi è stato rilevato un valore osservato 42. Il punto di controllo, invece, può essere memorizzato nella cache per più chiamate di traccia:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Oltre a memorizzare nella cache l'handle della metrica, l'esempio precedente ha ridotto `Task.Delay` anche a 50 millisecondi, in modo che il ciclo verrebbe eseguito con maggiore `TrackValue()` frequenza, ottenendo 772 chiamate.

## <a name="multi-dimensional-metrics"></a>Metriche multidimensionali

Gli esempi nella sezione precedente mostrano le metriche con dimensioni zero. Le metriche possono anche essere multidimensionali. Attualmente sono supportate fino a 10 dimensioni.

 Di seguito è riportato un esempio di come creare una metrica unidimensionale:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

L'esecuzione di questo codice per almeno 60 secondi determinerà l'invio di tre elementi di telemetria distinti ad Azure, ognuno dei quali rappresenta l'aggregazione di uno dei tre fattori di forma. Come prima, è possibile esaminarli nella visualizzazione log (Analytics):

![Visualizzazione di log Analytics della metrica multidimensionale](./media/get-metric/log-analytics-multi-dimensional.png)

E in Esplora metriche:

![Metriche personalizzate](./media/get-metric/custom-metrics.png)

Si noterà tuttavia che non è possibile dividere la metrica in base alla nuova dimensione personalizzata oppure visualizzare la dimensione personalizzata con la visualizzazione metrica:

![Supporto per la suddivisione](./media/get-metric/splitting-support.png)

Per impostazione predefinita, le metriche multidimensionali all'interno dell'esperienza di Esplora metriche non sono attivate in Application Insights risorse. Per attivare questo comportamento, passare alla scheda utilizzo e costo stimato selezionando ["Abilita avvisi sulle dimensioni metriche personalizzate"](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>Come usare metricIdentifier quando sono presenti più di tre dimensioni

Attualmente sono supportate 10 dimensioni, tuttavia, maggiori di tre dimensioni richiedono l'utente `metricIdentifier`:

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

### <a name="enable-multi-dimensional-metrics"></a>Abilitare le metriche multidimensionali

 > Per abilitare le metriche multidimensionali per una risorsa di Application Insights, selezionare **utilizzo e costi stimati****metriche** > **personalizzate Abilita avvisi sulle dimensioni** > metriche personalizzate**OK**.

Una volta apportata la modifica e inviato nuovi dati di telemetria multidimensionali, sarà possibile **applicare la suddivisione**.

> [!NOTE]
> Solo le metriche appena inviate dopo l'accensione della funzionalità nel portale avranno dimensioni archiviate.

![Applicare la suddivisione](./media/get-metric/apply-splitting.png)

E visualizzare le aggregazioni delle metriche per ogni dimensione _FormFactor_ :

![Fattori di forma](./media/get-metric/formfactor.png)

## <a name="custom-metric-configuration"></a>Configurazione metrica personalizzata

Se si vuole modificare la configurazione della metrica, è necessario eseguire questa operazione nel punto in cui viene inizializzata la metrica.

### <a name="special-dimension-names"></a>Nomi di dimensione speciali

Le metriche non usano il contesto di telemetria del `TelemetryClient` utilizzato per accedervi. i nomi di dimensione speciali disponibili come costanti `MetricDimensionNames` nella classe sono la soluzione migliore per questa limitazione.

Le aggregazioni di metrica inviate dalla "dimensione speciale della richiesta dell'operazione"- **not** la metrica non `Context.Operation.Name` avranno la relativa impostazione su "operazione speciale". Mentre `TrackMetric()` o qualsiasi altro TrackXXX () sarà `OperationName` impostato correttamente su "operazione speciale".

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

In questa circostanza, utilizzare i nomi di dimensione speciali elencati nella `MetricDimensionNames` classe per specificare `TelemetryContext` i valori.

Ad esempio, quando l'aggregazione metrica risultante dall'istruzione successiva viene inviata all'endpoint Cloud Application Insights, il campo `Context.Operation.Name` dati verrà impostato su "operazione speciale":

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

I valori di questa dimensione speciale verranno copiati in `TelemetryContext` e non verranno utilizzati come dimensione "normale". Se si vuole anche usare una dimensione operativa per l'esplorazione della metrica normale, è necessario creare una dimensione separata a tale scopo:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Limitazione delle dimensioni e della serie temporale

 Per evitare che il sottosistema di telemetria usi accidentalmente le risorse, è possibile controllare il numero massimo di serie di dati per ogni metrica. I limiti predefiniti non sono più di 1000 serie di dati totali per ogni metrica e non più di 100 valori diversi per ogni dimensione.

 Nel contesto del limite della dimensione e della serie temporale viene usato `Metric.TrackValue(..)` per assicurarsi che vengano osservati i limiti. Se i limiti sono già stati raggiunti, `Metric.TrackValue(..)` restituirà "false" e il valore non verrà rilevato. In caso contrario, verrà restituito "true". Questa operazione è utile se i dati di una metrica provengono dall'input dell'utente.

Il `MetricConfiguration` costruttore accetta alcune opzioni per la gestione di serie diverse all'interno della rispettiva metrica e un oggetto di una `IMetricSeriesConfiguration` classe che implementa che specifica il comportamento di aggregazione per ogni singola serie della metrica:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit`numero massimo di serie temporali dei dati che una metrica può contenere. Una volta raggiunto questo limite, le chiamate `TrackValue()`a.
* `valuesPerDimensionLimit`limita il numero di valori distinct per dimensione in modo analogo.
* `restrictToUInt32Values`determina se devono essere rilevati solo i valori interi non negativi.

Di seguito è riportato un esempio di come inviare un messaggio per verificare se sono stati superati i limiti del limite:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Altre ](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)informazioni sul monitoraggio delle applicazioni del servizio Worker.
* Per altre informazioni sulle [metriche basate su log e pre-aggregate](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics).
* [Esplora metriche](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)
* Come abilitare Application Insights per [le applicazioni ASP.NET Core](asp-net-core.md)
* Come abilitare Application Insights per [le applicazioni ASP.NET](asp-net.md)
