---
title: Diagnosticare i problemi di prestazioni con Azure Application Insights | Microsoft Docs
description: Esercitazione per il rilevamento e la diagnosi dei problemi di prestazioni in un'applicazione con Azure Application Insights.
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 1176e6ac33db5b9428a323c3a6271818807afc72
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Rilevare e diagnosticare i problemi di prestazioni con Azure Application Insights

Azure Application Insights raccoglie i dati di telemetria dall'applicazione per consentire di analizzarne il funzionamento e le prestazioni.  È possibile usare queste informazioni per identificare i problemi che potrebbero verificarsi o per individuare i miglioramenti da apportare all'applicazione che potrebbero influire maggiormente sugli utenti.  Questa esercitazione illustra il processo di analisi delle prestazioni sia dei componenti server dell'applicazione che dalla prospettiva del client.  Si apprenderà come:

> [!div class="checklist"]
> * Identificare le prestazioni delle operazioni sul lato server
> * Analizzare le operazioni del server per determinare la causa radice del rallentamento delle prestazioni
> * Identificare le operazioni con maggiori rallentamenti sul lato client
> * Analizzare i dettagli delle visualizzazioni pagina usando il linguaggio di query


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
    - Sviluppo Web e ASP.NET
    - Sviluppo di Azure
- Distribuire un'applicazione .NET in Azure e [abilitare Application Insights SDK](app-insights-asp-net.md).
- [Abilitare Application Insights Profiler](app-insights-profiler.md#enable-the-profiler) per l'applicazione.

## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identificare le operazioni del server con maggiori rallentamenti
Application Insights raccoglie i dettagli sulle prestazioni per le diverse operazioni nell'applicazione.  Identificando le operazioni con la durata maggiore, è possibile diagnosticare potenziali problemi o supportare meglio le attività di sviluppo continuative per migliorare le prestazioni complessive dell'applicazione.

1. Selezionare **Application Insights** e quindi selezionare la sottoscrizione in uso.  
1. Per aprire il pannello **Prestazioni**, selezionare **Prestazioni** nel menu **Analisi** oppure fare clic sul grafico **Tempo di risposta del server**.

    ![Prestazioni](media/app-insights-tutorial-performance/performance.png)

2. Il pannello **Prestazioni** mostra il conteggio e la durata media di ogni operazione per l'applicazione.  È possibile usare queste informazioni per identificare le operazioni che influiscono maggiormente sugli utenti. In questo esempio le operazioni **GET Customers/Details** e **GET Home/Index** sono probabili candidati per l'analisi a causa della durata e del numero di chiamate relativamente elevati.  Altre operazioni potrebbero avere una durata maggiore ma sono state chiamate raramente, pertanto l'effetto del loro miglioramento sarebbe minimo.  

    ![Pannello Prestazioni](media/app-insights-tutorial-performance/performance-blade.png)

3. Il grafico mostra attualmente la durata media di tutte le operazioni nel corso del tempo.  Aggiungere le operazioni a cui si è interessati aggiungendole al grafico.  Quest'ultimo indica la presenza di alcuni picchi che sarebbe utile analizzare.  È possibile isolarli ulteriormente riducendo l'intervallo di tempo del grafico.

    ![Aggiungere operazioni](media/app-insights-tutorial-performance/pin-operations.png)

4.  Fare clic su un'operazione per visualizzare il relativo pannello delle prestazioni a destra. È riportata la distribuzione delle durate per le diverse richieste.  In genere gli utenti si accorgono del rallentamento delle prestazioni quando impiegano circa metà secondo, pertanto ridurre l'intervallo di tempo alle richieste superiori ai 500 millisecondi.  

    ![Distribuzione della durata](media/app-insights-tutorial-performance/duration-distribution.png)

5.  In questo esempio si può notare che un numero significativo di richieste impiega oltre un secondo per l'elaborazione. È possibile visualizzare i dettagli di questa operazione facendo clic su **Dettagli dell'operazione**.

    ![Dettagli dell'operazione](media/app-insights-tutorial-performance/operation-details.png)

6.  Le informazioni raccolte finora confermano semplicemente un rallentamento delle prestazioni, senza individuarne la causa radice.  Il **Profiler** può essere utile poiché visualizza il codice effettivo che è stato eseguito per l'operazione e il tempo richiesto per ogni passaggio. Alcune operazioni potrebbero non avere una traccia dal momento che il profiler viene eseguito periodicamente.  Nel corso del tempo dovrebbero essere disponibili tracce per più operazioni.  Per avviare il profiler per l'operazione, fare clic su **Tracce Profiler**.
5.  La traccia mostra i singoli eventi per ogni operazione, in modo da poter diagnosticare la causa radice per la durata dell'operazione complessiva.  Fare clic su uno dei primi esempi, che hanno una durata maggiore.
6.  Fare clic su **Mostra percorso critico** per evidenziare il percorso specifico degli eventi che contribuiscono maggiormente alla durata totale dell'operazione.  In questo esempio è possibile vedere che la chiamata più lenta proviene dal metodo *FabrikamFiberAzureStorage.GetStorageTableData*. La parte che richiede più tempo è il metodo *CloudTable.CreateIfNotExist*. Se questa riga di codice viene eseguita ogni volta che viene chiamata la funzione, verranno usate chiamate di rete e risorse della CPU non necessarie. Il modo migliore per correggere il codice è inserire la linea in un metodo di avvio che si esegue solo una volta. 

    ![Dettagli del profiler](media/app-insights-tutorial-performance/profiler-details.png)

7.  Il **Suggerimento per le prestazioni** nella parte superiore della schermata supporta la valutazione che la durata eccessiva è dovuta all'attesa.  Fare clic sul collegamento **in attesa** per la documentazione sull'interpretazione dei diversi tipi di eventi.

    ![Suggerimento per le prestazioni](media/app-insights-tutorial-performance/performance-tip.png)

8.  Per un'ulteriore analisi, è possibile fare clic su **Scarica la traccia ETL** per scaricare la traccia in Visual Studio.

## <a name="use-analytics-data-for-server"></a>Usare i dati di analisi per il server
Analytics in Application Insights fornisce un linguaggio di query avanzato che consente di analizzare tutti i dati raccolti da Application Insights.  È possibile usare questo strumento per eseguire analisi approfondite sui dati delle richieste e delle prestazioni.

1. Tornare al pannello con i dettagli dell'operazione e fare clic sul pulsante Analytics.

    ![Pulsante Analytics](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Verrà aperta la finestra di Analytics in Application Insights con una query per ognuna delle visualizzazioni nel pannello.  È possibile eseguire queste query così come sono oppure modificarle in base alle proprie esigenze.  La prima query mostra la durata per questa operazione nel corso del tempo.

    ![Analytics](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identificare le operazioni del client con maggiori rallentamenti
Oltre a identificare i processi server da ottimizzare, Application Insights è in grado di analizzare il punto di vista dei browser client.  Ciò consente di identificare i possibili miglioramenti per i componenti client, oltre a rilevare i problemi relativi a browser o posizioni diverse.

1. Selezionare **Browser** in **Analisi** per aprire il riepilogo del browser.  Verrà fornito un riepilogo visivo delle varie telemetrie dell'applicazione dalla prospettiva del browser.

    ![Riepilogo del browser](media/app-insights-tutorial-performance/browser-summary.png)

2.  Scorrere verso il basso fino a **Quali sono le pagine in cui sono stati riscontrati maggiori rallentamenti?**,  dove è possibile visualizzare un elenco delle pagine nell'applicazione che hanno richiesto maggior tempo per il caricamento nei client.  Queste informazioni possono essere usate per classificare le pagine con l'impatto più significativo per l'utente.
3.  Fare clic su una delle pagine per aprire il pannello **Visualizzazione pagina**.  In questo esempio la pagina **/FabrikamProd** mostra una durata media eccessiva.  Il pannello **Visualizzazione pagina** fornisce informazioni dettagliate su questa pagina, inclusa una scomposizione dei diversi intervalli di durata.

    ![Visualizzazione pagina](media/app-insights-tutorial-performance/page-view.png)

4.  Fare clic sulla durata maggiore per esaminare i dettagli di queste richieste.  Quindi fare clic sulla singola richiesta per visualizzare i dettagli del client che richiede la pagina, inclusi il tipo di browser e il relativo percorso.  Queste informazioni possono risultare utili per determinare la presenza di problemi di prestazioni correlati a specifici tipi di client.

    ![Dettagli della richiesta](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Usare i dati di analisi per il client
Come per i dati raccolti per le prestazioni del server, Application Insights rende disponibili tutti i dati del client per un'analisi approfondita con Analytics.

1. Tornare al riepilogo del browser e fare clic sull'icona Analytics.

    ![Icona Analytics](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Verrà aperta la finestra di Analytics in Application Insights con una query per ognuna delle visualizzazioni nel pannello. La prima query mostra la durata per le diverse visualizzazioni pagina nel corso del tempo.

    ![Analytics](media/app-insights-tutorial-performance/client-analytics.png)

3.  Smart Diagnostics è una funzionalità di Analytics in Application Insights che identifica i modelli univoci nei dati.  Quando si fa clic sul punto di Smart Diagnostics nel grafico a linee, la stessa query viene eseguita senza i record che hanno causato l'anomalia.  I dettagli di questi record vengono visualizzati nella sezione dei commenti della query, in modo da identificare le proprietà di tali visualizzazioni pagina che causano la durata eccessiva.

    ![Smart Diagnostics](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come identificare i problemi di prestazioni, passare all'esercitazione successiva per imparare a creare avvisi in risposta agli errori.

> [!div class="nextstepaction"]
> [Avvisi sullo stato di integrità dell'applicazione](app-insights-tutorial-alert.md)
