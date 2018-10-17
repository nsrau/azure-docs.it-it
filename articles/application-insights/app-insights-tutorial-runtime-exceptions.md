---
title: Diagnosticare le eccezioni di runtime con Azure Application Insights | Microsoft Docs
description: Esercitazione per il rilevamento e la diagnosi delle eccezioni di runtime in un'applicazione con Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: f45cb6a47756fae7b75d8c3df80a0bc754742063
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299005"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Rilevare e diagnosticare le eccezioni di runtime con Azure Application Insights

Azure Application Insights raccoglie i dati di telemetria dall'applicazione per consentire di identificare e diagnosticare le eccezioni di runtime.  Questa esercitazione illustra l'esecuzione di questo processo con l'applicazione in uso.  Si apprenderà come:

> [!div class="checklist"]
> * Modificare il progetto per abilitare il rilevamento delle eccezioni
> * Identificare le eccezioni per i diversi componenti dell'applicazione
> * Visualizzare i dettagli di un'eccezione
> * Scaricare uno snapshot dell'eccezione in Visual Studio per il debug
> * Analizzare i dettagli delle richieste non riuscite usando il linguaggio di query
> * Creare un nuovo elemento di lavoro per correggere il codice non corretto


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
    - Sviluppo Web e ASP.NET
    - Sviluppo di Azure
- Scaricare e installare il [debugger di snapshot di Visual Studio](http://aka.ms/snapshotdebugger).
- Abilitare il [debugger di snapshot di Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)
- Distribuire un'applicazione .NET in Azure e [abilitare Application Insights SDK](app-insights-asp-net.md). 
- L'esercitazione tiene traccia dell'identificazione di un'eccezione nell'applicazione, quindi modificare il codice nell'ambiente di sviluppo o di test per generare un'eccezione. 

## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analizzare gli errori
Application Insights raccoglie gli eventuali errori nell'applicazione e consente di visualizzarne la frequenza in diverse operazioni in modo da concentrare l'attenzione su quelli con il maggiore impatto.  È quindi possibile esaminare i dettagli di tali errori per identificarne la causa radice.   

1. Selezionare **Application Insights** e quindi la sottoscrizione in uso.  
2. Per aprire il pannello **Errori**, selezionare **Errori** nel menu **Analisi** oppure fare clic sul grafico **Richieste non riuscite**.

    ![Richieste non riuscite](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

3. Il pannello **Richieste non riuscite** mostra il conteggio delle richieste non riuscite e il numero di utenti interessati per ogni operazione per l'applicazione.  Ordinando queste informazioni in base all'utente è possibile identificare i problemi che influiscono maggiormente sugli utenti.  In questo esempio le operazioni **GET Employees/Create** e **GET Customers/Details** sono probabili candidati per l'analisi a causa del numero elevato di errori e degli utenti interessati.  Quando si seleziona un'operazione, nel riquadro di destra vengono mostrate ulteriori informazioni su di essa.

    ![Pannello Richieste non riuscite](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

4. Ridurre l'intervallo di tempo per ingrandire il periodo in cui la frequenza degli errori mostra un picco.

    ![Finestra Richieste non riuscite](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

5. Vedere i campioni correlati facendo clic sul pulsante con il numero di risultati filtrati. I campioni "consigliati" contengono i dati di telemetria correlati di tutti i componenti, anche se il campionamento potrebbe non essere stato attivo in alcun componente. Fare clic su un risultato della ricerca per visualizzare i dettagli dell'errore.

    ![Campioni di richieste non riuscite](media/app-insights-tutorial-runtime-exceptions/failed-requests-search.png)

6. I dettagli della richiesta non riuscita vengono illustrati nel diagramma di Gantt che mostra che si sono verificati due errori di dipendenza in questa transazione, attribuiti a oltre il 50% della durata totale della transazione. Questa esperienza presenta tutti i dati di telemetria, in tutti i componenti di un'applicazione distribuita correlati a questo ID operazione. [Altre informazioni sulla nuova esperienza](app-insights-transaction-diagnostics.md). È possibile selezionare uno degli elementi per visualizzarne i dettagli sul lato destro. 

    ![Dettagli richieste non riuscite](media/app-insights-tutorial-runtime-exceptions/failed-request-details.png)

7. I dettagli delle operazioni rivelano inoltre un'eccezione di tipo FormatException che sembra aver causato l'errore.  Si può notare che il problema è causato da un codice postale non valido. È possibile aprire lo snapshot di debug per visualizzare le informazioni di debug a livello di codice in Visual Studio.

    ![Dettagli eccezione](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>Identificare il codice responsabile dell'errore
Il debugger di snapshot raccoglie gli snapshot delle eccezioni più frequenti nell'applicazione per agevolare la diagnosi della causa radice nell'ambiente di produzione.  È possibile visualizzare gli snapshot di debug nel portale per vedere lo stack di chiamate e ispezionare le variabili in ogni stack frame di chiamate. È quindi possibile eseguire il debug del codice sorgente scaricando lo snapshot e aprendolo in Visual Studio 2017.

1. Nelle proprietà dell'eccezione fare clic su **Apri snapshot di debug**.
2. Verrà aperto il pannello **Snapshot di debug** con lo stack di chiamate per la richiesta.  Fare clic su uno dei metodi per visualizzare i valori di tutte le variabili locali al momento della richiesta.  A partire dal primo metodo in questo esempio, è possibile vedere le variabili locali che non hanno alcun valore.

    ![Snapshot di debug](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. La prima chiamata con valori validi è **ValidZipCode** e si può notare che è stato fornito un codice postale contenente lettere che non può essere convertito in un numero intero.  Si tratta dell'errore nel codice che deve essere corretto.

    ![Snapshot di debug](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Per scaricare questo snapshot in Visual Studio dove è possibile individuare il codice effettivo che deve essere corretto, fare clic su **Download Snapshot** (Scarica snapshot).
6. Lo snapshot viene caricato in Visual Studio.
7. È ora possibile eseguire una sessione di debug in Visual Studio per identificare rapidamente la riga di codice che ha causato l'eccezione.

    ![Eccezione nel codice](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Usare i dati di analisi
Tutti i dati raccolti da Application Insights sono archiviati in Log Analytics di Azure, che fornisce un linguaggio di query avanzato per analizzare i dati in diversi modi.  È possibile usare questi dati per analizzare le richieste che hanno generato l'eccezione che si sta esaminando. 

8. Fare clic sulle informazioni di CodeLens sopra il codice per visualizzare i dati di telemetria forniti da Application Insights.

    ![Codice](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Fare clic su **Analizza l'impatto** per aprire Analytics in Application Insights,  che viene popolato con diverse query che forniscono informazioni dettagliate sulle richieste non riuscite, ad esempio gli utenti, le aree e i browser interessati.<br><br>![Analisi](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Aggiungere un elemento di lavoro
Se si connette Application Insights a un sistema di verifica come Azure DevOps o GitHub, è possibile creare un elemento di lavoro direttamente da Application Insights.

1. Tornare al pannello **Proprietà eccezione** in Application Insights.
2. Fare clic su **Nuovo elemento di lavoro**.
3. Viene aperto il pannello **Nuovo elemento di lavoro** con i dettagli sull'eccezione già popolati.  È possibile aggiungere eventuali informazioni aggiuntive prima di salvarlo.

    ![Nuovo elemento di lavoro](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come identificare le eccezioni di runtime, passare all'esercitazione successiva per imparare a identificare e diagnosticare i problemi di prestazioni.

> [!div class="nextstepaction"]
> [Identificare i problemi di prestazioni](app-insights-tutorial-performance.md)