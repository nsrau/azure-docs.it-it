<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Monitoraggio dell'utilizzo

## <a name="webclient"></a>Configurazione dell'analisi di utilizzo Web

Se non lo si è ancora fatto, [aggiungere Application Insights al progetto Web][start].

## <a name="usage"></a>Analisi di utilizzo

Nella falda della panoramica dell'applicazione verranno visualizzati i seguenti riquadri di utilizzo:

![](./media/appinsights/appinsights-47usage.png)

### Sessioni per browser

Una *sessione* è un periodo che inizia quando un utente apre qualsiasi pagina del sito Web e termina quando l'utente non invia alcuna richiesta Web per un periodo di timeout di 30 minuti.

Fare clic per ingrandire il grafico.

### Visualizzazioni delle pagine più importanti

Mostra i conteggi totali nelle ultime 24 ore.

Fare clic sulle visualizzazioni di pagina per ottenere una cronologia più dettagliata.

![](./media/appinsights/appinsights-49usage.png)

Fare clic su Intervallo di tempo per visualizzare una cronologia più lunga che arriva fino a sette giorni.

Fare clic su un grafico per vedere le altre metriche che è possibile visualizzare.

![](./media/appinsights/appinsights-63usermetrics.png)

## Conteggi di pagina personalizzati

Per impostazione predefinita, viene conteggiata una pagina ogni volta che una nuova pagina viene caricata nel browser client. Potrebbero tuttavia essere utile conteggiare visualizzazioni di pagina aggiuntive. Ad esempio, il contenuto di una pagina potrebbe essere visualizzato in schede e si desidera conteggiare una pagina quando l'utente cambia scheda oppure un codice JavaScript nella pagina potrebbe caricare nuovi contenuti senza cambiare l'URL del browser.

Inserire una chiamata JavaScript simile a questa nel punto appropriato nel codice del client:

    appInsights.trackPageView(myPageName);

Il nome della pagina può contenere gli stessi caratteri di un URL, ma i caratteri successivi a "\#" o "?" verranno ignorati.

## Esame di singoli eventi di visualizzazione di pagina

In genere la telemetria delle visualizzazioni di pagina viene analizzata da Application Insights e vengono quindi visualizzati solo i report cumulativi, mediati su tutti gli utenti del sito. A scopo di debug, tuttavia, è possibile visualizzare anche singoli eventi di visualizzazione di pagina.

Nella falda di ricerca diagnostica, impostare Filtri su Visualizzazione pagina.

![](./media/appinsights/appinsights-51searchpageviews.png)

Selezionare qualsiasi evento per visualizzare altri dettagli.

> [WACOM.NOTE] Se si utilizza la [ricerca][diagnostic], tenere presente che è necessario cercare parole intere: "Circ" e "irca" non troveranno "Circa", ma "Circ\* " sì. Inoltre, non è possibile iniziare un termine di ricerca con un carattere jolly. Ad esempio, se si cerca "\*irc" non si troverà "Circa".

> [Ulteriori informazioni sulla ricerca diagnostica][diagnostic]

## Monitoraggio dell'utilizzo

> [AZURE.VIDEO tracking-usage-with-application-insights]

## Ulteriori informazioni

-   [Application Insights - Guida introduttiva][start]
-   [Monitoraggio di un server Web live][redfield]
-   [Monitoraggio delle prestazioni delle applicazioni Web][perf]
-   [Ricerca nei registri di diagnostica][diagnostic]
-   [Monitoraggio della disponibilità con test Web][availability]
-   [Monitoraggio dell'utilizzo][usage]
-   [Domande e risposte e risoluzione dei problemi][qna]

<!--Link references-->

  [start]: ../app-insights-start-monitoring-app-health-usage/
  []: ./media/appinsights/appinsights-47usage.png
  [diagnostic]: ../app-insights-search-diagnostic-logs/
  [redfield]: ../app-insights-monitor-performance-live-website-now/
  [perf]: ../app-insights-web-monitor-performance/
  [availability]: ../app-insights-monitor-web-app-availability/
  [usage]: ../app-insights-web-track-usage/
  [qna]: ../app-insights-troubleshoot-faq/
