---
title: Rilevamento intelligente in Azure Application Insights | Microsoft Docs
description: Application Insights esegue automaticamente un'analisi approfondita dei dati di telemetria dell'app e segnala potenziali problemi.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: 8ee2dea364253d871d5624242d15d8a81ab6f08f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465904"
---
# <a name="smart-detection-in-application-insights"></a>Rilevamento intelligente in Application Insights
 Rilevamento intelligente segnala automaticamente i potenziali problemi di prestazioni e le anomalie degli errori nell'applicazione web. Esegue l'analisi proattiva dei dati di telemetria che l'app invia ad [Application Insights](../../azure-monitor/app/app-insights-overview.md). Se si verifica un improvviso aumento della percentuale di errori o in caso di modelli anomali delle prestazioni di client o server, viene generato un avviso. Questa funzionalità non richiede alcuna configurazione. Funziona se l'applicazione invia dati di telemetria sufficienti.

È possibile accedere rilevamenti vystavitel rilevamento intelligente dai messaggi di posta elettronica ricevuti e dal pannello rilevamento intelligente.

## <a name="review-your-smart-detections"></a>Esaminare i rilevamenti intelligenti
È possibile individuare i rilevamenti in due modi:

* **Viene visualizzato un messaggio di posta elettronica** da Application Insights. Ecco un esempio tipico:
  
    ![Avviso di posta elettronica](./media/proactive-diagnostics/03.png)
  
    Fare clic sul pulsante grande per visualizzare altri dettagli nel portale.
* **Il pannello di rilevamento intelligente** in Application Insights. Selezionare **rilevamento intelligente** sotto il **indagare** menu per visualizzare un elenco di rilevamenti recenti.

![Visualizzare rilevamenti recenti](./media/proactive-diagnostics/04.png)

Selezionare un rilevamento per visualizzarne i dettagli.

## <a name="what-problems-are-detected"></a>Tipi di problemi rilevati
Rilevamento intelligente rileva e invia una notifica su una serie di problemi, ad esempio:

* [Rilevamento intelligente: anomalie negli errori](../../azure-monitor/app/proactive-failure-diagnostics.md). Si usa Machine Learning per impostare la frequenza prevista delle richieste non riuscite per l'app, in correlazione con il carico e altri fattori. Se la percentuale di errori supera la prevista, viene inviato un avviso.
* [Rilevamento intelligente: anomalie nelle prestazioni](../../azure-monitor/app/proactive-performance-diagnostics.md). Vengono inviate notifiche se il tempo di risposta di un'operazione o la durata delle dipendenze sta rallentando rispetto alla baseline cronologica o se viene identificato un modello anomalo nel tempo di risposta o di caricamento pagina.   
* Riduzioni delle prestazioni generali e i problemi, quali [traccia peggioramento](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [perdita di memoria](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [aumento anomalo nel volume delle eccezioni](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) e [anti-modelli di sicurezza](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

I collegamenti della Guida in ogni notifica consentono di vedere gli articoli pertinenti.

## <a name="smart-detection-email-notifications"></a>Notifiche di posta elettronica di Rilevamento intelligente

Tutte le regole di rilevamento intelligente, ad eccezione delle regole contrassegnate come _preview_, sono configurate per impostazione predefinita per inviare notifiche tramite posta elettronica quando vengono rilevati i rilevamenti.

È possibile configurare notifiche di posta elettronica per una determinata regola di Rilevamento intelligente aprendo il pannello **Impostazioni** di Rilevamento intelligente e selezionando la regola, che aprirà il pannello **Modifica regola**.

In alternativa, è possibile modificare la configurazione usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Gestire le regole di rilevamento intelligente di Application Insights usando modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passaggi successivi
Gli strumenti di diagnostica seguenti consentono di controllare la telemetria dall'app:

* [Esplora metriche](../../azure-monitor/app/metrics-explorer.md)
* [Esplora ricerche](../../azure-monitor/app/diagnostic-search.md)
* [Linguaggio avanzato di query di Analisi](../../azure-monitor/log-query/get-started-portal.md)

Il rilevamento intelligente è completamente automatico, tuttavia è possibile configurare avvisi aggiuntivi, se necessario.

* [Configurare manualmente gli avvisi relativi alle metriche](../../azure-monitor/app/alerts.md)
* [Test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) 

