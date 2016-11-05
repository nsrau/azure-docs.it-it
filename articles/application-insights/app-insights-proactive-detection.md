---
title: Rilevamento proattivo in Application Insights | Microsoft Docs
description: Application Insights esegue automaticamente un'analisi approfondita dei dati di telemetria dell'app e segnala potenziali problemi.
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: awills

---
# Rilevamento proattivo in Application Insights
 Il rilevamento proattivo segnala automaticamente i potenziali problemi di prestazioni nell'applicazione Web. Esegue l'analisi intelligente dei dati di telemetria che l'applicazione invia a [Visual Studio Application Insights](app-insights-overview.md). Se si verifica un improvviso aumento della percentuale di errori o in caso di modelli anomali delle prestazioni di client o server, viene generato un avviso. Questa funzionalità non richiede alcuna configurazione. Funziona se l'applicazione invia dati di telemetria sufficienti.

È possibile accedere gli avvisi della funzionalità Rilevamento proattivo dai messaggi di posta elettronica ricevuti e dal pannello Rilevamento proattivo.

## Esaminare i rilevamenti proattivi
È possibile individuare i rilevamenti in due modi:

* **Viene visualizzato un messaggio di posta elettronica** da Application Insights. Ecco un esempio tipico:
  
    ![Avviso di posta elettronica](./media/app-insights-proactive-detection/03.png)
  
    Fare clic sul pulsante grande per visualizzare altri dettagli nel portale.
* Il **riquadro Rilevamento proattivo** nel pannello della panoramica dell'app visualizza il numero di avvisi recenti. Fare clic sul riquadro per visualizzare un elenco degli avvisi recenti.

![Visualizzare rilevamenti recenti](./media/app-insights-proactive-detection/04.png)

Selezionare un avviso per visualizzarne i dettagli.

## Tipi di problemi rilevati
Esistono tre tipologie di rilevamento:

* [Avvisi di errore quasi in tempo reale](app-insights-proactive-failure-diagnostics.md). Si usa Machine Learning per impostare la frequenza prevista delle richieste non riuscite per l'app, in correlazione con il carico e altri fattori. Se la percentuale di errori supera la prevista, viene inviato un avviso.
* [Diagnostica delle anomalie](app-insights-proactive-anomaly-diagnostics.md). Vengono cercati ogni giorno i modelli anomali nei tempi di risposta e nelle percentuali di errore. Questi problemi vengono correlati con proprietà, ad esempio percorso, browser, sistema operativo client, istanza del server e ora del giorno.
* [Servizi cloud di Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Vengono inviati avvisi all'utente se l'app è ospitata in Servizi cloud di Azure e un'istanza del ruolo presenta errori di avvio, ricicli frequenti o arresti anomali del sistema in fase di esecuzione.

I collegamenti della Guida in ogni notifica consentono di vedere gli articoli pertinenti.

## Passaggi successivi
Gli strumenti di diagnostica seguenti consentono di controllare la telemetria dall'app:

* [Esplora metriche](app-insights-metrics-explorer.md)
* [Esplora ricerche](app-insights-diagnostic-search.md)
* [Linguaggio avanzato di query di Analisi](app-insights-analytics-tour.md)

Diagnostica proattiva è completamente automatica, tuttavia è possibile configurare avvisi aggiuntivi, se necessario.

* [Configurare manualmente gli avvisi relativi alle metriche](app-insights-alerts.md)
* [Test Web di disponibilità](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->