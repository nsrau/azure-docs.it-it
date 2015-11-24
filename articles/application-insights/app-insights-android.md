<properties
    pageTitle="Analisi per app Android | Microsoft Azure"
    description="Analizzare l'utilizzo e le prestazioni dell'app Android."
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/14/2015"
    ms.author="awills"/>

# Analisi per app le Android

Visual Studio Application Insights consente di monitorare un'applicazione per dispositivi mobili per verificarne l'utilizzo, gli eventi e gli arresti anomali.

> [AZURE.NOTE]È consigliabile [HockeyApp](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk) per ottenere segnalazioni di arresto anomalo, analisi, distribuzione e gestione di commenti e suggerimenti.

## Requisiti

Sono necessari gli elementi seguenti:

* Una sottoscrizione con [Microsoft Azure](http://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft.
* Android Studio
* Android SDK Versione 9 o successive.

## Creare una risorsa di Application Insights

Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Scegliere l'opzione Android.

![Fare clic su Nuovo, Servizi per gli sviluppatori, Application Insights](./media/app-insights-android/11-new.png)

Viene visualizzato un pannello che mostra le prestazioni e i dati di utilizzo relativi all'app. Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, nella schermata Start dovrebbe venire visualizzato un riquadro per l'app. In alternativa, fare clic su Sfoglia per cercarla.

## Impostazione

Attenersi alla [guida all'installazione](https://github.com/Microsoft/ApplicationInsights-Android#-3-setup).


## Usare l'SDK

Inizializzare l'SDK e iniziare a tenere traccia dei dati di telemetria.

Aggiungere l'importazione seguente all'attività radice dell'app:

```java

     import com.microsoft.applicationinsights.library.ApplicationInsights;
```

Aggiungere quindi quanto segue al callback `onCreate` dell'attività.

```java

    ApplicationInsights.setup(this.getApplicationContext(), this.getApplication());
    ApplicationInsights.start();
```

Dopo avere chiamato `ApplicationInsights.start()`, l'SDK inizierà a tenere traccia dell'attività del ciclo di vita Android e di qualsiasi eccezione non gestita.

> [AZURE.NOTE]Gli eventi del ciclo di vita dell'applicazione vengono raccolti solo in Android SDK versione 15 e successive (Ice Cream Sandwich+).

Oltre a questi possono essere raccolti eventi personalizzati, tracce, metriche ed eccezioni gestite. Usare l'[API di Application Insights][api] per inviare dati di telemetria.

* TrackEvent(eventName) per altre azioni utente
* TrackTrace(logEvent) per la [registrazione diagnostica][diagnostic]
* TrackHandledException(exception) in clausole catch
* TrackMetric(name, value) in un'attività in background per inviare report periodici delle metriche non associate a eventi specifici.

Il codice seguente è un esempio di inizializzazione e raccolta di dati di telemetria manuale.

```java

    public class MyActivity extends Activity {

      @Override
      protected void onCreate(Bundle savedInstanceState) {

        ApplicationInsights.setup(this);
        //... other initialization code ...//
        ApplicationInsights.start();

        // track telemetry data
        TelemetryClient client = TelemetryClient.getInstance();
        HashMap<String, String> properties = new HashMap<String, String>();
        properties.put("property1", "my custom property");
        client.trackEvent("sample event", properties);
        client.trackTrace("sample trace");
        client.trackMetric("sample metric", 3);
        client.trackHandledException(new Exception("sample exception"));
      }
    }
```

## <a name="run"></a> Eseguire il progetto

Eseguire l'applicazione (MAIUSC+F10 in Windows, CTRL+R in OS X) per generare dati di telemetria.

## Visualizzare i dati in Application Insights

Tornare a http://portal.azure.com e passare alla risorsa di Application Insights.

Fare clic su **Cerca** per aprire [Ricerca diagnostica][diagnostic], ovvero la finestra in cui vengono visualizzati i primi eventi. Se non viene visualizzato alcun contenuto, attendere uno o due minuti e fare clic su **Aggiorna**.

![Fare clic su Ricerca diagnostica](./media/app-insights-android/21-search.png)

I dati vengono visualizzati nel pannello Panoramica non appena l'app viene usata.

![Pannello Panoramica](./media/app-insights-android/22-oview.png)

Fare clic su qualsiasi grafico per ottenere altri dettagli. Selezionare ad esempio gli arresti anomali:

![Fare clic sul grafico per gli arresti anomali](./media/app-insights-android/23-crashes.png)


## <a name="usage"></a>Passaggi successivi

[Tenere traccia dell'utilizzo dell'app][track]

[Ricerca diagnostica][diagnostic]

[Esplora metriche][metrics]

[Risoluzione dei problemi][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->