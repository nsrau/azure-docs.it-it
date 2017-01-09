---
title: Mappa delle applicazioni in Application Insights | Microsoft Docs
description: Una rappresentazione visiva delle dipendenze tra i componenti di app, contrassegnati con avvisi e indicatori KPI.
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: douge
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc2d3c3fee5abbab0eab16c106c88c8753e703cc
ms.openlocfilehash: f7ee8a43c83b1bad53acdb71de7a66d8afaa09b2


---
# <a name="application-map-in-application-insights"></a>Mappa delle applicazioni in Application Insights
In [Azure Application Insights](app-insights-overview.md), Mappa delle applicazioni è un layout visivo delle relazioni di dipendenza dei componenti dell'applicazione. Ogni componente mostra gli indicatori KPI, ad esempio carico, prestazioni, errori e avvisi per individuare eventuali componenti che generano un errore o un problema di prestazioni. È possibile fare clic da qualsiasi componente per ottenere una diagnostica più dettagliata, ad esempio sugli eventi di Application Insights. Se l'app usa i servizi di Azure, è possibile anche fare clic sulla diagnostica di Azure, ad esempio per consigli di Advisor su database SQL.

Come altri tipi di grafico, è possibile aggiungere una mappa delle applicazioni al dashboard di Azure, in cui è completamente funzionale. 

## <a name="open-the-application-map"></a>Aprire la mappa delle applicazioni
Aprire la mappa nel pannello di panoramica dell'applicazione:

![aprire la mappa delle app](./media/app-insights-app-map/01.png)

![mappa delle app](./media/app-insights-app-map/02.png)

La mappa mostra:

* Test della disponibilità
* Componente lato client (monitorato con JavaScript SDK)
* Componente lato server
* Dipendenze dei componenti client e server

È possibile espandere e comprimere i gruppi di collegamento di dipendenza:

![comprimere](./media/app-insights-app-map/03.png)

Se si dispone di numerose dipendenze di un tipo (SQL, HTTP e così via), possono essere visualizzate raggruppate. 

![dipendenze raggruppate](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Individuazione di problemi
Ogni nodo dispone di indicatori di prestazioni rilevanti, ad esempio tassi di carico, prestazioni ed errori per il componente. 

Le icone di avviso evidenziano possibili problemi. Un avviso di colore arancione indica che si verificano errori nelle richieste, visualizzazioni di pagina o chiamate di dipendenza. Il rosso indica una percentuale di errore superiore al 5%.

![icone di errore](./media/app-insights-app-map/04.png)

Vengono visualizzati anche avvisi attivi: 

![avvisi attivi](./media/app-insights-app-map/05.png)

Se si usa SQL Azure, è presente un'icona che indica quando sono disponibili consigli su come è possibile migliorare le prestazioni. 

![Suggerimento di Azure](./media/app-insights-app-map/06.png)

Per ottenere altri dettagli, fare clic su qualsiasi icona:

![Suggerimento di Azure](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Diagnostica tramite clic
Ciascun nodo della mappa permette una diagnostica tramite clic mirati. Le opzioni variano a seconda del tipo del nodo.

![opzioni server](./media/app-insights-app-map/09.png)

Per i componenti ospitati in Azure, le opzioni includono collegamenti diretti a essi.

## <a name="filters-and-time-range"></a>Filtri e intervallo di tempo
Per impostazione predefinita, la mappa riepiloga tutti i dati disponibili per l'intervallo di tempo scelto. è possibile filtrarlo in modo da includere solo i nomi di operazioni o dipendenze specifici.

* Nome dell'operazione: include sia le visualizzazioni pagina che i tipi di richiesta lato server. Con questa opzione, la mappa mostra l'indicatore KPI nel nodo lato server/client solo per le operazioni selezionate. Mostra le dipendenze chiamate nel contesto di tali operazioni specifiche.
* Nome di base delle dipendenze: include le dipendenze del browser AJAX e lato server. Se si segnalano dati di telemetria di dipendenza personalizzati con l'API TrackDependency, questi vengono visualizzati anche qui. È possibile selezionare le dipendenze da mostrare sulla mappa. Attualmente, questa selezione non filtra le richieste lato server o le visualizzazioni di pagina lato client.

![impostare i filtri](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Salvare i filtri
Per salvare i filtri applicati, bloccare la visualizzazione filtrata su un [dashboard](app-insights-dashboards.md).

![Aggiungi al dashboard](./media/app-insights-app-map/12.png)

## <a name="end-to-end-system-app-maps"></a>Mappe delle app del sistema end-to-end

Se l'applicazione include diversi componenti, ad esempio un servizio back-end oltre all'App Web, è anche possibile visualizzarli tutti in una mappa integrata delle app.

![impostare i filtri](./media/app-insights-app-map/multi-component-app-map.png)

La mappa delle app consente di trovare i nodi del server mediante la ricerca di tutte le risorse di Application Insights all'interno del gruppo di risorse corrente. Rileva inoltre i nodi server seguendo le chiamate di dipendenza di cui le risorse di Application Insights hanno tenuto traccia nel gruppo di risorse corrente.


### <a name="setting-up"></a>Configurazione

> [!NOTE] 
> La mappa delle app del sistema end-to-end è in versione di anteprima. È necessario instrumentare i componenti con una versione speciale dell'SDK, inoltre è necessario usare un URL speciale per visualizzare la mappa delle app. [Informazioni su come configurare le mappe delle app del sistema end-to-end](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-app-map-preview.md).


## <a name="feedback"></a>Commenti e suggerimenti
[Fornire un feedback tramite l'opzione di feedback del portale](app-insights-get-dev-support.md).

![Immagine MapLink-1](./media/app-insights-app-map/13.png)




<!--HONumber=Nov16_HO4-->


