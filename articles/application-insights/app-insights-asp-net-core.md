---
title: Azure Application Insights per ASP.NET Core | Documentazione Microsoft
description: "Monitorare la disponibilità, le prestazioni e l&quot;utilizzo delle applicazioni Web."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: 4c97ec97d9d12d86796e995c71d8c9bd6c6a3df2


---
# <a name="application-insights-for-aspnet-core"></a>Application Insights per ASP.NET Core
[Application Insights](app-insights-overview.md) consente di monitorare la disponibilità, le prestazioni e l'uso dell'applicazione Web. Con il feedback ottenuto sulle prestazioni e sull'efficacia dell'app in circostanze normali, è possibile prendere decisioni informate sulla direzione della progettazione in ogni ciclo di vita di sviluppo.

![Esempio](./media/app-insights-asp-net-core/sample.png)

È necessaria una sottoscrizione con [Microsoft Azure](http://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft. Se il team ha una sottoscrizione di Azure per l'organizzazione, chiedere al proprietario di aggiungere l'utente alla sottoscrizione usando il rispettivo account Microsoft.

## <a name="getting-started"></a>Introduzione
Seguire la [Guida introduttiva](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

## <a name="using-application-insights"></a>Utilizzo di Application Insights
Accedere al [Portale di Microsoft Azure](https://portal.azure.com) e accedere alla risorsa creata per monitorare l'app.

In una finestra separata dal browser, usare l'app per un periodo di tempo. Verranno visualizzati dati nei grafici di Application Insights. (Potrebbe essere necessario fare clic su Aggiorna.) Ci sarà solo una piccola quantità di dati al momento dello sviluppo, ma questi grafici si attiveranno davvero quando si pubblicherà l'app e si avranno numerosi utenti. 

La pagina di panoramica mostra i grafici delle prestazioni alle quali si potrebbe essere interessati: tempo di risposta del server, tempo di caricamento della pagina e conteggi delle richieste non riuscite. Fare clic su qualsiasi grafico per visualizzare altri grafici e dati.

Le visualizzazioni nel portale rientrano in due categorie principali:

* [Esplora metriche](app-insights-metrics-explorer.md) mostra grafici e tabelle di metriche e conteggi, come tempi di risposta, frequenze di errori o metriche create dall'utente con l'[API](app-insights-api-custom-events-metrics.md). Filtrare e segmentare i dati dai valori della proprietà per ottenere una migliore comprensione dell'app e dei relativi utenti.
* [Esplora ricerche](app-insights-diagnostic-search.md) elenca i singoli eventi, come richieste specifiche, eccezioni, tracce di log o eventi creati dall'utente con l'[API](app-insights-api-custom-events-metrics.md). Filtrare e cercare negli eventi, e spostarsi tra gli eventi correlati per analizzare i problemi.
* [Analisi](app-insights-analytics.md) consente di eseguire query simili a SQL sui dati di telemetria ed è un potente strumento di analisi e diagnostica.

## <a name="alerts"></a>Avvisi
* Si ottengono automaticamente [avvisi di diagnostica proattivi](app-insights-proactive-diagnostics.md) che informano su modifiche anomale alle frequenze di errori o ad altre metriche.
* Impostare i [test di disponibilità](app-insights-monitor-web-app-availability.md) per testare il sito Web continuamente da varie parti del mondo e ottenere messaggi di posta elettronica non appena un test ha esito negativo.
* Impostare [avvisi di metrica](app-insights-monitor-web-app-availability.md) per sapere se delle metriche quali tempi di risposta o frequenza di eccezioni superano i limiti accettabili.

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere i dati di telemetria alle pagine Web](app-insights-javascript.md) per monitorare l'uso e le prestazioni delle pagine.
* [Monitoraggio delle dipendenze](app-insights-asp-net-dependencies.md) per vedere se REST, SQL o altre risorse esterne rallentano l’utente.
* [Utilizzare l'API](app-insights-api-custom-events-metrics.md) per inviare gli eventi e le metriche per una visualizzazione più dettagliata dell'utilizzo e delle prestazioni dell'app.
* [Test di disponibilità](app-insights-monitor-web-app-availability.md) controlla costantemente l’app da tutto il mondo. 

## <a name="open-source"></a>Aprire origine
[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)




<!--HONumber=Jan17_HO4-->


