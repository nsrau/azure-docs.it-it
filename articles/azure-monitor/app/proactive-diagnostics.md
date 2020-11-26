---
title: Rilevamento intelligente in Azure Application Insights | Microsoft Docs
description: Application Insights esegue automaticamente un'analisi approfondita dei dati di telemetria dell'app e segnala potenziali problemi.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 2b6e9370387bf3bb87555261b3949db2917e9911
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186253"
---
# <a name="smart-detection-in-application-insights"></a>Rilevamento intelligente in Application Insights
 Il rilevamento intelligente segnala automaticamente i potenziali problemi di prestazioni e le anomalie degli errori nell'applicazione Web. Esegue l'analisi proattiva dei dati di telemetria che l'app invia ad [Application Insights](./app-insights-overview.md). Se si verifica un improvviso aumento della percentuale di errori o in caso di modelli anomali delle prestazioni di client o server, viene generato un avviso. Questa funzionalità non richiede alcuna configurazione. Funziona se l'applicazione invia dati di telemetria sufficienti.

È possibile accedere ai rilevamenti generati dal rilevamento intelligente dai messaggi di posta elettronica ricevuti e dal pannello Rilevamento intelligente.

## <a name="review-your-smart-detections"></a>Esaminare i rilevamenti intelligenti
È possibile individuare i rilevamenti in due modi:

* **Viene visualizzato un messaggio di posta elettronica** da Application Insights. Ecco un esempio tipico:
  
    ![Avviso di posta elettronica](./media/proactive-diagnostics/03.png)
  
    Fare clic sul pulsante grande per visualizzare altri dettagli nel portale.
* **Pannello Rilevamento intelligente** in Application Insights. Scegliere **Rilevamento intelligente** nel menu **Ricerca causa** per visualizzare un elenco di rilevamenti recenti.

![Visualizzare rilevamenti recenti](./media/proactive-diagnostics/04.png)

Selezionare un rilevamento per visualizzarne i dettagli.

## <a name="what-problems-are-detected"></a>Tipi di problemi rilevati
Il rilevamento intelligente rileva e notifica una serie di problemi, ad esempio:

* [Rilevamento intelligente: anomalie negli errori](./proactive-failure-diagnostics.md). Si usa Machine Learning per impostare la frequenza prevista delle richieste non riuscite per l'app, in correlazione con il carico e altri fattori. Se la percentuale di errori supera la prevista, viene inviato un avviso.
* [Rilevamento intelligente: anomalie nelle prestazioni](./proactive-performance-diagnostics.md). Vengono inviate notifiche se il tempo di risposta di un'operazione o la durata delle dipendenze sta rallentando rispetto alla baseline cronologica o se viene identificato un modello anomalo nel tempo di risposta o di caricamento pagina.   
* Riduzioni generali delle prestazioni e problemi, ad esempio [riduzione di traccia](./proactive-trace-severity.md), [perdita di memoria](./proactive-potential-memory-leak.md), [aumento anomalo nel volume delle eccezioni](./proactive-exception-volume.md) e [anti-pattern di sicurezza](./proactive-application-security-detection-pack.md).

I collegamenti della Guida in ogni notifica consentono di vedere gli articoli pertinenti.

## <a name="smart-detection-email-notifications"></a>Notifiche di posta elettronica di Rilevamento intelligente

Tutte le regole di Rilevamento intelligente, ad eccezione delle regole contrassegnate come _Anteprima_, sono configurate per impostazione predefinita per l'invio di notifiche tramite posta elettronica quando vengono identificati nuovi rilevamenti.

È possibile configurare notifiche di posta elettronica per una determinata regola di Rilevamento intelligente aprendo il pannello **Impostazioni** di Rilevamento intelligente e selezionando la regola, che aprirà il pannello **Modifica regola**.

In alternativa, è possibile modificare la configurazione usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Gestire le regole di rilevamento intelligente di Application Insights usando modelli di Azure Resource Manager](./proactive-arm-config.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passaggi successivi
Gli strumenti di diagnostica seguenti consentono di controllare la telemetria dall'app:

* [Esplora metriche](../platform/metrics-charts.md)
* [Esplora ricerche](./diagnostic-search.md)
* [Linguaggio avanzato di query di Analisi](../log-query/log-analytics-tutorial.md)

Il rilevamento intelligente è completamente automatico, tuttavia è possibile configurare avvisi aggiuntivi, se necessario.

* [Configurare manualmente gli avvisi relativi alle metriche](../platform/alerts-log.md)
* [Test Web di disponibilità](./monitor-web-app-availability.md)