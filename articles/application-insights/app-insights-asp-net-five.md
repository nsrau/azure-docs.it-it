<properties 
	pageTitle="Application Insights per ASP.NET 5" 
	description="Monitorare la disponibilità, le prestazioni e l'utilizzo delle applicazioni Web." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="awills"/>

# Application Insights per ASP.NET 5

Application Insights consente di monitorare la disponibilità, le prestazioni e l'utilizzo dell'applicazione Web. Con il feedback ottenuto sulle prestazioni e sull'efficacia dell'app in circostanze normali, è possibile prendere decisioni informate sulla direzione della progettazione in ogni ciclo di vita di sviluppo.

![Esempio](./media/app-insights-asp-net-five/sample.png)

È necessaria una sottoscrizione con [Microsoft Azure](http://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft.

## Creare un progetto ASP.NET 5

...se non lo si è fatto ancora.

Usare il modello di progetto ASP.NET 5 standard in Visual Studio 2015.


## Creare una risorsa Application Insights

Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Selezionare l'opzione ASP.NET.

![Fare clic su Nuovo, Servizi per gli sviluppatori, Application Insights](./media/app-insights-asp-net-five/01-new-asp.png)

Viene visualizzato un pannello delle [risorse][roles] che è dove vengono visualizzate le prestazioni e i dati di utilizzo relativi all'app. Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, nella schermata Start dovrebbe venire visualizzato un riquadro per l'app. In alternativa, fare clic su Sfoglia per cercarla.

La scelta del tipo di applicazione imposta il contenuto predefinito dei pannelli delle risorse e le proprietà visibili in [Esplora metriche][metrics].

##  Configurare il progetto con la chiave di strumentazione.

Copiare la chiave dalla risorsa di Application Insights:

![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/app-insights-asp-net-five/02-props-asp.png)

Nel progetto ASP.NET 5 incollarla in `config.json`:

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

In alternativa, se si preferisce la configurazione dinamica, è possibile aggiungere questo codice alla classe di avvio dell'applicazione:

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## Aggiunta di Application Insights al progetto


#### Installare il pacchetto NuGet

Trovare il [numero di versione più recente](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases) del pacchetto NuGet.

Aprire `project.json` e modificare la sezione `dependencies`:

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta4",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta4"
      }
    }

#### Analizzare il file di configurazione

In `startup.cs`:

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

Nel metodo `Startup`:

    // Setup configuration sources.
    var configuration = new Configuration()
       .AddJsonFile("config.json")
       .AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    configuration.AddEnvironmentVariables();
    Configuration = configuration;

    if (env.IsEnvironment("Development"))
    {
      configuration.AddApplicationInsightsSettings(developerMode: true);
    }

Nel metodo `ConfigurationServices`:

    services.AddApplicationInsightsTelemetry(Configuration);

Nel metodo `Configure`:

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## Aggiungere la strumentazione client JavaScript

Se si dispone di un file _Layout.cshtml, inserirvi il codice seguente. In caso contrario, inserire il codice in qualsiasi pagina di cui si vuole tenere traccia.

Definire l'inserimento nella parte superiore del file:

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

Inserire l'helper Html prima del tag `</head>` e prima di qualsiasi altro script. Qualsiasi telemetria JavaScript personalizzata che si vuole segnalare dalla pagina deve essere inserita dopo questo frammento di codice:

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## Eseguire l'app

Eseguire il debug dell'applicazione in Visual Studio o pubblicarlo sul server Web.

## Visualizza i dati sull'app

Tornare al [portale di Azure][portal] e passare alla risorsa Application Insights. Se non sono presenti dati nel pannello Panoramica, attendere uno o due minuti e fare clic su Aggiorna.

* [Tenere traccia dell'utilizzo dell'app][usage]
* [Diagnosticare problemi di prestazioni][detect]
* [Configurare i test Web per monitorare la disponibilità][availability]



## Aprire origine

[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=58--> 