---
title: Analisi delle tendenze in Visual Studio | Microsoft Docs
description: Analizzare, visualizzare ed esaminare le tendenze nei dati di telemetria di Application Insights in Visual Studio.
services: application-insights
documentationcenter: .net
author: numberbycolors
manager: douge
ms.assetid: 3150c6fc-2691-44f6-a290-fc5cd68e692a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: daviste
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: c0c1eb80685d18794bcae7acaa16b777ff21b3e6
ms.lasthandoff: 03/21/2017


---
# <a name="analyzing-trends-in-visual-studio"></a>Analisi delle tendenze in Visual Studio
Lo strumento Tendenze di Application Insights visualizza la variazione nel tempo degli eventi di telemetria importanti dell'app Web, consentendo di identificare rapidamente problemi e anomalie. Collegando l'utente a informazioni di diagnostica più dettagliate, Tendenze consente di migliorare le prestazioni dell'app, ricostruire le cause delle eccezioni e individuare informazioni utili dagli eventi personalizzati.

![Finestra Tendenze di esempio](./media/app-insights-visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Configurare l'app Web per Application Insights

Se non è ancora stato fatto, [configurare l'app Web per Application Insights](app-insights-overview.md). Questa operazione consente all'app di inviare dati di telemetria al portale di Application Insights, dove lo strumento Tendenze legge i dati di telemetria.

Tendenze di Application Insights è disponibile in Visual Studio 2015 Update 3 e versioni successive.

## <a name="open-application-insights-trends"></a>Aprire Tendenze di Application Insights
Aprire la finestra Tendenze di Application Insights in uno dei modi seguenti:

* Dal pulsante della barra degli strumenti di Application Insights scegliere **Esplora tendenze di telemetria**
* Dal menu di scelta rapida del progetto scegliere **Application Insights > Esplora tendenze di telemetria**
* Sulla barra dei menu di Visual Studio scegliere **Visualizza > Altre finestre > Tendenze di Application Insights**

Potrebbe essere visualizzata la richiesta di selezionare una risorsa. Fare clic su **Seleziona una risorsa**, accedere con una sottoscrizione di Azure e quindi scegliere nell'elenco una risorsa di Application Insights di cui si vogliono analizzare le tendenze di telemetria.

## <a name="choose-a-trend-analysis"></a>Scegliere un'analisi delle tendenze
![Menu dei tipi comuni di analisi delle tendenze](./media/app-insights-visual-studio-trends/app-insights-trends-1-750.png)

Per iniziare, scegliere una di cinque analisi comuni delle tendenze, ognuna delle quali analizza i dati delle ultime 24 ore.

* **Analisi dei problemi di prestazioni relativi alle richieste server** : richieste effettuate al servizio, raggruppate in base ai tempi di risposta
* **Analisi degli errori nelle richieste server** : richieste effettuate al servizio, raggruppate in base al codice di risposta HTTP
* **Esame delle eccezioni presenti nell'applicazione** : eccezioni provenienti dal servizio, raggruppate in base al tipo di eccezione
* **Verifica delle prestazioni delle dipendenze dell'applicazione** : servizi chiamati dal servizio, raggruppati in base ai tempi di risposta
* **Esame degli eventi personalizzati** : eventi personalizzati configurati per il servizio, raggruppati in base al tipo di evento

Queste analisi predefinite sono accessibili successivamente dal pulsante **Visualizza tipi comuni di analisi dei dati di telemetria** nell'angolo superiore sinistro della finestra Tendenze.

## <a name="visualize-trends-in-your-application"></a>Visualizzare le tendenze nell'applicazione
Tendenze di Application Insights crea una visualizzazione basata su serie temporale dei dati di telemetria dell'app. Ogni visualizzazione basata su serie temporale contiene un tipo di dati di telemetria, raggruppati in base a una proprietà di tali dati, in un determinato intervallo di tempo. È ad esempio possibile visualizzare le richieste server, raggruppate per paese di provenienza, nelle ultime 24 ore. In questo esempio, ogni bolla nella visualizzazione rappresenterà un conteggio delle richieste server di un determinato paese/area geografica durante un'ora.

Usare i controlli nella parte superiore della finestra per modificare i tipi di dati di telemetria visualizzati. Prima di tutto, scegliere i tipi di dati di telemetria a cui si è interessati.

* **Tipo di telemetria** : richieste server, eccezioni, dipendenze o eventi personalizzati
* **Intervallo di tempo** : qualsiasi intervallo dagli ultimi 30 minuti agli ultimi 3 giorni
* **Raggruppa per** : tipo di eccezione, ID problema, paese/area geografica e altro ancora

Fare quindi clic su **Analizza telemetria** per eseguire la query.

Per esplorare le bolle nella visualizzazione:

* Fare clic per selezionare una bolla. In questo modo vengono aggiornati i filtri nella parte inferiore della finestra in modo da riepilogare solo gli eventi che si sono verificati durante uno specifico periodo di tempo.
* Fare doppio clic su una bolla per passare allo strumento Ricerca e visualizzare tutti i singoli eventi di telemetria che si sono verificati durante tale periodo di tempo.
* Fare clic tenendo premuto CTRL su una bolla per deselezionarla nella visualizzazione.

> [!TIP]
> Gli strumenti Analisi e Ricerca interagiscono per consentire all'utente di individuare le cause dei problemi nel servizio tra migliaia di eventi di telemetria. Se in un pomeriggio i clienti notano che l'app ha una velocità di risposta inferiore, ad esempio, iniziare con Analisi. Analizzare le richieste effettuate al servizio nelle ultime ore, raggruppate in base al tempo di risposta, e osservare se sono presenti gruppi insolitamente consistenti di richieste lente. Fare quindi clic sulla bolla per passare allo strumento Ricerca, filtrato in base a tali eventi di richiesta. Da Ricerca è possibile esaminare il contenuto di tali richieste e passare al codice coinvolto per risolvere il problema.
> 
> 

## <a name="filter"></a>Filtro
I controlli di filtro nella parte inferiore della finestra consentono di individuare tendenze più specifiche. Per applicare un filtro, fare clic sul relativo nome. È possibile passare rapidamente da un filtro all'altro per individuare tendenze eventualmente nascoste in una determinata dimensione della telemetria. Se si applica un filtro in una dimensione, ad esempio Tipo di eccezione, i filtri nelle altre dimensioni rimangono selezionabili anche se visualizzati in grigio. Per annullare l'applicazione di un filtro, fare clic di nuovo su di esso. Fare clic tenendo premuto CTRL per selezionare più filtri nella stessa dimensione.

![Filtri delle tendenze](./media/app-insights-visual-studio-trends/TrendsFiltering-750.png)

Per applicare più filtri: 

1. Applicare il primo filtro. 
2. Fare clic sul pulsante **Apply selected filters and query again** (Applica filtri selezionati e ripeti query) accanto al nome della dimensione del primo filtro. Verrà così eseguita di nuovo la query sui dati di telemetria solo per gli eventi corrispondenti al primo filtro. 
3. Applicare un secondo filtro. 
4. Ripetere il processo per trovare tendenze in specifici sottoinsiemi dei dati di telemetria. Ad esempio, nelle richieste server denominate "GET Home/Index" *e* provenienti dalla Germania *e* che hanno ricevuto un codice di risposta 500. 

Per annullare l'applicazione di uno di questi filtri, fare clic sul pulsante **Remove selected filters and query again** (Rimuovi filtri selezionati e ripeti query) della dimensione.

![Filtri multipli](./media/app-insights-visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Trovare le anomalie
Lo strumento Tendenze consente di evidenziare bolle di eventi che presentano anomalie rispetto ad altre bolle nella stessa serie temporale. Nell'elenco a discesa Tipo di visualizzazione scegliere **Conteggi nell'intervallo di tempo (evidenziazione delle anomalie)** o **Percentuali nell'intervallo di tempo (evidenziazione delle anomalie)**. Le bolle di colore rosso sono anomale. Si definiscono anomalie le bolle con conteggi/percentuali che superano di 2,1 volte la deviazione standard dei conteggi o delle percentuali negli ultimi due intervalli di tempo (48 ore se si visualizzano le ultime 24 ore e così via).

![I punti colorati indicano anomalie](./media/app-insights-visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> L'evidenziazione delle anomalie è particolarmente utile per trovare in una serie temporale di piccole bolle outlier che potrebbero altrimenti sembrare di dimensioni simili.  
> 
> 

## <a name="next"></a>Passaggi successivi
|  |  |
| --- | --- |
| **[Uso di Application Insights in Visual Studio](app-insights-visual-studio.md)**<br/>Ricerca sui dati di telemetria, visualizzazione dei dati in CodeLens e configurazione di Application Insights. Tutto in Visual Studio. |![Fare clic con il pulsante destro del mouse sul progetto e scegliere Application Insights, Cerca.](./media/app-insights-visual-studio-trends/34.png) |
| **[Aggiungere altri dati](app-insights-asp-net-more.md)**<br/>Monitorare l'utilizzo, la disponibilità, le dipendenze e le eccezioni, integrare le tracce dei framework di registrazione e scrivere telemetria personalizzata. |![Visual Studio](./media/app-insights-visual-studio-trends/64.png) |
| **[Uso del portale Application Insights](app-insights-dashboards.md)**<br/>Dashboard, strumenti avanzati di diagnostica e di analisi, avvisi, mappa attiva delle dipendenze dell'applicazione ed esportazione dei dati di telemetria. |![Visual Studio](./media/app-insights-visual-studio-trends/62.png) |


