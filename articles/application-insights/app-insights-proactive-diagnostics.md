---
title: Rilevamento intelligente in Application Insights | Documentazione Microsoft
description: Application Insights esegue automaticamente un&quot;analisi approfondita dei dati di telemetria dell&quot;app e segnala potenziali problemi.
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 63c901529b81c75f46f1b21219054817c148063a
ms.openlocfilehash: 30124e697af5f7560e8070ce43c5a985bd2c403b


---
# <a name="smart-detection-in-application-insights"></a>Rilevamento intelligente in Application Insights
 Il rilevamento intelligente segnala automaticamente i potenziali problemi di prestazioni nell'applicazione Web. Esegue l'analisi proattiva dei dati di telemetria che l'app invia ad [Application Insights](app-insights-overview.md). Se si verifica un improvviso aumento della percentuale di errori o in caso di modelli anomali delle prestazioni di client o server, viene generato un avviso. Questa funzionalità non richiede alcuna configurazione. Funziona se l'applicazione invia dati di telemetria sufficienti.

È possibile accedere agli avvisi della funzionalità di rilevamento intelligente dai messaggi di posta elettronica ricevuti e dal pannello Smart Detection (Rilevamento intelligente).

## <a name="review-your-smart-detections"></a>Esaminare i rilevamenti intelligenti
È possibile individuare i rilevamenti in due modi:

* **Viene visualizzato un messaggio di posta elettronica** da Application Insights. Ecco un esempio tipico:
  
    ![Avviso di posta elettronica](./media/app-insights-proactive-diagnostics/03.png)
  
    Fare clic sul pulsante grande per visualizzare altri dettagli nel portale.
* Il **riquadro Smart Detection (Rilevamento intelligente)** nel pannello della panoramica dell'app visualizza il numero di avvisi recenti. Fare clic sul riquadro per visualizzare un elenco degli avvisi recenti.

![Visualizzare rilevamenti recenti](./media/app-insights-proactive-diagnostics/04.png)

Selezionare un avviso per visualizzarne i dettagli.

## <a name="what-problems-are-detected"></a>Tipi di problemi rilevati
Esistono tre tipologie di rilevamento:

* [Rilevamento intelligente: anomalie negli errori](app-insights-proactive-failure-diagnostics.md). Si usa Machine Learning per impostare la frequenza prevista delle richieste non riuscite per l'app, in correlazione con il carico e altri fattori. Se la percentuale di errori supera la prevista, viene inviato un avviso.
* [Rilevamento intelligente: anomalie nelle prestazioni](app-insights-proactive-performance-diagnostics.md). Vengono cercati ogni giorno i modelli anomali nei tempi di risposta e nelle percentuali di errore. Questi problemi vengono correlati con proprietà, ad esempio percorso, browser, sistema operativo client, istanza del server e ora del giorno.
* [Rilevamento intelligente: problemi del servizio cloud di Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Vengono inviati avvisi all'utente se l'app è ospitata in Servizi cloud di Azure e un'istanza del ruolo presenta errori di avvio, ricicli frequenti o arresti anomali del sistema in fase di esecuzione.

I collegamenti della Guida in ogni notifica consentono di vedere gli articoli pertinenti.

## <a name="next-steps"></a>Passaggi successivi
Gli strumenti di diagnostica seguenti consentono di controllare la telemetria dall'app:

* [Esplora metriche](app-insights-metrics-explorer.md)
* [Esplora ricerche](app-insights-diagnostic-search.md)
* [Linguaggio avanzato di query di Analisi](app-insights-analytics-tour.md)

Il rilevamento intelligente è completamente automatico, tuttavia è possibile configurare avvisi aggiuntivi, se necessario.

* [Configurare manualmente gli avvisi relativi alle metriche](app-insights-alerts.md)
* [Test Web di disponibilità](app-insights-monitor-web-app-availability.md) 




<!--HONumber=Nov16_HO3-->


