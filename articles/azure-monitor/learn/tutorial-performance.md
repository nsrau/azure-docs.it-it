---
title: Diagnosticare i problemi di prestazioni con Azure Application Insights | Microsoft Docs
description: Esercitazione per il rilevamento e la diagnosi dei problemi di prestazioni in un'applicazione con Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/13/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 9768191d98bf1987ac24564869107cdd6bf19e8d
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69032162"
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

- Installare [Visual Studio 2019](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
    - Sviluppo Web e ASP.NET
    - Sviluppo di Azure
- Distribuire un'applicazione .NET in Azure e [abilitare Application Insights SDK](../../azure-monitor/app/asp-net.md).
- [Abilitare Application Insights Profiler](../../azure-monitor/app/profiler.md#installation) per l'applicazione.

## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identificare le operazioni del server con maggiori rallentamenti
Application Insights raccoglie i dettagli sulle prestazioni per le diverse operazioni nell'applicazione. Identificando le operazioni con la durata maggiore, è possibile diagnosticare potenziali problemi o supportare meglio le attività di sviluppo continuative per migliorare le prestazioni complessive dell'applicazione.

1. Selezionare **Application Insights** e quindi selezionare la sottoscrizione in uso.  
1. Per aprire il pannello **Prestazioni**, selezionare **Prestazioni** nel menu **Analisi** oppure fare clic sul grafico **Tempo di risposta del server**.

    ![Prestazioni](media/tutorial-performance/1-overview.png)

2. Il pannello **Prestazioni** mostra il conteggio e la durata media di ogni operazione per l'applicazione.  È possibile usare queste informazioni per identificare le operazioni che influiscono maggiormente sugli utenti. In questo esempio le operazioni **GET Customers/Details** e **GET Home/Index** sono probabili candidati per l'analisi a causa della durata e del numero di chiamate relativamente elevati.  Altre operazioni potrebbero avere una durata maggiore ma sono state chiamate raramente, pertanto l'effetto del loro miglioramento sarebbe minimo.  

    ![Pannello server prestazioni](media/tutorial-performance/2-server-operations.png)

3. Il grafico mostra attualmente la durata media delle operazioni selezionate nel tempo. È possibile passare al 95° percentile per trovare i problemi di prestazioni. Aggiungere le operazioni a cui si è interessati aggiungendole al grafico.  Quest'ultimo indica la presenza di alcuni picchi che sarebbe utile analizzare.  È possibile isolarli ulteriormente riducendo l'intervallo di tempo del grafico.

    ![Aggiungere operazioni](media/tutorial-performance/3-server-operations-95th.png)

4.  Il pannello delle prestazioni a destra illustra la distribuzione delle durate per le diverse richieste per l'operazione selezionata.  Ridurre l'intervallo di tempo per l'avvio al 95° percentile circa. La scheda con le informazioni dettagliate "3 dipendenze più frequenti" consente di verificare immediatamente che le dipendenze esterne stanno contribuendo al rallentamento delle transazioni.  Fare clic sul pulsante con il numero di campioni per visualizzare un elenco di campioni. È quindi possibile selezionare qualsiasi campione per visualizzare i dettagli della transazione.

5.  È possibile verificare immediatamente che la chiamata alla tabella di Azure Fabrikamaccount sta contribuendo più di tutte alla durata totale della transazione. Noterete anche che un'eccezione ne ha causato l'esito negativo. È possibile fare clic su qualsiasi elemento nell'elenco per visualizzarne i dettagli sul lato destro. [Altre informazioni sull'esperienza di diagnostica delle transazioni](../../azure-monitor/app/transaction-diagnostics.md)

    ![Dettagli dell'operazione end-to-end](media/tutorial-performance/4-end-to-end.png)
    

6.  Il **Profiler** consente di analizzare più a fondo il problema con la diagnostica a livello di codice mostrando il codice effettivo che è stato eseguito per l'operazione e il tempo richiesto per ogni passaggio. Alcune operazioni potrebbero non avere una traccia dal momento che il profiler viene eseguito periodicamente.  Nel corso del tempo dovrebbero essere disponibili tracce per più operazioni.  Per avviare il profiler per l'operazione, fare clic su **Tracce Profiler**.
5.  La traccia mostra i singoli eventi per ogni operazione, in modo da poter diagnosticare la causa radice per la durata dell'operazione complessiva.  Fare clic su uno dei primi esempi, che hanno una durata maggiore.
6.  Fare clic su **Percorso critico** per evidenziare il percorso specifico degli eventi che contribuiscono maggiormente alla durata totale dell'operazione.  In questo esempio è possibile vedere che la chiamata più lenta proviene dal metodo *FabrikamFiberAzureStorage.GetStorageTableData*. La parte che richiede più tempo è il metodo *CloudTable.CreateIfNotExist*. Se questa riga di codice viene eseguita ogni volta che viene chiamata la funzione, verranno usate chiamate di rete e risorse della CPU non necessarie. Il modo migliore per correggere il codice è inserire la linea in un metodo di avvio che si esegue solo una volta.

    ![Dettagli del profiler](media/tutorial-performance/5-hot-path.png)

7.  Il **Suggerimento per le prestazioni** nella parte superiore della schermata supporta la valutazione che la durata eccessiva è dovuta all'attesa.  Fare clic sul collegamento **in attesa** per la documentazione sull'interpretazione dei diversi tipi di eventi.

    ![Suggerimento per le prestazioni](media/tutorial-performance/6-perf-tip.png)

8.  Per un'ulteriore analisi, è possibile fare clic su **Scarica la traccia** per scaricare la traccia in Visual Studio.

## <a name="use-logs-data-for-server"></a>Usare i dati dei log per il server
 Log fornisce un linguaggio di query avanzato che consente di analizzare tutti i dati raccolti da Application Insights. È possibile usare questo strumento per eseguire analisi approfondite sui dati delle richieste e delle prestazioni.

1. Tornare al pannello con i dettagli dell'operazione e fare clic sull'![icona Log](media/tutorial-performance/app-viewinlogs-icon.png)**Visualizza in Log (Analisi)**

2. Verrà aperta la finestra di Log con una query per ognuna delle visualizzazioni nel pannello.  È possibile eseguire queste query così come sono oppure modificarle in base alle proprie esigenze.  La prima query mostra la durata per questa operazione nel corso del tempo.

    ![Query sui log](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Identificare le operazioni del client con maggiori rallentamenti
Oltre a identificare i processi server da ottimizzare, Application Insights è in grado di analizzare il punto di vista dei browser client.  Ciò consente di identificare i possibili miglioramenti per i componenti client, oltre a rilevare i problemi relativi a browser o posizioni diverse.

1. Selezionare **Browser** in **Analisi**, quindi fare clic su **Prestazioni del browser** o selezionare **Prestazioni** in **Analisi** e passare alla scheda **Browser** facendo clic sul pulsante di attivazione/disattivazione server/browser in alto a destra per aprire il riepilogo delle prestazioni del browser. Verrà fornito un riepilogo visivo delle varie telemetrie dell'applicazione dalla prospettiva del browser.

    ![Riepilogo del browser](media/tutorial-performance/8-browser.png)

2. Selezionare uno dei nomi delle operazioni, quindi fare clic sul pulsante esempi blu in basso a destra e selezionare un'operazione. In questo modo vengono visualizzati i dettagli della transazione end-to-end e sul lato destro è possibile visualizzare le **Proprietà di Visualizzazione pagina**. Questo consente di visualizzare i dettagli del client che richiede la pagina, inclusi il tipo di browser e il relativo percorso. Queste informazioni possono risultare utili per determinare la presenza di problemi di prestazioni correlati a specifici tipi di client.

    ![Visualizzazione pagina](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Usare i dati dei log per il client
Come per i dati raccolti per le prestazioni del server, Application Insights rende disponibili tutti i dati del client per un'analisi approfondita con Log.

1. Tornare al riepilogo del browser e fare clic sull'![icona Log](media/tutorial-performance/app-viewinlogs-icon.png) **Visualizza in Log (Analisi)**

2. Verrà aperta la finestra di Log con una query per ognuna delle visualizzazioni nel pannello. La prima query mostra la durata per le diverse visualizzazioni pagina nel corso del tempo.

    ![Query sui log](media/tutorial-performance/10-page-view-logs.png)

3.  Smart Diagnostics è una funzionalità di Log che identifica i modelli univoci nei dati. Quando si fa clic sul punto di Smart Diagnostics nel grafico a linee, la stessa query viene eseguita senza i record che hanno causato l'anomalia. I dettagli di questi record vengono visualizzati nella sezione dei commenti della query, in modo da identificare le proprietà di tali visualizzazioni pagina che causano la durata eccessiva.

    ![Log con Diagnostica intelligenti](media/tutorial-performance/11-page-view-logs-dsmart.png)


## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come identificare i problemi di prestazioni, passare all'esercitazione successiva per imparare a creare avvisi in risposta agli errori.

> [!div class="nextstepaction"]
> [Avvisi sullo stato di integrità dell'applicazione](../../azure-monitor/learn/tutorial-alert.md)
