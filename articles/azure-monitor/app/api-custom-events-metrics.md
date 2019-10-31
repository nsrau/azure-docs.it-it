---
title: API di Application Insights per metriche ed eventi personalizzati | Microsoft Docs
description: Inserire alcune righe di codice nell'app desktop o per dispositivi, nella pagina Web o nel servizio per tenere traccia dell'utilizzo e diagnosticare i problemi.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/27/2019
ms.openlocfilehash: 515d1da5333bb29237baa4bd941275f32ba754d3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161569"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API di Application Insights per metriche ed eventi personalizzati

Inserire alcune righe di codice nell'applicazione per scoprire come viene usato dagli utenti o per agevolare la diagnosi dei problemi. È possibile inviare i dati di telemetria dalle app desktop e per dispositivi, dai client Web e dai server Web. Usare l'API di telemetria principale di [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) per inviare metriche ed eventi personalizzati e le versioni personalizzate dei dati di telemetria standard. Questa API è la stessa utilizzata dagli agenti di raccolta dati di Application Insights standard.

## <a name="api-summary"></a>Riepilogo dell'API

L'API principale è uniforme in tutte le piattaforme, a parte alcune variazioni, ad esempio `GetMetric` (solo .NET).

| Metodo | Usato per |
| --- | --- |
| [`TrackPageView`](#page-views) |Pagine, schermate, pannelli o form. |
| [`TrackEvent`](#trackevent) |Azioni dell'utente e altri eventi. Usato per tenere traccia del comportamento dell'utente o per monitorare le prestazioni. |
| [`GetMetric`](#getmetric) |Metriche multidimensionali e con dimensioni pari a zero, aggregazione configurata a livello centrale, solo C#. |
| [`TrackMetric`](#trackmetric) |Misurazioni delle prestazioni, ad esempio la lunghezza della coda, non correlate a eventi specifici. |
| [`TrackException`](#trackexception) |Registrare le eccezioni per la diagnosi. Tenere traccia del punto in cui si verificano in relazione ad altri eventi ed esaminare le analisi dello stack. |
| [`TrackRequest`](#trackrequest) |Registrare la frequenza e la durata delle richieste del server per l'analisi delle prestazioni. |
| [`TrackTrace`](#tracktrace) |Messaggi nei log di diagnostica. È anche possibile acquisire log di terze parti. |
| [`TrackDependency`](#trackdependency) |Registrare la durata e la frequenza delle chiamate ai componenti esterni da cui dipende l'app. |

È possibile [associare proprietà e metriche](#properties) alla maggior parte di queste chiamate di telemetria.

## <a name="prep"></a>Prima di iniziare

Se non si ha ancora un riferimento in Application Insights SDK:

* Aggiungere Application Insights SDK al progetto:

  * [Progetto ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Progetto ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
  * [Progetto Java](../../azure-monitor/app/java-get-started.md)
  * [Progetto Node.js](../../azure-monitor/app/nodejs.md)
  * [JavaScript in ogni pagina Web](../../azure-monitor/app/javascript.md) 
* Nel dispositivo o nel codice del server Web includere:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Ottenere un'istanza di TelemetryClient

Ottenere un'istanza di `TelemetryClient` (tranne che in JavaScript nelle pagine Web):

Per [ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) app e [non http/Worker per le app .NET/.NET Core](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) , è consigliabile ottenere un'istanza di `TelemetryClient` dal contenitore di inserimento delle dipendenze, come illustrato nella rispettiva documentazione.

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient è thread-safe.

Per i progetti ASP.NET e Java, vengono acquisite automaticamente le richieste HTTP in ingresso. È possibile creare istanze aggiuntive di TelemetryClient per altri moduli dell'app. Ad esempio potrebbe esserci un’istanza di TelemetryClient in una classe middleware per segnalare eventi di logica di business. È possibile impostare proprietà quali ID utente e ID dispositivo per identificare il computer. Queste informazioni sono associate a tutti gli eventi inviati dall'istanza.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

Nei progetti Node.js è possibile usare `new applicationInsights.TelemetryClient(instrumentationKey?)` per creare una nuova istanza, ma questo è consigliato solo per scenari che richiedono la configurazione isolata dal `defaultClient` singleton.

## <a name="trackevent"></a>TrackEvent

In Application Insights un *evento personalizzato* è un punto dati che è possibile visualizzare in [Esplora metriche](../../azure-monitor/app/metrics-explorer.md) come conteggio aggregato e in [Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md) come singole occorrenze. Non è correlato a MVC o ad altri "eventi" del framework.

Inserire chiamate `TrackEvent` nel codice per contare i vari eventi. La frequenza d'uso di una particolare funzionalità, la frequenza di raggiungimento di obiettivi specifici o la frequenza di particolari tipi di errore.

Ad esempio, in un'app di gioco è possibile inviare un evento ogni volta che un utente vince il gioco:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Eventi personalizzati in Analytics

I dati di telemetria sono disponibili nella tabella `customEvents` in [Analytics di Application Insights](analytics.md). Ogni riga rappresenta una chiamata a `trackEvent(..)` nell'app in uso.

Se il [campionamento](../../azure-monitor/app/sampling.md) è attivo, la proprietà itemCount mostra un valore maggiore di 1. Per esempio itemCount==10 indica che su 10 chiamate a trackEvent(), il processo di campionamento ne trasmette solo una. Per ottenere un conteggio corretto degli eventi personalizzati, è quindi consigliabile usare codice come `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>esempi

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft. ApplicationInsights. TelemetryClient. TrackMetric non è il metodo preferito per l'invio di metriche. Le metriche devono essere sempre pre-aggregate per un periodo di tempo prima dell'invio. Usare uno degli overload GetMetric(..) per ottenere un oggetto metrica per l'accesso alle funzionalità di pre-aggregazione dell'SDK. Se si implementa la propria logica di pre-aggregazione, è possibile usare il metodo TrackMetric () per inviare le aggregazioni risultanti. Se l'applicazione richiede ogni volta l'invio di un elemento di telemetria separato senza aggregazione nel tempo, è probabile che si tratti di un caso d'uso per la telemetria degli eventi. Vedere TelemetryClient.TrackEvent (Microsoft.Applicationlnsights.DataContracts.EventTelemetry).

Application Insights è in grado di creare grafici in base a metriche non sono associate a determinati eventi. Ad esempio, è possibile monitorare la lunghezza di una coda a intervalli regolari. Grazie alle metriche, le singole misurazioni sono meno interessanti rispetto alle variazioni e alle tendenze, i grafici statistici risultano pertanto utili.

Per inviare le metriche ad Application Insights, è possibile usare l'API `TrackMetric(..)`. Per inviare le metriche è possibile procedere in due modi:

* Valore singolo. Ogni volta che si esegue una misurazione nell'applicazione, si invia il valore corrispondente ad Application Insights. Ad esempio, si supponga di avere una metrica che descrive il numero di elementi in un contenitore. Durante un periodo di tempo specifico, inserire prima tre elementi nel contenitore e poi rimuoverne due. Di conseguenza, è necessario chiamare `TrackMetric` due volte: prima passando il valore `3` e poi il valore `-2`. Application Insights memorizza entrambi i valori per conto dell'utente.

* Aggregazione. Quando si usano le metriche non si considera mai una sola misura. È importante invece il riepilogo delle operazioni eseguite in un periodo di tempo specifico. Tale riepilogo viene chiamato _aggregazione_. Nell'esempio precedente la somma della metrica di aggregazione per quel periodo di tempo è `1` e il conteggio dei valori della metrica è `2`. Quando si usa l'approccio di aggregazione, si chiama `TrackMetric` solo una volta per periodo di tempo e si inviano i valori di aggregazione. Questo è l'approccio consigliato in quanto può ridurre notevolmente i costi e le prestazioni generali inviando meno punti dati ad Application Insights, durante la raccolta di tutte le informazioni pertinenti.

### <a name="examples"></a>esempi

#### <a name="single-values"></a>Valori singoli

Per inviare un singolo valore di metrica:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Metriche personalizzate in Analytics

I dati di telemetria sono disponibili nella tabella `customMetrics` in [Analytics di Application Insights](analytics.md). Ogni riga rappresenta una chiamata a `trackMetric(..)` nell'app in uso.

* `valueSum`: somma delle misurazioni. Per ottenere il valore medio, dividere per `valueCount`.
* `valueCount`: numero delle misurazioni aggregate nella chiamata `trackMetric(..)`.

## <a name="page-views"></a>Visualizzazioni pagina

In un'app per dispositivo o pagine Web i dati di telemetria delle visualizzazioni pagina vengono inviati per impostazione predefinita quando viene caricata ogni schermata o pagina. È tuttavia possibile modificare questa impostazione per tenere traccia delle visualizzazioni pagina in momenti diversi o aggiuntivi. Ad esempio, in un'app che visualizza schede o pannelli, è possibile tenere traccia di una "pagina" ogni volta che l'utente apre un nuovo pannello.

I dati relativi a utente e sessione vengono inviati come proprietà insieme alle visualizzazioni pagina, in modo che i grafici utente e sessione si attivino in presenza dei dati di telemetria delle visualizzazioni pagina.

### <a name="custom-page-views"></a>Visualizzazioni pagina personalizzate

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Se sono presenti alcune schede in pagine HTML diverse, è possibile specificare anche l'URL:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Temporizzazione delle visualizzazioni delle pagine

Per impostazione predefinita gli intervalli di tempo indicati come **Tempo di caricamento della visualizzazione pagina** sono misurati dal momento in cui il browser invia la richiesta al momento della chiamata dell'evento di caricamento pagina del browser.

In alternativa, è possibile:

* Impostare una durata esplicita nella chiamata di [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview): `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Usare le chiamate relative ai tempi di visualizzazione della pagina `startTrackPage` e `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Il nome che si usa come primo parametro associa le chiamate di avvio e arresto. Il valore predefinito corrisponde al nome della pagina corrente.

I tempi di caricamento delle pagine visualizzati in Esplora metriche sono calcolati in base all'intervallo tra la chiamata di avvio e la chiamata di arresto. È possibile specificare l'intervallo effettivo calcolato desiderato.

### <a name="page-telemetry-in-analytics"></a>Dati di telemetria della pagina in Analytics

In [Analytics](analytics.md) due tabelle mostrano i dati delle operazioni del browser:

* La tabella `pageViews` contiene i dati relativi al titolo della pagina e all'URL
* La tabella `browserTimings` contiene i dati sulle prestazioni del client, ad esempio il tempo impiegato per elaborare i dati in ingresso

Per trovare il tempo necessario al browser per elaborare pagine diverse:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Per individuare le popolarità dei diversi browser:

```kusto
pageViews
| summarize count() by client_Browser
```

Per associare le visualizzazioni di pagina alle chiamate AJAX, unirle alle dipendenze:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

Il server SDK usa TrackRequest per registrare le richieste HTTP.

È anche possibile chiamarlo manualmente se si vogliono simulare le richieste in un contesto in cui il modulo del servizio Web non è in esecuzione.

Il modo consigliato per inviare dati di telemetria della richiesta è quello in cui la richiesta agisce come un <a href="#operation-context">contesto dell'operazione</a>.

## <a name="operation-context"></a>Contesto dell'operazione

È possibile mettere in correlazione gli elementi di telemetria associandoli al contesto dell'operazione. Il modulo di rilevamento delle richieste standard esegue questa operazione per le eccezioni e gli altri eventi inviati durante l'elaborazione di una richiesta HTTP. In [Ricerca](../../azure-monitor/app/diagnostic-search.md) e [Analisi](analytics.md) è possibile trovare facilmente gli eventi associati alla richiesta usando l'ID operazione.

Per altre informazioni sulla correlazione, vedere [Correlazione di dati di telemetria in Application Insights](../../azure-monitor/app/correlation.md).

Quando si tiene traccia dei dati di telemetria manualmente, il modo più semplice per garantire la correlazione dei dati di telemetria consiste nell'usare questo modello:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Oltre a impostare un contesto operativo, `StartOperation` crea un elemento di telemetria del tipo specificato. Invia l'elemento di telemetria quando si elimina l'operazione o si chiama esplicitamente `StopOperation`. Se si usa `RequestTelemetry` come tipo di telemetria, la sua durata viene impostata sull'intervallo di tempo compreso tra l'avvio e l'arresto.

Gli elementi di telemetria segnalati in un ambito dell'operazione diventano elementi "figlio" di tale operazione. I contesti dell'operazione possono essere annidati.

In Ricerca il contesto dell'operazione viene usato per creare l'elenco di **elementi correlati**:

![Elementi correlati](./media/api-custom-events-metrics/21.png)

Per altre informazioni sulle operazioni di rilevamento personalizzate, vedere [Tenere traccia delle operazioni personalizzate con Application Insights .NET SDK](../../azure-monitor/app/custom-operations-tracking.md).

### <a name="requests-in-analytics"></a>Richieste in Analytics

In [Analytics di Application Insights](analytics.md) le richieste vengono visualizzate nella tabella `requests`.

Se il [campionamento](../../azure-monitor/app/sampling.md) è attivo, la proprietà itemCount mostrerà un valore maggiore di 1. Per esempio itemCount==10 indica che su 10 chiamate a trackRequest(), il processo di campionamento ne trasmette solo una. Per ottenere un conteggio corretto delle richieste e della durata media segmentato in base ai nomi della richiesta, usare un codice, ad esempio:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Inviare le eccezioni ad Application Insights:

* Per [contarle](../../azure-monitor/app/metrics-explorer.md), come indicazione della frequenza di un problema.
* Per [esaminare le singole occorrenze](../../azure-monitor/app/diagnostic-search.md).

I report includono le analisi dello stack.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Gli SDK rilevano molte eccezioni automaticamente, quindi non è sempre necessario richiamare TrackException in modo esplicito.

* ASP.NET: [Scrivere codice per intercettare le eccezioni](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: le [eccezioni vengono rilevate automaticamente](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Le eccezioni vengono rilevate automaticamente. Se si vuole disabilitare la raccolta automatica, aggiungere una riga al frammento di codice che si inserisce nelle pagine Web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Eccezioni in Analytics

In [Analytics di Application Insights](analytics.md) le eccezioni vengono visualizzate nella tabella `exceptions`.

Se il [campionamento](../../azure-monitor/app/sampling.md) è attivo, la proprietà `itemCount` mostra un valore maggiore di 1. Per esempio itemCount==10 indica che su 10 chiamate a trackException(), il processo di campionamento ne trasmette solo una. Per ottenere un conteggio corretto delle eccezioni segmentate per tipo di eccezione, usare un codice, ad esempio:

```kusto
exceptions
| summarize sum(itemCount) by type
```

La maggior parte delle informazioni importanti dello stack è già stata estratta in variabili distinte, ma è possibile separare la struttura `details` per ottenerne altre. Poiché si tratta di una struttura dinamica, è necessario eseguire il cast del risultato per il tipo previsto. ad esempio:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Per associare le richieste alle eccezioni correlate, è possibile usare un join:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Usare TrackTrace per diagnosticare i problemi mediante l'invio di una traccia di navigazione ad Application Insights. È possibile inviare blocchi di dati di diagnostica e controllarli in [Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md).

In .NET gli [adattatori di log](../../azure-monitor/app/asp-net-trace-logs.md) usano questa API per inviare i log di terze parti al portale.

In Java per [logger Standard come Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) usano Application Insights Log4j o Logback Appenders per inviare i log di terze parti al portale.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*JavaScript lato client/browser*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: AI.SeverityLevel)
```

Registrare un evento di diagnostica, ad esempio inserire o rimuovere un metodo.

 Parametro | Description
---|---
`message` | Dati di diagnostica. Possono essere molto più lunghi di un nome.
`properties` | Mappa della stringa alla stringa: dati aggiuntivi usati per [filtrare le eccezioni](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) nel portale. Per impostazione predefinita è vuoto.
`severityLevel` | Valori supportati: [SeverityLevel. TS](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

È possibile eseguire ricerche nel contenuto del messaggio, ma, a differenza dei valori delle proprietà, non è possibile filtrarlo.

Il limite delle dimensioni per `message` è molto superiore al limite per le proprietà.
Un vantaggio di TrackTrace è che è possibile inserire dati relativamente lunghi nel messaggio. Ad esempio è possibile codificare dati POST.  

È anche possibile aggiungere al messaggio un livello di gravità. E come per altri tipi di dati di telemetria è possibile aggiungere valori di proprietà utili per filtrare o cercare set di tracce diversi. ad esempio:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

In [Ricerca](../../azure-monitor/app/diagnostic-search.md) sarà possibile filtrare facilmente tutti i messaggi di un determinato livello di gravità relativi a un database specifico.

### <a name="traces-in-analytics"></a>Tracce in Analytics

In [Analytics di Application Insights](analytics.md) le chiamate a TrackTrace vengono visualizzate nella tabella `traces`.

Se il [campionamento](../../azure-monitor/app/sampling.md) è attivo, la proprietà itemCount mostra un valore maggiore di 1. Per esempio itemCount==10 indica che, su 10 chiamate di `trackTrace()`, il processo di campionamento ne trasmette solo una. Per ottenere un conteggio corretto delle chiamate delle tracce, si consiglia di usare un codice, ad esempio `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

Usare la chiamata di TrackDependency per rilevare i tempi di risposta e le percentuali di successo delle chiamate a un frammento di codice esterno. I risultati vengono visualizzati nei grafici dipendenze nel portale. Il frammento di codice seguente deve essere aggiunto ogni volta che viene effettuata una chiamata alle dipendenze.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
}
finally {
    long endTime = System.currentTimeMillis();
    long delta = endTime - startTime;
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    telemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*JavaScript*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Tenere presente che il server SDK include un [modulo dipendenza](../../azure-monitor/app/asp-net-dependencies.md) che consente di individuare e tracciare alcune chiamate di dipendenza automaticamente, ad esempio a database e API REST. È necessario installare un agente nel server per consentire il funzionamento del modulo. 

In Java, alcune chiamate a dipendenze possono essere automaticamente rilevate tramite [agente Java](../../azure-monitor/app/java-agent.md).

Usare questa chiamata se si vuole tenere traccia di chiamate che il rilevamento automatico non intercetta o se non si vuole installare l'agente.

Per disattivare il modulo standard per il rilevamento delle dipendenze in C#, modificare il file [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ed eliminare il riferimento`DependencyCollector.DependencyTrackingTelemetryModule`. In Java, non installare l'agente java se non si desidera raccogliere le dipendenze standard automaticamente.

### <a name="dependencies-in-analytics"></a>Dipendenze in Analytics

In [Analytics di Application Insights](analytics.md) le chiamate a trackDependency vengono visualizzate nella tabella `dependencies`.

Se il [campionamento](../../azure-monitor/app/sampling.md) è attivo, la proprietà itemCount mostra un valore maggiore di 1. Per esempio itemCount==10 indica che su 10 chiamate a trackDependency(), il processo di campionamento ne trasmette solo una. Per ottenere un conteggio corretto delle dipendenze segmentate per componente di destinazione, usare un codice, ad esempio:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Per associare le dipendenze alle richieste correlate, è possibile usare un join:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Scaricamento dei dati

In genere, l'SDK invia i dati a intervalli fissi (in genere 30 secondi) o ogni volta che il buffer è pieno (in genere 500 elementi). In alcuni casi tuttavia è possibile che si voglia scaricare il buffer, ad esempio se si sta usando l'SDK in un'applicazione che si arresta.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

La funzione è asincrona per il [canale di telemetria del server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

In teoria, il metodo Flush () deve essere utilizzato nell'attività di arresto dell'applicazione.

## <a name="authenticated-users"></a>utenti autenticati

In un'app Web gli utenti sono identificati dai cookie per impostazione predefinita. Un utente può essere conteggiato più volte se accede all'app da un computer o da un browser diverso o se elimina i cookie.

Se gli utenti accedono all'app, è possibile ottenere un conteggio più preciso impostando l'ID dell'utente autenticato nel codice del browser:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

In un'applicazione MVC Web ASP.NET, ad esempio:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Non è necessario usare il nome di accesso effettivo dell'utente. È sufficiente usare un ID univoco per l'utente. Non deve includere spazi o i caratteri `,;=|`.

L'ID utente viene inoltre impostato in un cookie di sessione e inviato al server. Se l'SDK del server è installato, l'ID dell'utente autenticato viene inviato come parte delle proprietà contestuali della telemetria del client e del server. Quindi sarà possibile filtrarlo ed eseguire ricerche al suo interno.

Se l'app raggruppa gli utenti in account, è anche possibile passare un identificatore per l'account, con le stesse limitazioni di caratteri.

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

In [Esplora metriche](../../azure-monitor/app/metrics-explorer.md) è possibile creare un grafico che conta i valori **Utenti, Autenticati** e **Account utente**.

È anche possibile [cercare](../../azure-monitor/app/diagnostic-search.md) i punti dati del client con account e nomi utente specifici.

## <a name="properties"></a>Filtro, ricerca e segmentazione dei dati mediante le proprietà

È possibile associare proprietà e misure agli eventi e anche a metriche, visualizzazioni pagine, eccezioni e altri dati di telemetria.

*proprietà* sono valori di stringa che è possibile usare per filtrare i dati di telemetria nei report di utilizzo. Ad esempio, se l'app offre più giochi, è possibile associare il nome del gioco a ogni evento per vedere quali sono i giochi più diffusi.

Esiste un limite di 8192 per la lunghezza della stringa. Se si vogliono inviare grandi quantità di dati, usare il parametro del messaggio di TrackTrace.

*metriche* sono valori numerici che possono essere rappresentati graficamente. Ad esempio è possibile verificare se esiste un aumento graduale nei punteggi raggiunti dai giocatori. I grafici possono essere segmentati in base alle proprietà inviate con l'evento, in modo da ottenere grafici separati o in pila per giochi diversi.

Affinché i valori metrici siano visualizzati correttamente, devono essere maggiori o uguali a 0.

Esistono tuttavia alcuni [limiti sul numero di proprietà, di valori delle proprietà e di metriche](#limits) che è possibile usare.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Assicurarsi di non registrare informazioni personali nelle proprietà.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Metodo alternativo per impostare proprietà e metriche

Se si preferisce, è possibile raccogliere i parametri di un evento in un oggetto separato:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Non riusare la stessa istanza dell'elemento di telemetria, `event` in questo esempio, per chiamare Track*() più volte. Potrebbe causare l'invio della telemetria con una configurazione errata.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Misure e proprietà personalizzate in Analytics

In [Analytics](analytics.md) le metriche e le proprietà personalizzate vengono mostrate negli attributi `customMeasurements` e `customDimensions` di ogni record di dati di telemetria.

Ad esempio, se è stata aggiunta una proprietà denominata "game" ai dati di telemetria della richiesta, questa query conta le occorrenze di diversi valori di "game" e mostrerà la media del "punteggio" della metrica personalizzata:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Si noti che:

* Quando si estrae un valore dal JSON customDimensions o customMeasurements, questo è di tipo dinamico e pertanto è necessario eseguirne il cast in `tostring` o `todouble`.
* Per tener conto della possibilità di [campionamento](../../azure-monitor/app/sampling.md), si consiglia di usare `sum(itemCount)`, non `count()`.

## <a name="timed"></a> Temporizzazione degli eventi

A volte si vuole rappresentare in un grafico il tempo necessario per eseguire un'azione. Ad esempio si potrebbe voler sapere quanto tempo occorre agli utenti per scegliere tra le opzioni disponibili in un gioco. Per questo è possibile usare il parametro di misurazione.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Proprietà predefinite per i dati di telemetria personalizzati

Se si intende impostare solo i valori di proprietà predefiniti per alcuni degli eventi personalizzati scritti, è possibile impostarli in un'istanza di TelemetryClient. Vengono associati a ogni elemento di telemetria inviato da quel client.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Le singole chiamate di telemetria possono sostituire i valori predefiniti nei relativi dizionari delle proprietà.

*Per i client Web di JavaScript*, usare gli inizializzatori di telemetria JavaScript.

*Per aggiungere proprietà a tutti i dati di telemetria*, inclusi i dati dei moduli di raccolta standard, [implementare `ITelemetryInitializer`](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Campionamento, filtri ed elaborazione dei dati di telemetria

È possibile scrivere il codice per elaborare i dati di telemetria prima che vengano inviati dall'SDK. L'elaborazione include i dati inviati dai moduli di telemetria standard, come la raccolta delle richieste HTTP e la raccolta delle dipendenze.

[Aggiungere proprietà](../../azure-monitor/app/api-filtering-sampling.md#add-properties) ai dati di telemetria implementando `ITelemetryInitializer`. Ad esempio è possibile aggiungere numeri di versione o valori calcolati da altre proprietà.

L'[applicazione di filtri](../../azure-monitor/app/api-filtering-sampling.md#filtering) consente di modificare o rimuovere i dati di telemetria prima che vengano inviati dall'SDK implementando `ITelemetryProcessor`. È possibile controllare gli elementi inviati o eliminati, ma è necessario tenere conto dell'effetto sulle metriche. A seconda di come si eliminano gli elementi, si potrebbe perdere la possibilità di navigare tra elementi correlati.

Il [campionamento](../../azure-monitor/app/api-filtering-sampling.md) è una soluzione in pacchetto che consente di ridurre il volume dei dati inviati dall'app al portale. Lo fa senza influenzare le metriche visualizzate e senza influire sulla possibilità di diagnosticare i problemi navigando tra elementi correlati, come eccezioni, richieste e visualizzazioni di pagina.

[Altre informazioni](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Disabilitazione della telemetria

Per *avviare e arrestare in modo dinamico* la raccolta e la trasmissione di dati di telemetria:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Per disabilitare gli agenti di *raccolta standard selezionati*, ad esempio i contatori delle prestazioni, le richieste HTTP o le dipendenze, eliminare o impostare come commento le righe pertinenti in [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Questa operazione può essere eseguita, ad esempio, se si desidera inviare i propri dati TrackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Per *disabilitare gli agenti di raccolta standard selezionati* (ad esempio, i contatori delle prestazioni, le richieste HTTP o le dipendenze, in fase di inizializzazione), concatenare i metodi di configurazione con il codice di inizializzazione SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Per disabilitare questi agenti di raccolta dopo l'inizializzazione, usare l'oggetto Configuration:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Modalità di sviluppo

Durante il debug, è utile accelerare i dati di telemetria venga nella pipeline in modo da visualizzare immediatamente i risultati. È possibile che vengano visualizzati anche altri messaggi che consentono di tracciare eventuali problemi con i dati di telemetria. Disattivare questa modalità in fase di produzione poiché potrebbe rallentare l'app.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Per node. js, è possibile abilitare la modalità sviluppatore abilitando la registrazione interna tramite `setInternalLogging` e impostando `maxBatchSize` su 0, che causa l'invio dei dati di telemetria non appena vengono raccolti.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="ikey"></a> Impostazione della chiave di strumentazione per la telemetria personalizzata selezionata

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> Chiave di strumentazione dinamica

Per evitare di combinare i dati di telemetria di ambienti di sviluppo, test e produzione, è possibile [creare risorse distinte di Application Insights](../../azure-monitor/app/create-new-resource.md ) e modificare le relative chiavi a seconda dell'ambiente.

Invece di ottenere la chiave di strumentazione dal file di configurazione, è possibile impostarla nel codice. Impostare la chiave in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

Nelle pagine Web è possibile impostarla dallo stato del server Web anziché codificarla nello script. Ad esempio, in una pagina Web generata in un'app ASP.NET:

*JavaScript in Razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient dispone di una proprietà Context contenente valori che vengono inviati insieme a tutti i dati di telemetria. Sono in genere impostati dai moduli di telemetria standard, ma è possibile anche impostarli manualmente. ad esempio:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Se si imposta uno di questi valori manualmente, provare a rimuovere la riga pertinente da [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), in modo che i valori personali e quelli standard non si confondano.

* **Componente**: l'app e la relativa versione.
* **Dispositivo**: dati relativi al dispositivo in cui l'applicazione è in esecuzione. Nelle App Web questo è il server o dispositivo client da cui sono inviati i dati di telemetria.
* **InstrumentationKey**: la risorsa Application Insights in Azure in cui vengono visualizzati i dati di telemetria. Viene in genere presa dal file ApplicationInsights.config.
* **Posizione**: la posizione geografica del dispositivo.
* **Operazione**: nelle App Web la richiesta HTTP corrente. In altri tipi di app è possibile impostarla in modo da raggruppare gli eventi tra loro.
  * **ID**: un valore generato che mette in correlazione eventi diversi, in modo che quando si ispeziona un evento nella ricerca diagnostica, è possibile trovare elementi correlati.
  * **Nome**: un identificatore, in genere l'URL della richiesta HTTP.
  * **SyntheticSource**: se non è null o vuota, una stringa indicante che l'origine della richiesta è stata identificata come un test Web o un robot. Per impostazione predefinita viene esclusa dai calcoli in Esplora metriche.
* **Proprietà**: proprietà che vengono inviate con tutti i dati di telemetria. È possibile eseguire l'override di questo valore in singole chiamate di Track*.
* **Sessione**: la sessione dell'utente. L'ID viene impostato su un valore generato, che viene modificato quando l'utente non è stato attivo per un periodo di tempo specifico.
* **Utente**: le informazioni dell'utente.

## <a name="limits"></a>limiti

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Per evitare di raggiungere il limite di velocità dei dati usare il [campionamento](../../azure-monitor/app/sampling.md).

Per determinare quanto tempo vengono conservati i dati, vedere [Raccolta, conservazione e archiviazione di dati in Application Insights](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Documenti di riferimento

* [Riferimento ASP.NET](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Riferimento Java](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [Informazioni di riferimento su JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>Codice SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pacchetti per Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Domande?

* *Quali eccezioni potrebbero essere generate dalle chiamate Track_()?*

    None. Non è necessario eseguirne il wrapping in clausole try-catch. Se l'SDK rileva un problema, registrerà messaggi nell'output della console di debug e quindi in Ricerca diagnostica per approfondirne i dettagli.
* *Esiste un'API REST per ottenere dati dal portale?*

    Sì, l'[API di accesso ai dati](https://dev.applicationinsights.io/). Altri modi per estrarre i dati sono l'[esportazione da Analytics a Power BI](../../azure-monitor/app/export-power-bi.md ) e l'[esportazione continua](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Passaggi successivi

* [Ricerca di eventi e log](../../azure-monitor/app/diagnostic-search.md)
* [risoluzione dei problemi](../../azure-monitor/app/troubleshoot-faq.md)
