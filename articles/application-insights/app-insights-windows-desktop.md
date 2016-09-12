<properties 
	pageTitle="Monitoraggio dell'utilizzo e delle prestazioni per le applicazioni desktop di Windows" 
	description="Analizzare l'utilizzo e le prestazioni dell'applicazione desktop di Windows con HockeyApp e Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/26/2016" 
	ms.author="awills"/>  

# Monitoraggio dell'utilizzo e delle prestazioni nelle applicazioni desktop di Windows

*Application Insights è disponibile in anteprima.*

[Visual Studio Application Insights](app-insights-get-started.md) e [HockeyApp](https://hockeyapp.net) consentono di monitorare l'utilizzo e le prestazioni dell'applicazione distribuita.

> [AZURE.IMPORTANT] È consigliabile usare [HockeyApp](https://hockeyapp.net) per distribuire e monitorare applicazioni desktop e app per dispositivi. Con HockeyApp è possibile gestire la distribuzione, i test live e i commenti degli utenti, oltre a monitorare i report di utilizzo e di arresto anomalo. È anche possibile [esportare ed eseguire una query sui dati di telemetria con Analisi](app-insights-hockeyapp-bridge-app.md).

> Sebbene i dati di telemetria possano essere inviati ad Application Insights da un'applicazione desktop, sono utili principalmente a scopo di debug e sperimentazione.


## Per inviare dati di telemetria ad Application Insights da un'applicazione Windows

1. Nel [portale di Azure](https://portal.azure.com) [creare una risorsa di Application Insights](app-insights-create-new-resource.md). Scegliere l'app ASP.NET per il tipo di applicazione.
2. Eseguire una copia della chiave di strumentazione. Trovare la chiave nell'elenco a discesa Informazioni di base della nuova risorsa appena creata.
3. In Visual Studio, modificare i pacchetti NuGet del progetto dell'app e aggiungere Microsoft.ApplicationInsights.WindowsServer. In alternativa, se si vuole ottenere solo l'API, senza i moduli di raccolta dei dati di telemetria standard, scegliere Microsoft.ApplicationInsights.
4. Impostare la chiave di strumentazione nel codice:

    `TelemetryConfiguration.Active.InstrumentationKey = "` *nome della chiave* `";`

    o in ApplicationInsights.config, se è installato uno dei pacchetti di telemetria standard:
 
    `<InstrumentationKey>`*nome della chiave*`</InstrumentationKey>`

    Se si utilizza Applicationinsights.config, assicurarsi che le proprietà in Esplora soluzione siano impostate su **Build Action = Content, Copy to Output Directory = Copy**.
5. [Usare l'API](app-insights-api-custom-events-metrics.md) per inviare dati di telemetria.
6. Eseguire l'app e visualizzare i dati di telemetria nella risorsa creata nel portale di Azure.

## <a name="telemetry"></a>Codice di esempio

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## Passaggi successivi

* [Creare un dashboard](app-insights-dashboards.md)
* [Ricerca diagnostica](app-insights-diagnostic-search.md)
* [Esplorare le metriche](app-insights-metrics-explorer.md)
* [Scrivere query di Analisi](app-insights-analytics.md)
 

<!---HONumber=AcomDC_0831_2016-->