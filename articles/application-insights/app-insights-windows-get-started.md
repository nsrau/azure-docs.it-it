<properties
	pageTitle="Application Insights per app di Windows Phone e Windows Store | Microsoft Azure"
	description="Analizzare l'utilizzo e le prestazioni dell'app per dispositivi Windows."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/21/2015"
	ms.author="awills"/>

# Analisi per app di Windows Phone e Windows Store

Microsoft offre due soluzioni di sviluppo per i dispositivi: [HockeyApp](http://hockeyapp.net/), per l'analisi di arresti anomali e flussi di lavoro DevOps, e [Application Insights](app-insights-overview.md), per l'analisi di arresti anomali e utilizzo.

[HockeyApp](http://hockeyapp.net/) è la soluzione DevOps mobile per la compilazione di app per dispositivi iOS, OS X, Android e Windows, nonché di app multipiattaforma basate su Xamarin, Cordova e Unity. Permette di distribuire le compilazioni ai beta tester, raccogliere dati sugli arresti anomali del sistema e ottenere commenti e suggerimenti degli utenti. L'integrazione con Visual Studio Team Services semplifica la distribuzione delle compilazioni e l'integrazione degli elementi di lavoro. Per altre informazioni, vedere la [Knowledge Base di HockeyApp](http://support.hockeyapp.net/kb). Per aggiornamenti, vedere il [blog di HockeyApp](http://hockeyapp.net/blog/).

Se l'app ha un lato server, usare [Application Insights](app-insights-overview.md) per monitorare il lato server Web dell'app in [ASP.NET](app-insights-asp-net.md) o [J2EE](app-insights-java-get-started.md). Inviare i dati di telemetria alla stessa risorsa di Application Insights per poter correlare gli eventi sui due lati.

È anche disponibile un [Application Insights SDK per app C++ universali](https://github.com/Microsoft/ApplicationInsights-CPP) che invia dati di telemetria al portale di Application Insights.

Visual Studio Application Insights consente di monitorare un'applicazione monitorata per le operazioni seguenti:

* [**Uso**][windowsUsage]\: acquisire informazioni sul numero di utenti a disposizione e su quali operazioni eseguono con l'app.
* [**Arresti anomali**][windowsCrash]\: ottenere report di diagnostica degli arresti anomali e comprendere l'impatto sugli utenti.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)

Per molti tipi di applicazioni [Visual Studio può aggiungere Application Insights all'app](#ide) quasi automaticamente. Tuttavia, poiché si sta leggendo questo articolo per una migliore comprensione, verranno illustrati i passaggi manuali.

Sono necessari gli elementi seguenti:

* Una sottoscrizione a [Microsoft Azure][azure].
* Visual Studio 2013 o versione successiva.

## 1\. Creare una risorsa di Application Insights 

Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights.

![Scegliere Nuovo, quindi Servizi per gli sviluppatori, Application Insights](./media/app-insights-windows-get-started/01-new.png)

Una [risorsa][roles] in Azure è un'istanza di un servizio. In questa risorsa la telemetria dell'app verrà analizzata e visualizzata.

#### Eseguire una copia della chiave di strumentazione

La chiave identifica la risorsa. È necessaria subito per configurare SDK per l'invio di dati alla risorsa.

![Aprire il cassetto a discesa Informazioni di base e selezionare la chiave di strumentazione](./media/app-insights-windows-get-started/02-props.png)


## 2\. Aggiungere Application Insights SDK alle app

In Visual Studio aggiungere l'SDK appropriato al progetto.

Se si tratta di un'app di Windows universale, ripetere i passaggi per il progetto Windows Phone e quello Windows.

1. Fare clic con il pulsante destro del mouse in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.

    ![](./media/app-insights-windows-get-started/03-nuget.png)

2. Cercare "Application Insights".

    ![](./media/app-insights-windows-get-started/04-ai-nuget.png)

3. Selezionare **Application Insights per applicazioni Windows**.

4. Aggiungere un file ApplicationInsights.config alla radice del progetto e inserire la chiave di strumentazione copiata in precedenza. Di seguito è riportato un file XML di esempio per questo file di configurazione.

	```xml
		<?xml version="1.0" encoding="utf-8" ?>
		<ApplicationInsights>
			<InstrumentationKey>YOUR COPIED INSTRUMENTATION KEY</InstrumentationKey>
		</ApplicationInsights>
	```

    Impostare le proprietà del file ApplicationInsights.config: **Azione di compilazione** == **Contenuto** e **Copia nella directory di output** == **Copia sempre**.
	
	![](./media/app-insights-windows-get-started/AIConfigFileSettings.png)

5. Aggiungere il codice di inizializzazione seguente. È preferibile aggiungere questo codice al costruttore `App()`. Se si esegue in un'altra operazione, si potrebbe perdere la raccolta automatica delle prime PageViews.

```C#
	public App()
	{
	   // Add this initilization line. 
	   WindowsAppInitializer.InitializeAsync();
	
	   this.InitializeComponent();
	   this.Suspending += OnSuspending;
	}  
```

**App universali di Windows**: ripetere i passaggi per i progetti Phone e Store. [Esempio di app universale di Windows 8.1](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal).

## <a name="network"></a>3. Abilitare l'accesso alla rete per l'app

Se l'app non ha già [richiesto l'accesso alla rete in uscita](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx), sarà necessario aggiungere il manifesto come una [funzionalità necessaria](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

## <a name="run"></a>4. Eseguire il progetto

[Eseguire l'applicazione con F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) e usarla in modo da generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati ricevuti.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

In modalità di debug la telemetria viene inviata non appena viene generata. In modalità di rilascio la telemetria è archiviata nel dispositivo e inviata solo quando l'app viene ripresa.


## <a name="monitor"></a>5. Visualizzare i dati monitorati

Aprire Application Insights dal progetto.

![Fare clic con il pulsante destro del mouse sul progetto e aprire il portale di Azure](./media/app-insights-windows-get-started/appinsights-04-openPortal.png)


All'inizio si vedranno solo uno o due punti. ad esempio:

![Fare clic per visualizzare altri dati](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

Se si prevedono più dati, fare clic su Aggiorna dopo pochi secondi.

Per visualizzare altri dettagli, fare clic su qualsiasi grafico.


## <a name="deploy"></a>5. Pubblicare l'applicazione in Store

[Pubblicare l'applicazione](http://dev.windows.com/publish) e controllare i dati accumulati quando gli utenti li scaricano e li usano.

## Personalizzare i dati di telemetria

#### Scelta degli agenti di raccolta

Application Insights SDK include alcuni agenti di raccolta , che raccolgono automaticamente diversi tipi di dati dall'app. Per impostazione predefinita, sono tutti attivi. È però possibile scegliere gli agenti di raccolta da inizializzare nel costruttore dell'app:

    WindowsAppInitializer.InitializeAsync( "00000000-0000-0000-0000-000000000000",
       WindowsCollectors.Metadata
       | WindowsCollectors.PageView
       | WindowsCollectors.Session 
       | WindowsCollectors.UnhandledException);

#### Inviare dati di telemetria personalizzati

Usare l'[API][api] per inviare eventi, metriche e dati di diagnostica ad Application Insights. Riepilogo:

```C#

 var tc = new TelemetryClient(); // Call once per thread

 // Send a user action or goal:
 tc.TrackEvent("Win Game");

 // Send a metric:
 tc.TrackMetric("Queue Length", q.Length);

 // Provide properties by which you can filter events:
 var properties = new Dictionary{"game", game.Name};

 // Provide metrics associated with an event:
 var measurements = new Dictionary{"score", game.score};

 tc.TrackEvent("Win Game", properties, measurements);

```

Per informazioni dettagliate, vedere [Metriche ed eventi personalizzati][api].

## Passaggi successivi

* [Rilevare e diagnosticare gli arresti anomali nell'app][windowsCrash]
* [Informazioni sulle metriche][metrics]
* [Informazioni su Ricerca diagnostica][diagnostic]


## <a name="ide"></a>Configurazione automatizzata

Se si preferisce, è possibile consentire a Visual Studio di eseguire automaticamente la procedura di configurazione per Windows Phone, Windows Store e molti altri tipi di app.

###<a name="new"></a> Se si crea un nuovo progetto di app Windows...

Nella finestra di dialogo Nuovo progetto selezionare Application Insights.

Se viene chiesto di accedere, usare le credenziali dell'account Azure, che è diverso dall'account Visual Studio Team Services.

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


###<a name="existing"></a> In alternativa, se è un progetto esistente ...

Aggiungere Application Insights da Esplora soluzioni.


![](./media/app-insights-windows-get-started/appinsights-d22-add.png)

## Per eseguire l'aggiornamento a una nuova versione dell’SDK

Quando [viene rilasciata una nuova versione di SDK](app-insights-release-notes-windows.md): * Fare clic con il pulsante destro del mouse sul progetto e scegliere Gestisci pacchetti NuGet. * Selezionare i pacchetti di Application Insights installati e scegliere Azione: aggiornamento.


## <a name="usage"></a>Passaggi successivi


[Rilevare e diagnosticare gli arresti anomali nell'app][windowsCrash]

[Acquisire ed eseguire ricerche nei log di diagnostica][diagnostic]


[Tenere traccia dell'utilizzo dell'app][windowsUsage]

[Usare l'API per inviare dati di telemetria personalizzati][api]

[Risoluzione dei problemi][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

<!---HONumber=AcomDC_1217_2015-->