<properties title="Application Insights" pageTitle="Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Introduzione ad Application Insights

*Application Insights è attualmente disponibile in anteprima.*

Application Insights consente di monitorare disponibilità, prestazioni e utilizzo dell'applicazione attiva (non necessariamente un'applicazione Microsoft Azure). La configurazione è semplicissima e i risultati sono visibili in pochi minuti.

-   **Disponibilità**: è possibile assicurarsi che l'app Web sia disponibile e reattiva. Gli URL verranno testati a intervalli di alcuni minuti in tutto il mondo e l'utente verrà informato di un eventuale problema.
-   **Prestazioni**: è possibile diagnosticare eventuali problemi di prestazioni o eccezioni nel servizio Web. L'utente potrà verificare che i tempi di risposta variano con il conteggio delle richieste, scoprire se la CPU o altre risorse vengono estese, ottenere le tracce dello stack dalle eccezioni ed eseguire facilmente la ricerca nelle tracce del registro.
-   **Utilizzo**: è possibile scoprire in che modo gli utenti usano l'app, in modo da pote concentrare l'attività di sviluppo laddove è più utile. Al momento, è possibile monitorare le app Web, per Windows Store e per Windows Phone.

## Introduzione

È possibile iniziare in due modi:

-   [Aggiungere Application Insights al progetto in Visual Studio][Aggiungere Application Insights al progetto in Visual Studio]

    Aggiungere Application Insights ai progetti per tenere traccia dell'utilizzo, delle prestazioni e della disponibilità, oltre che per analizzare i log di diagnostica. I dati saranno visibili entro pochi minuti in modalità di debug e sarà possibile distribuire il progetto per ottenere dati in diretta.

    Usare questa opzione se si sta aggiornando o creando un progetto.

    [Per iniziare, aggiungere Application Insights al progetto.][Aggiungere Application Insights al progetto in Visual Studio]

-   [Diagnosticare i problemi in un servizio Web attivo ora][Diagnosticare i problemi in un servizio Web attivo ora]

    Installare l'agente Application Insights sul server IIS e visualizzare i dati relativi alle prestazioni in pochi minuti. Controllare il conteggio delle richieste, i tempi di risposta, il carico delle risorse e ottenere le tracce delle eccezioni.

    Usare questa opzione se è necessario conoscere le attività in corso sul server Web. La scelta di questa opzione non implica la ridistribuzione del codice, ma sono comunque necessari l'accesso amministrativo al server e un account Microsoft Azure.

    È possibile aggiungere il monitoraggio della disponibilità in qualsiasi momento.

    In seguito, sarà possibile usare l'altra opzione per aggiungere Application Insights al progetto in modo da analizzare i log di diagnostica e tenere traccia dell'utilizzo.

    [Per iniziare, installare Application Insights sul server Web.][Diagnosticare i problemi in un servizio Web attivo ora]

> [WACOM.NOTE] Esiste una [versione precedente di Application Insights][versione precedente di Application Insights] in Visual Studio Online. La nuova versione descritta in questo articolo viene creata da zero come parte di Microsoft Azure.

![Esempio di monitoraggio delle applicazioni in Application Insights][Esempio di monitoraggio delle applicazioni in Application Insights]

## Altre informazioni

-   [Application Insights][Application Insights]
-   [Aggiungere Application Insights al progetto][Aggiungere Application Insights al progetto in Visual Studio]
-   [Monitorare un server Web attivo][Diagnosticare i problemi in un servizio Web attivo ora]
-   [Esaminare le metriche in Application Insights][Esaminare le metriche in Application Insights]
-   [Ricerche nei log di diagnostica][Ricerche nei log di diagnostica]
-   [Verifica della disponibilità mediante test Web][Verifica della disponibilità mediante test Web]
-   [Verifica dell'utilizzo con eventi e metriche][Verifica dell'utilizzo con eventi e metriche]
-   [Domande e risposte e risoluzione dei problemi][Domande e risposte e risoluzione dei problemi]

<!--Link references-->

  [Aggiungere Application Insights al progetto in Visual Studio]: ../app-insights-monitor-application-health-usage/
  [Diagnosticare i problemi in un servizio Web attivo ora]: ../app-insights-monitor-performance-live-website-now/
  [versione precedente di Application Insights]: http://msdn.microsoft.com/it-it/library/dn481095.aspx
  [Esempio di monitoraggio delle applicazioni in Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Application Insights]: ../app-insights-get-started/
  [Esaminare le metriche in Application Insights]: ../app-insights-explore-metrics/
  [Ricerche nei log di diagnostica]: ../app-insights-search-diagnostic-logs/
  [Verifica della disponibilità mediante test Web]: ../app-insights-monitor-web-app-availability/
  [Verifica dell'utilizzo con eventi e metriche]: ../app-insights-web-track-usage-custom-events-metrics/
  [Domande e risposte e risoluzione dei problemi]: ../app-insights-troubleshoot-faq/
