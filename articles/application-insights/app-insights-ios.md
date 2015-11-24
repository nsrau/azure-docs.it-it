<properties
    pageTitle="Analisi per app iOS | Microsoft Azure"
    description="Analizzare l'utilizzo e le prestazioni dell'app iOS."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/15/2015"
    ms.author="awills"/>

# Analisi per le app iOS

Visual Studio Application Insights consente di monitorare un'applicazione per dispositivi mobili per verificarne l'utilizzo, gli eventi e gli arresti anomali.


> [AZURE.NOTE]È consigliabile [HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios) per ottenere segnalazioni di arresto anomalo, analisi, distribuzione e gestione di commenti e suggerimenti.

## Requisiti

Sono necessari gli elementi seguenti:

* Una sottoscrizione con [Microsoft Azure](http://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft.
* Xcode 6 o versione successiva.
* L'SDK viene eseguito nei dispositivi con iOS 6.0 o versione successiva.

## Creare una risorsa di Application Insights

Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Scegliere l'opzione per iOS.

![Fare clic su Nuovo, Servizi per gli sviluppatori, Application Insights](./media/app-insights-ios/11-new.png)

Viene visualizzato un pannello che mostra le prestazioni e i dati di utilizzo relativi all'app. Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, nella schermata Start dovrebbe venire visualizzato un riquadro per l'app. In alternativa, fare clic su Sfoglia per cercarla.

## Impostazione

Seguire il [manuale di installazione](https://github.com/Microsoft/ApplicationInsights-iOS#setup) per installare l'SDK nell'app.

## Visualizzare i dati in Application Insights

Eseguire l'app per generare alcuni dati di telemetria.

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

<!---HONumber=Nov15_HO4-->