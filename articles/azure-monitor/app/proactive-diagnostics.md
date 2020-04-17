---
title: Rilevamento intelligente in Azure Application Insights | Microsoft Docs
description: Application Insights esegue automaticamente un'analisi approfondita dei dati di telemetria dell'app e segnala potenziali problemi.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: ff9f88e1d2e643d04c4417283420217e7d496caf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536795"
---
# <a name="smart-detection-in-application-insights"></a>Rilevamento intelligente in Application Insights
 Rilevamento intelligente avvisa automaticamente l'utente di potenziali problemi di prestazioni e anomalie di errore nell'applicazione Web. Esegue l'analisi proattiva dei dati di telemetria che l'app invia ad [Application Insights](../../azure-monitor/app/app-insights-overview.md). Se si verifica un improvviso aumento della percentuale di errori o in caso di modelli anomali delle prestazioni di client o server, viene generato un avviso. Questa funzionalità non richiede alcuna configurazione. Funziona se l'applicazione invia dati di telemetria sufficienti.

È possibile accedere ai rilevamenti emessi da Smart Detection sia dalle e-mail ricevute che dal pannello Rilevamento intelligente.

## <a name="review-your-smart-detections"></a>Esaminare i rilevamenti intelligenti
È possibile individuare i rilevamenti in due modi:

* **Viene visualizzato un messaggio di posta elettronica** da Application Insights. Ecco un esempio tipico:
  
    ![Avviso di posta elettronica](./media/proactive-diagnostics/03.png)
  
    Fare clic sul pulsante grande per visualizzare altri dettagli nel portale.
* **Il pannello Rilevamento intelligente** in Application Insights. Selezionare **Rilevamento intelligente** nel menu **Esamina** per visualizzare un elenco di rilevamenti recenti.

![Visualizzare rilevamenti recenti](./media/proactive-diagnostics/04.png)

Selezionare un rilevamento per visualizzarne i dettagli.

## <a name="what-problems-are-detected"></a>Tipi di problemi rilevati
Smart Detection rileva e notifica una serie di problemi, ad esempio:

* [Rilevamento intelligente: anomalie negli errori](../../azure-monitor/app/proactive-failure-diagnostics.md). Si usa Machine Learning per impostare la frequenza prevista delle richieste non riuscite per l'app, in correlazione con il carico e altri fattori. Se la percentuale di errori supera la prevista, viene inviato un avviso.
* [Rilevamento intelligente: anomalie nelle prestazioni](../../azure-monitor/app/proactive-performance-diagnostics.md). Vengono inviate notifiche se il tempo di risposta di un'operazione o la durata delle dipendenze sta rallentando rispetto alla baseline cronologica o se viene identificato un modello anomalo nel tempo di risposta o di caricamento pagina.   
* Le degradazioni generali e i problemi, ad esempio [Trace degradation](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [Perdita di memoria](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [Aumento anomalo del volume delle eccezioni](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) e [anti-modelli](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack)di sicurezza .

I collegamenti della Guida in ogni notifica consentono di vedere gli articoli pertinenti.

## <a name="smart-detection-email-notifications"></a>Notifiche di posta elettronica di Rilevamento intelligente

Tutte le regole di rilevamento intelligente, ad eccezione delle regole contrassegnate come _anteprima,_ sono configurate per impostazione predefinita per l'invio di notifiche e-mail quando vengono rilevati rilevamenti.

È possibile configurare notifiche di posta elettronica per una determinata regola di Rilevamento intelligente aprendo il pannello **Impostazioni** di Rilevamento intelligente e selezionando la regola, che aprirà il pannello **Modifica regola**.

In alternativa, è possibile modificare la configurazione usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Gestire le regole di rilevamento intelligente di Application Insights usando modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passaggi successivi
Gli strumenti di diagnostica seguenti consentono di controllare la telemetria dall'app:

* [Esplora metriche](../../azure-monitor/platform/metrics-charts.md)
* [Esplora ricerche](../../azure-monitor/app/diagnostic-search.md)
* [Linguaggio avanzato di query di Analisi](../../azure-monitor/log-query/get-started-portal.md)

Il rilevamento intelligente è completamente automatico, tuttavia è possibile configurare avvisi aggiuntivi, se necessario.

* [Configurare manualmente gli avvisi relativi alle metriche](../../azure-monitor/app/alerts.md)
* [Test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) 

