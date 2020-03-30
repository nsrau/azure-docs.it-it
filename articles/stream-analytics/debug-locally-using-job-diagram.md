---
title: Debug Azure Stream Analytics queries locally using job diagram in Visual Studio
description: Questo articolo descrive come eseguire il debug delle query in locale usando il diagramma dei processi in Strumenti di Analisi di flusso di Azure per Visual Studio.This article describes how to debug queries locally using job diagram in Azure Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847199"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Debug Azure Stream Analytics queries locally using job diagram in Visual Studio

Processi che restituiscono nessun risultato o risultati imprevisti sono scenari comuni di risoluzione dei problemi per le query di streaming. È possibile usare il diagramma dei processi durante il test della query in locale in Visual Studio per esaminare il set di risultati intermedi o le metriche per ogni passaggio. I diagrammi di lavoro consentono di isolare rapidamente l'origine di un problema durante la risoluzione dei problemi.

## <a name="debug-a-query-using-job-diagram"></a>Eseguire il debug di una query utilizzando il diagramma dei processiDebug a query using job diagram

Uno script di Analisi di flusso di Azure viene usato per trasformare i dati di input in dati di output. Il diagramma dei processi mostra come i dati passano dalle origini di input (Hub eventi, hub IoT e così via) tramite più passaggi di query e, infine, ai sink di output. Ogni passaggio di query viene mappato a un `WITH` set di risultati temporaneo definito nello script tramite un'istruzione. È possibile visualizzare i dati e le metriche di ogni passaggio della query in ogni set di risultati intermedio per trovare l'origine di un problema.

> [!NOTE]
> Questo diagramma dei processi mostra solo i dati e le metriche per i test locali in un singolo nodo. Non deve essere utilizzato per l'ottimizzazione delle prestazioni e la risoluzione dei problemi.

### <a name="start-local-testing"></a>Avviare il test locale

Utilizzare questa [guida introduttiva](stream-analytics-quick-create-vs.md) per informazioni su come creare un processo di Analisi di flusso utilizzando Visual Studio o [esportare un processo esistente in un progetto locale.](stream-analytics-vs-tools.md#export-jobs-to-a-project) Se si desidera testare la query con dati di input locali, seguire queste [istruzioni](stream-analytics-live-data-local-testing.md). Se si desidera eseguire il test con l'input dinamico, passare al passaggio successivo.

> [!NOTE]
> Se si esporta un processo in un progetto locale e si desidera eseguire il test rispetto a un flusso di input live, è necessario specificare nuovamente le credenziali per tutti gli input.  

Scegliere l'origine di input e output dall'editor di script e selezionare **Esegui localmente**. Il diagramma del lavoro viene visualizzato sul lato destro.

### <a name="view-the-intermediate-result-set"></a>Visualizzare il set di risultati intermedioView the intermediate result set  

1. Selezionare il passaggio della query per passare allo script. Si viene automaticamente indirizzati allo script corrispondente nell'editor a sinistra.

   ![Diagramma di processo navigare script](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Selezionare il passaggio della query e selezionare **Anteprima** nella finestra di dialogo visualizzata. Il set di risultati viene visualizzato in una scheda nella finestra dei risultati inferiore.

   ![Risultato dell'anteprima del diagramma di processo](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Visualizzare le metriche dei passaggi

In questa sezione vengono esaminate le metriche disponibili per ogni parte del diagramma.

#### <a name="input-sources-live-stream"></a>Origini di input (Live stream)

![Origini di input live del diagramma di lavoro](./media/debug-locally-using-job-diagram/live-input.png)

|Metrica|Descrizione|
|-|-|
|**TaxiRide**| Nome dell'input.|
|**Hub eventi** | Tipo di origine di input.|
|**Events**|Numero di eventi letti.|
|**Origini eventi backlogged**|Quanti altri messaggi devono essere letti per gli hub di eventi e gli input dell'hub IoT.How many more messages need to be read for Event Hubs and IoT Hub inputs.|
|**Eventi in byte**|Numero di byte letti.|
| **Eventi degradati**|Numero di eventi che hanno avuto un problema diverso dalla deserializzazione.|
|**Eventi iniziali**| Numero di eventi con un timestamp dell'applicazione prima della filigrana alta.|
|**Eventi tardivi**| Il numero di eventi che hanno un timestamp dell'applicazione dopo il limite massimo.|
|**Origini eventi**| Numero di unità di dati lette. Ad esempio il numero di BLOB.|

#### <a name="input-sources-local-input"></a>Origini di ingresso (ingresso locale)

![Origini di input locali del diagramma processi](./media/debug-locally-using-job-diagram/local-input.png)

|Metrica|Descrizione|
|-|-|
|**TaxiRide**| Nome dell'input.|
|**Conteggio righe**| Numero di righe generate dal passaggio.|
|**Dimensioni dei dati**| Dimensioni dei dati generati da questo passaggio.|
|**Ingresso locale**| Utilizzare i dati locali come input.|

#### <a name="query-steps"></a>Passi di query

![Passaggio della query del diagramma processi](./media/debug-locally-using-job-diagram/query-step.png)

|Metrica|Descrizione|
|-|-|
|**Dati di viaggio**|Nome del set di risultati temporaneo.|
|**Conteggio righe**| Numero di righe generate dal passaggio.|
|**Dimensioni dei dati**| Dimensioni dei dati generati da questo passaggio.|
  
#### <a name="output-sinks-live-output"></a>Sink di uscita (output live)

![Sink di output locali del diagramma dei processi](./media/debug-locally-using-job-diagram/live-output.png)

|Metrica|Descrizione|
|-|-|
|**regioneggEH**|Nome dell'output.|
|**Events**|Numero di eventi restituiti nei sink.|

#### <a name="output-sinks-local-output"></a>Sink di uscita (output locale)

![Sink di output locali del diagramma dei processi](./media/debug-locally-using-job-diagram/local-output.png)

|Metrica|Descrizione|
|-|-|
|**regioneggEH**|Nome dell'output.|
|**Uscita locale**| Risultato dell'output in un file locale.|
|**Conteggio righe**| Numero di righe di output nel file locale.|
|**Dimensioni dei dati**| Dimensioni dell'output dei dati nel file locale.|

### <a name="close-job-diagram"></a>Diagramma di chiusura dei processi

Se il diagramma del processo non è più necessario, selezionare **Chiudi** nell'angolo superiore destro. Dopo aver chiuso la finestra del diagramma, è necessario avviare di nuovo il test locale per visualizzarlo.

### <a name="view-job-level-metrics-and-stop-running"></a>Visualizzare le metriche a livello di processo e interrompere l'esecuzione

Altre metriche a livello di processo vengono visualizzate nella console popup. Se si desidera interrompere il lavoro, premete **Ctrl-C** nella console.

![Processo diagramma di arresto processo](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Limitazioni

* I sink di output di Power BI e Azure Data Lake Storage Gen1 non sono supportati a causa delle limitazioni del modello di autenticazione.

* Solo le opzioni di input cloud hanno il supporto dei [criteri temporali](stream-analytics-out-of-order-and-late-events.md), a differenza delle opzioni di input locale.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Creare un processo di Analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md)
* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)
* [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio (anteprima)](stream-analytics-live-data-local-testing.md)
