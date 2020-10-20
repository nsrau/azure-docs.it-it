---
title: Eseguire il debug di query di analisi di flusso di Azure localmente usando il diagramma processi in Visual Studio
description: Questo articolo descrive come eseguire il debug di query localmente usando il diagramma dei processi negli strumenti di analisi di flusso di Azure per Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: e9d6bbdf325c0da96b81c1f2146da284ea744def
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218256"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Eseguire il debug di query di analisi di flusso di Azure localmente usando il diagramma processi in Visual Studio

I processi che restituiscono nessun risultato o risultati imprevisti sono scenari comuni per la risoluzione dei problemi per le query di streaming. È possibile usare il diagramma dei processi durante il test della query localmente in Visual Studio per esaminare il set di risultati intermedi e le metriche per ogni passaggio. I diagrammi di processo consentono di isolare rapidamente l'origine di un problema durante la risoluzione dei problemi.

## <a name="debug-a-query-using-job-diagram"></a>Eseguire il debug di una query usando il diagramma del processo

Uno script di analisi di flusso di Azure viene usato per trasformare i dati di input in dati di output. Il diagramma dei processi Mostra come il flusso di dati dalle origini di input (hub eventi, hub Internet e così via) attraverso più passaggi di query e, infine, i sink di output. Ogni passaggio della query viene mappato a un set di risultati temporaneo definito nello script mediante un' `WITH` istruzione. Per individuare l'origine di un problema, è possibile visualizzare i dati e le metriche di ogni passaggio della query in ogni set di risultati intermedi.

> [!NOTE]
> Questo diagramma di processo Mostra solo i dati e le metriche per i test locali in un singolo nodo. Non deve essere utilizzato per l'ottimizzazione delle prestazioni e la risoluzione dei problemi.

### <a name="start-local-testing"></a>Avvia test locali

Usare questa [Guida introduttiva](stream-analytics-quick-create-vs.md) per informazioni su come creare un processo di analisi di flusso usando Visual Studio o [esportare un processo esistente in un progetto locale](stream-analytics-vs-tools.md#export-jobs-to-a-project). Se si desidera testare la query con i dati di input locali, attenersi a queste [istruzioni](stream-analytics-live-data-local-testing.md). Se si vuole eseguire il test con l'input Live, procedere al passaggio successivo.

> [!NOTE]
> Se si esporta un processo in un progetto locale e si vuole eseguire il test in un flusso di input Live, è necessario specificare di nuovo le credenziali per tutti gli input.  

Scegliere l'origine di input e di output nell'editor di script e selezionare **Esegui localmente**. Il diagramma del processo viene visualizzato sul lato destro.

### <a name="view-the-intermediate-result-set"></a>Visualizzare il set di risultati intermedio  

1. Selezionare il passaggio della query per passare allo script. Si viene indirizzati automaticamente allo script corrispondente nell'editor a sinistra.

   ![Script di esplorazione Diagramma processi](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Selezionare il passaggio della query e selezionare **Anteprima** nella finestra di dialogo visualizzata. Il set di risultati viene visualizzato in una scheda della finestra dei risultati inferiore.

   ![Anteprima dei risultati nel diagramma di processo](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Visualizzare le metriche del passaggio

In questa sezione vengono esaminate le metriche disponibili per ogni parte del diagramma.

#### <a name="input-sources-live-stream"></a>Origini di input (flusso live)

![Origini input Live del diagramma processi](./media/debug-locally-using-job-diagram/live-input.png)

|Metrica|Descrizione|
|-|-|
|**TaxiRide**| Nome dell'input.|
|**Hub eventi** | Tipo di origine di input.|
|**Eventi**|Il numero di eventi letti.|
|**Origini eventi backlog**|Quanti altri messaggi devono essere letti per gli input di hub eventi e dell'hub Internet.|
|**Eventi in byte**|Numero di byte letti.|
| **Eventi declassati**|Il numero di eventi con un problema diverso da quello della deserializzazione.|
|**Eventi iniziali**| Il numero di eventi che hanno un timestamp dell'applicazione prima del limite massimo.|
|**Eventi tardivi**| Il numero di eventi che hanno un timestamp dell'applicazione dopo il limite massimo.|
|**Origini eventi**| Numero di unità di dati lette. Ad esempio il numero di BLOB.|

#### <a name="input-sources-local-input"></a>Origini di input (input locale)

![Origini di input locali del diagramma processi](./media/debug-locally-using-job-diagram/local-input.png)

|Metrica|Descrizione|
|-|-|
|**TaxiRide**| Nome dell'input.|
|**Conteggio righe**| Numero di righe generate dal passaggio.|
|**Dimensioni dei dati**| Dimensioni dei dati generati da questo passaggio.|
|**Input locale**| Usare i dati locali come input.|

#### <a name="query-steps"></a>Passi di query

![Passaggio della query del diagramma di processo](./media/debug-locally-using-job-diagram/query-step.png)

|Metrica|Descrizione|
|-|-|
|**TripData**|Nome del set di risultati temporaneo.|
|**Conteggio righe**| Numero di righe generate dal passaggio.|
|**Dimensioni dei dati**| Dimensioni dei dati generati da questo passaggio.|
  
#### <a name="output-sinks-live-output"></a>Sink di output (output Live)

![Diagramma di processo che mostra i sink di output locali.](./media/debug-locally-using-job-diagram/live-output.png)

|Metrica|Descrizione|
|-|-|
|**regionaggEH**|Nome dell'output.|
|**Eventi**|Il numero di eventi restituiti a sink.|

#### <a name="output-sinks-local-output"></a>Sink di output (output locale)

![Sink di output locale del diagramma processi](./media/debug-locally-using-job-diagram/local-output.png)

|Metrica|Descrizione|
|-|-|
|**regionaggEH**|Nome dell'output.|
|**Output locale**| Risultato dell'output in un file locale.|
|**Conteggio righe**| Il numero di righe restituite al file locale.|
|**Dimensioni dei dati**| Dimensioni dell'output dei dati nel file locale.|

### <a name="close-job-diagram"></a>Chiudi Diagramma processi

Se non è più necessario il diagramma del processo, selezionare **Chiudi** nell'angolo superiore destro. Dopo aver chiuso la finestra del diagramma, è necessario avviare di nuovo il testing locale per visualizzarlo.

### <a name="view-job-level-metrics-and-stop-running"></a>Visualizzare le metriche a livello di processo e arrestare l'esecuzione

Altre metriche a livello di processo vengono visualizzate nella console popup. Se si desidera arrestare il processo, premere **CTRL + C** nella console.

![Processo di arresto Diagramma processi](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Limitazioni

* I sink di output Power BI e Azure Data Lake Storage Gen1 non sono supportati a causa delle limitazioni del modello di autenticazione.

* Solo le opzioni di input cloud hanno il supporto dei [criteri temporali](stream-analytics-out-of-order-and-late-events.md), a differenza delle opzioni di input locale.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Creare un processo di Analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md)
* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)
* [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio (anteprima)](stream-analytics-live-data-local-testing.md)
