<properties pageTitle="Application Insights for C++ apps" description="Analyze usage and performance of your C++ app with Application Insights." services="application-insights" documentationCenter="cpp" authors="crystk" manager="jakubo""/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# Application Insights per le app C++

Visual Studio Application Insights consente di monitorare un'applicazione per dispositivi mobili per verificarne l'utilizzo, gli eventi e gli arresti anomali.

## Requisiti

Sono necessari gli elementi seguenti:

* Una sottoscrizione con [Microsoft Azure](http://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft.
* Visual Studio 2015 o versione successiva.

## Creare una risorsa Application Insights

Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Scegliere l'opzione Windows Phone o Windows Store.

![Fare clic su Nuovo, Servizi per gli sviluppatori, Application Insights](./media/app-insights-windows-cpp/01-universal.png)

Viene visualizzato un pannello che mostra le prestazioni e i dati di utilizzo relativi all'app. Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, nella schermata Start dovrebbe venire visualizzato un riquadro per l'app. In alternativa, fare clic su Sfoglia per cercarla.

####  Eseguire una copia della chiave di strumentazione.

La chiave identifica la risorsa e verrà installata subito nell'SDK per indirizzare i dati alla risorsa.

![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a> Installare l’SDK nell'applicazione


1. In Visual Studio è possibile modificare i pacchetti NuGet del progetto di app desktop.

    ![Fare clic con il pulsante destro del mouse sul progetto e selezionare Gestisci pacchetti Nuget](./media/app-insights-windows-cpp/03-nuget.png)

2. Installare Application Insights SDK per app C++.

    ![Selezionare **Includi versione preliminare** e cercare "Application Insights"](./media/app-insights-windows-cpp/04-nuget.png)

3. Nelle impostazioni del progetto per release e debug:
  - Aggiungere $(SolutionDir)packages\\ApplicationInsights-CPP.1.0.0-Beta\\src\\inc alle proprietà del progetto -> VC++ Directories -> Include Directories
  - Aggiungere $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<PLATFORM TYPE>\\release\\AppInsights\_Win10-UAP alle proprietà del progetto -> VC++ Directories -> Library Directories

4. Aggiungere ApplicationInsights.winmd come riferimento al progetto da $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<TIPO DI PIATTAFORMA>\\release\\ApplicationInsights
5. Aggiungere il file AppInsights\_Win10-UAP.dll da $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<TIPO DI PIATTAFORMA>\\release\\AppInsights\_Win10-UAP. Passare alle proprietà e impostare il contenuto su YES per copiare la DLL nella directory di compilazione.


#### Per aggiornare l’SDK per le versioni future

Quando un nuovo [SDK viene rilasciato](app-insights-release-notes-windows-cpp.md):

* In Gestione pacchetti NuGet, selezionare l’SDK installato e scegliere Azione: aggiornamento.
* Ripetere i passaggi di installazione usando il nuovo numero di versione.

## Usare l'SDK

Inizializzare l'SDK e iniziare a tenere traccia dei dati di telemetria.

1. In App.xaml.h: 
  - Aggiungere: `ApplicationInsights::CX::SessionTracking^ m_session;`
2. In App.xaml.cpp:
  - Aggiungere: `using namespace ApplicationInsights::CX;`

  - In App:App()
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - Dopo aver creato il frame principale (in genere alla fine di App::OnLaunched), inizializzare m\_session:
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. Per usare la traccia altrove nell'applicazione, è possibile dichiarare un'istanza del client di telemetria.


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> Eseguire il progetto

Eseguire l'applicazione per generare dati di telemetria. È possibile eseguire l’applicazione in modalità di debug nel computer di sviluppo o pubblicarla e consentire agli utenti di eseguirla.

## Visualizzare i dati in Application Insights

Tornare a http://portal.azure.com e passare alla risorsa di Application Insights.

Fare clic su Cerca per aprire [Ricerca diagnostica][diagnostic], ovvero la finestra in cui vengono visualizzati i primi eventi. Se non viene visualizzato alcun contenuto, attendere uno o due minuti e fare clic su Aggiorna.

![Fare clic su Ricerca diagnostica](./media/app-insights-windows-cpp/21-search.png)

I dati verranno visualizzati nel pannello Panoramica non appena l'app viene usata.

![Pannello Panoramica](./media/app-insights-windows-cpp/22-oview.png)

Fare clic su qualsiasi grafico per ottenere altri dettagli. Selezionare ad esempio gli arresti anomali:

![Fare clic sul grafico per gli arresti anomali](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>Passaggi successivi

[Tenere traccia dell'utilizzo dell'app][track]

[Usare l'API per inviare metriche ed eventi personalizzati][api]

[Ricerca diagnostica][diagnostic]

[Esplora metriche][metrics]

[Risoluzione dei problemi][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=August15_HO6-->