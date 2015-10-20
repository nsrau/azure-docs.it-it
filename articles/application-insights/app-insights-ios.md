<properties
    pageTitle="Application Insights per app per iOS | Microsoft Azure"
    description="Analizzare l'utilizzo e le prestazioni dell'app per iOS con Application Insights."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="ronmart"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/27/2015"
    ms.author="awills"/>

# Application Insights per app per iOS

Visual Studio Application Insights consente di monitorare un'applicazione per dispositivi mobili per verificarne l'utilizzo, gli eventi e gli arresti anomali.

## Requisiti

Sono necessari gli elementi seguenti:

* Una sottoscrizione con [Microsoft Azure](http://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft.
* Xcode 6 o versione successiva.
* L'SDK viene eseguito nei dispositivi con iOS 6.0 o versione successiva.

## Creare una risorsa di Application Insights

Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Scegliere l'opzione per iOS.

![Fare clic su Nuovo, Servizi per gli sviluppatori, Application Insights](./media/app-insights-ios/11-new.png)

Viene visualizzato un pannello che mostra le prestazioni e i dati di utilizzo relativi all'app. Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, nella schermata Start dovrebbe venire visualizzato un riquadro per l'app. In alternativa, fare clic su Sfoglia per cercarla.

## Scaricare Application Insights per Mac

(se non lo si è già fatto).

1. Scaricare [Application Insights per Mac](http://go.microsoft.com/fwlink/?LinkID=533209).

2. Estrarre il file con estensione zip.

3. Fare clic sull'icona dell'app per avviare Application Insights per Mac.

## <a name="signin"></a>Accedere ad Azure

1. Fare clic su **Sign In**.

2. Accedere con l'account Azure.

## Installare l'SDK nell'applicazione

1. Fare clic su **Integrate** per avviare l'integrazione dell'SDK.

2. Selezionare il progetto Xcode dall'elenco o fare clic su **Open Other** per trovare il progetto, quindi fare clic su **Integrate**.

3. Scegliere la cartella per Application Insights SDK, quindi fare clic su **Install**.

4. Aggiungere questa esecuzione dello script alle fasi di compilazione: [Aggiungere fase di esecuzione dello script](http://hockeyapp.net/help/runscriptbuildphase/).

5. Aggiungere i framework mancanti al progetto Xcode.

6. Trascinare il framework di Application Insights sul progetto Xcode, quindi fare clic su **Next**.

7. Selezionare **Integrate SDK into Target** per la destinazione usata.

8. Fare clic su **Create New Component** per creare l'app nel portale di Application Insights.

9. Selezionare la sottoscrizione, il gruppo di risorse e immettere un nome per il componente. Nella maggior parte dei casi, questo nome deve corrispondere al nome dell'app. Confermare con il pulsante **Create Resource**.

10. Assicurarsi che sia selezionato il componente corretto, quindi fare clic su **Next**.

11. Modificare il codice sorgente come illustrato nella procedura guidata, quindi fare clic su **Finish**.

12. Avviare l'app nel simulatore iOS con **Build & Run**.

## Inserire le chiamate di telemetria

Dopo avere chiamato `[MSAIApplicationInsights start]`, l'SDK inizia a tenere traccia di sessioni, visualizzazioni pagina ed eccezioni non gestite o arresti anomali.

È possibile aggiungere altri eventi, come indicato di seguito:

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric"
                                        value:42.2];

## Visualizzare i dati in Application Insights

Tornare a http://portal.azure.com e passare alla risorsa di Application Insights.

Fare clic su **Cerca** per aprire [Ricerca diagnostica][diagnostic], ovvero la finestra in cui vengono visualizzati i primi eventi. Se non viene visualizzato alcun contenuto, attendere uno o due minuti e fare clic su **Aggiorna**.

![Fare clic su Ricerca diagnostica](./media/app-insights-ios/21-search.png)

I dati verranno visualizzati nel pannello Panoramica non appena l'app viene usata.

![Pannello Panoramica](./media/app-insights-ios/22-oview.png)

Per ottenere altri dettagli, fare clic su qualsiasi grafico. Selezionare ad esempio gli arresti anomali:

![Fare clic sul grafico per gli arresti anomali](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>Passaggi successivi

[Tenere traccia dell'utilizzo dell'app][track]

[Ricerca diagnostica][diagnostic]

[Esplora metriche][metrics]

[Risoluzione dei problemi][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Oct15_HO3-->