<properties 
	pageTitle="Cosa fare in Application Insights" 
	description="Domande frequenti in Application Insights" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Cosa fare in Application Insights?

## Per ricevere un messaggio di posta elettronica quando...

### Inviare un messaggio di posta elettronica se il sito non è disponibile

Impostare un [test web di disponibilità](app-insights-monitor-web-app-availability.md).

### Inviare un messaggio di posta elettronica se il sito è sovraccarico

Impostare un [avviso](app-insights-alerts.md) per il **tempo di risposta del server**. Una soglia compresa tra 1 e 2 secondi dovrebbe risolvere il problema.

![](./media/app-insights-how-do-i/030-server.png)

L'applicazione potrebbe inoltre dare segni di difficoltà tramite la restituzione di codici di errore. Impostare un avviso per le **richieste non riuscite**.

Se si desidera impostare un avviso per le **eccezioni del server**, è necessario effettuare [alcune impostazioni aggiuntive](app-insights-asp-net-exceptions.md) per visualizzare i dati.


### Inviare un messaggio di posta elettronica per un evento generato dall'app

Si supponga che si desidera ricevere un messaggio di posta elettronica quando si verifica un evento specifico. Application Insights non fornisce direttamente questa funzionalità, ma è possibile [inviare un avviso quando una metrica supera una soglia](app-insights-alerts.md).

Gli avvisi possono essere impostati per [metriche personalizzate](app-insights-api-custom-events-metrics.md#track-metric), anche se non per gli eventi personalizzati. Scrivere codice per potenziare una metrica quando si verifica l'evento:

    telemetry.TrackMetric("Alarm", 10);

oppure:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Poiché gli avvisi possono avere due stati, è necessario inviare un valore basso quando si presume che l'avviso sia terminato:

    telemetry.TrackMetric("Alarm", 0.5);

Creare un grafico in [Esplora metriche](app-insights-metric-explorer.md) per visualizzare l'allarme:

![](./media/app-insights-how-do-i/010-alarm.png)

Impostare un avviso da attivare quando la metrica supera un valore medio per un breve periodo:


![](./media/app-insights-how-do-i/020-threshold.png)

Impostare il periodo medio sul valore minimo.

Verranno ricevuti messaggi di posta elettronica quando la metrica è maggiore o minore della soglia.

Alcune informazioni da considerare:

* Un avviso dispone di due stati: "Avviso" e "Integro". Lo stato viene valutato solo quando viene ricevuta una metrica.
* Un messaggio di posta elettronica viene inviato solo quando lo stato cambia. Questo è il motivo per cui è necessario inviare sia metriche con valori alti che metriche con valori bassi. 
* Per valutare l'avviso, viene eseguita la media dei valori ricevuti nel periodo precedente. Ciò si verifica ogni volta che viene ricevuta una metrica. Per questo motivo è possibile che i messaggi di posta elettronica vengano inviati più frequentemente rispetto al periodo impostato.
* Poiché vengono inviati messaggi di posta elettronica sia per avvisi con stato "Avviso" e che per avvisi con stato "Integro", è possibile riconsiderare l'evento unico come una condizione con due stati. Ad esempio, anziché un evento "processo completato", creare una condizione "processo in corso", dove è possibile ricevere messaggi di posta elettronica all'inizio e alla fine di un processo.
 
## Filtro della versione dell'applicazione

Quando si pubblica una nuova versione dell'app, potrebbe essere opportuno separare i dati telemetrici delle diverse versioni.

È possibile impostare la proprietà della versione dell'applicazione in modo che sia possibile filtrare i risultati della [ricerca](app-insights-diagnostic-search.md) e di [Esplora metriche](app-insights-metrics-explorer.md).


![](./media/app-insights-how-do-i/050-filter.png)

Esistono diversi metodi di impostazione della proprietà della versione dell'applicazione.

* Impostare direttamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* Eseguire il wrapping di tale riga in un [inizializzatore di telemetria](app-insights-api-custom-events-metrics.md#telemetry-initializers) per assicurarsi che tutte le istanze di TelemetryClient siano impostate in modo coerente.

* [ASP.NET] Impostare la versione `BuildInfo.config`. Il modulo Web selezionerà la versione dal nodo BuildLabel. Includere questo file nel progetto e ricordarsi di impostare la proprietà Copia sempre in Esplora soluzioni.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generare automaticamente BuildInfo.config in MSBuild. A tale scopo, aggiungere alcune righe al file con estensione CSPROJ:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
    ```

    Viene generato un file denominato *NomeProgetto*.BuildInfo.config. Il processo di pubblicazione rinomina il file in BuildInfo.config.

    L'etichetta di compilazione contiene un segnaposto (AutoGen\_) se la compilazione viene eseguita in Visual Studio. Se la compilazione viene eseguita con MSBuild, viene inserito il numero di versione corretto.

    Per consentire a MSBuild di generare i numeri di versione, in AssemblyReference.cs impostare la versione come, ad esempio, `1.0.*`.

<!---HONumber=Oct15_HO1-->