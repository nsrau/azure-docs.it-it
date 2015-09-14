<properties 
	pageTitle="Separare le risorse di Application Insights per lo sviluppo, il test e la produzione"
	description="Monitorare le prestazioni e l'utilizzo dell'applicazione nelle diverse fasi di sviluppo"
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
	ms.date="09/02/2015"
	ms.author="awills"/>

# Separare le risorse di Application Insights per lo sviluppo, il test e la produzione


Per evitare la confusione tra i dati di telemetria provenienti dalle versioni di debug, test e produzione, è consigliabile creare risorse separate di [Application Insights][start] per la ricezione di dati da ogni versione.

I dati ricevuti dall'applicazione vengono archiviati ed elaborati da Application Insights in una *risorsa* di Microsoft Azure. Ogni risorsa viene identificata da una *chiave di strumentazione*. Nell'app la chiave viene fornita ad Application Insights SDK, in modo che possa inviare i dati raccolti alla risorsa corretta. La chiave può essere fornita nel codice o nel file ApplicationInsights.config. La modifica della chiave nell'SDK permette di indirizzare i dati a risorse diverse.


## Creare una risorsa di Application Insights
  

In [portal.azure.com](https://portal.azure.com) aggiungere una nuova risorsa di Application Insights:

![Fare clic su Nuovo, Application Insights](./media/app-insights-create-new-resource/01-new.png)


* Il **tipo di applicazione** influisce sul contenuto del pannello Panoramica e sulle proprietà disponibili in [Esplora metriche][metrics]. Se il proprio tipo di app non viene visualizzato, scegliere uno dei tipi Web per le pagine Web e uno dei tipi telefono per gli altri dispositivi.
* Un **gruppo di risorse** è utile per gestire le proprietà come il [controllo di accesso](app-insights-resources-roles-access-control.md). È possibile usare gruppi di risorse separati per lo sviluppo, i test e la produzione.
* La **sottoscrizione** è il proprio account di pagamento in Azure.
* Il **percorso** è la posizione in cui vengono mantenuti i propri dati e attualmente non è modificabile.
* L'opzione **Aggiungi a schermata iniziale** inserisce un riquadro di accesso rapido alla propria risorsa nella pagina iniziale di Azure. 

La creazione della risorsa richiede pochi secondi. Al termine della creazione verrà visualizzato un avviso.

È possibile scrivere uno [script di PowerShell](app-insights-powershell-script-create-resource.md) per creare automaticamente una risorsa.


## Eseguire una copia della chiave di strumentazione

La chiave di strumentazione identifica la risorsa creata.

![Fare clic su Informazioni di base, quindi sulla chiave di strumentazione e infine premere CTRL+C.](./media/app-insights-create-new-resource/02-props.png)

Saranno necessarie le chiavi di strumentazione di tutte le risorse a cui l'app invierà dati.


## <a name="dynamic-ikey"></a> Chiave di strumentazione dinamica

In genere l'SDK ottiene il valore iKey dal file ApplicationInsights.config. Per semplificare, è invece possibile configurarlo nel codice.

Impostare la chiave in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

In questo esempio i valori ikey per le diverse risorse vengono inseriti in versioni diverse del file di configurazione Web. Se si cambia il file di configurazione Web, verrà cambiata la risorsa di destinazione.

#### Pagina Web

Il valore iKey viene usato anche nelle pagine Web dell'app, nello [script ottenuto dal pannello Avvio rapido](app-insights-javascript.md). Invece di codificarlo letteralmente nello script, generarlo dallo stato del server. Ad esempio, in un'app ASP.NET:

*JavaScript in Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=September15_HO1-->