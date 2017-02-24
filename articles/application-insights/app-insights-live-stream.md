---
title: Flusso metriche attive in Azure Application Insights | Documentazione Microsoft
description: Monitorare le prestazioni dell&quot;app Web in tempo reale, per osservare gli effetti di una versione o altra modifica.
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: c453774b04c7005ce5948617beea8770b499b88d
ms.openlocfilehash: 2da6d02055616db5f9854481054ff12c69dc4801


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>Flusso di metriche live: metriche istantanee per un monitoraggio dettagliato
Flusso metriche attive mostra le metriche [Application Insights](app-insights-overview.md) in questo esatto momento, con una latenza quasi in tempo reale di un secondo. Questo monitoraggio immediato è molto utile quando si sta rilasciando una nuova compilazione e si desidera assicurarsi che tutto funzioni come previsto oppure si sta indagando su un evento imprevisto in tempo reale.

![Nel pannello Panoramica fare clic su Flusso attivo](./media/app-insights-live-stream/live-stream.png)

A differenza di [Esplora metriche](app-insights-metrics-explorer.md), Flusso metriche attive consente di visualizzare un set fisso di metriche. I dati vengono mantenuti finché  si trovano nel grafico, poi vengono eliminati.

I dati di Flusso metriche attive sono gratuiti e non vengono addebitati in fattura.

## <a name="live-failures"></a>Errori live

Se vengono registrati errori o eccezioni, Live Stream ne seleziona un esempio. Fare clic su **Pausa** per bloccare un esempio specifico e selezionare un evento per visualizzarne i dettagli.

![Errori live campionati](./media/app-insights-live-stream/live-stream-failures.png)


Live Metrics Stream è disponibile con la versione più recente di [Application Insights SDK per il Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/).



## <a name="troubleshooting"></a>Risoluzione dei problemi

Dati non visualizzati Flusso metriche attive usa porte diverse da quelle di altri dati di telemetria di Application Insights. Assicurarsi che [tali porte](app-insights-ip-addresses.md) siano aperte nel firewall.



## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio dell'utilizzo con Application Insights](app-insights-overview-usage.md)
* [Uso di Ricerca diagnostica](app-insights-diagnostic-search.md)




<!--HONumber=Feb17_HO2-->


