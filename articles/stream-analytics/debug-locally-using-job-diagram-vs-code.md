---
title: Eseguire il debug di query di analisi di flusso di Azure in locale usando il diagramma Visual Studio Code del processo
description: Questo articolo descrive come eseguire il debug di query localmente usando il diagramma dei processi nell'estensione analisi di flusso di Azure per Visual Studio Code.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: f8885229c8ab6784c6c0bf45487812fcc43f9b22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903829"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Eseguire il debug di query di analisi di flusso di Azure in locale usando il diagramma Visual Studio Code del processo

I processi di streaming che restituiscono nessun risultato o risultati imprevisti richiedono spesso la risoluzione dei problemi. L'estensione Visual Studio Code per analisi di flusso di Azure integra diagrammi di processo, metriche, log di diagnostica e risultati intermedi che consentono di isolare rapidamente l'origine di un problema. È possibile utilizzare il diagramma dei processi durante il test della query localmente per esaminare il set di risultati intermedi e le metriche per ogni passaggio.

## <a name="debug-a-query-using-job-diagram"></a>Eseguire il debug di una query usando il diagramma del processo

Uno script di analisi di flusso di Azure viene usato per trasformare i dati di input in dati di output. Il diagramma dei processi Mostra come il flusso di dati dalle origini di input, ad esempio hub eventi o hub eventi, tramite più passaggi di query ai sink di output. Ogni passaggio della query viene mappato a un set di risultati temporaneo definito nello script mediante un' `WITH` istruzione. Per individuare l'origine di un problema, è possibile visualizzare i dati e le metriche di ogni passaggio della query in ogni set di risultati intermedi.

> [!NOTE]
> Questo diagramma di processo Mostra solo i dati e le metriche per i test locali in un singolo nodo. Non deve essere utilizzato per l'ottimizzazione delle prestazioni e la risoluzione dei problemi.

### <a name="start-local-testing"></a>Avvia test locali

Usare questa [Guida introduttiva](quick-create-visual-studio-code.md) per informazioni su come creare un processo di analisi di flusso usando Visual Studio Code o [esportare un processo esistente in un progetto locale](visual-studio-code-explore-jobs.md). Le credenziali per gli input e gli output vengono popolate automaticamente per i processi esportati.

Se si desidera testare la query con i dati di input locali, attenersi a queste [istruzioni](visual-studio-code-local-run.md). Se si vuole eseguire il test con l'input Live, configurare lo spostamento dell' [input](stream-analytics-add-inputs.md) al passaggio successivo. 

Aprire il file di script * \. asaql* e selezionare **Esegui localmente**. Selezionare quindi **Usa input locale** o **Usa input Live**. Il diagramma del processo viene visualizzato sul lato destro della finestra.

### <a name="view-the-output-and-intermediate-result-set"></a>Visualizzare l'output e il set di risultati intermedi  

1. Tutti gli output del processo vengono visualizzati nella finestra dei risultati nella parte inferiore destra della finestra di Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Risultati dell'output del processo](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. Selezionare il passaggio della query per passare allo script. Si viene indirizzati automaticamente allo script corrispondente nell'editor a sinistra. Il risultato intermedio viene visualizzato nella finestra dei risultati nella parte inferiore destra della finestra di Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Anteprima dei risultati nel diagramma di processo](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Visualizzare le metriche

In questa sezione vengono esaminate le metriche disponibili per ogni parte del diagramma.

1. Selezionare la scheda **metriche** accanto alla scheda **risultato** nel lato inferiore destro della finestra di Visual Studio Code.

2. Selezionare **processo** nell'elenco a discesa. È possibile selezionare qualsiasi spazio vuoto in un nodo grafico per passare alle metriche a livello di processo. Questa vista contiene tutte le metriche, che vengono aggiornate ogni 10 secondi quando il processo è in esecuzione. È possibile selezionare o deselezionare le metriche sul lato destro per visualizzarle nei grafici.

   > [!div class="mx-imgBorder"]
   > ![Metriche del diagramma di processo](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. Selezionare il nome dell'origine dati di input dall'elenco a discesa per visualizzare le metriche di input. L'origine di input nello screenshot seguente è denominata *virgolette*. Per altre informazioni sulle metriche di input, vedere informazioni sul [monitoraggio dei processi di analisi di flusso e su come monitorare le query](stream-analytics-monitoring.md).

   > [!div class="mx-imgBorder"]
   > ![Metriche di input del diagramma processi](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Selezionare un passaggio della query dal diagramma del processo oppure selezionare il nome del passaggio nell'elenco a discesa per visualizzare le metriche del livello del passaggio. Il ritardo della filigrana è l'unica metrica del passaggio disponibile.

   > [!div class="mx-imgBorder"]
   > ![Metrica passaggio](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. Selezionare un output nel diagramma o nell'elenco a discesa per visualizzare le metriche correlate all'output. Per altre informazioni sulle metriche di output, vedere informazioni sul [monitoraggio dei processi di analisi di flusso e su come monitorare le query](stream-analytics-monitoring.md). I sink di output Live non sono supportati.

   > [!div class="mx-imgBorder"]
   > ![Metriche di output](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Visualizzare i log di diagnostica

I log di diagnostica a livello di processo contengono informazioni di diagnostica per le origini dati di input e i sink di output. Quando si seleziona un nodo di input o un nodo di output, vengono visualizzati solo i log corrispondenti. Non viene visualizzato alcun log se si seleziona un passaggio della query. È possibile trovare tutti i log a livello di processo ed è possibile filtrare i log in base a gravità e tempo.

   > [!div class="mx-imgBorder"]
   > ![Log di diagnostica](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Selezionare una voce di log per visualizzare l'intero messaggio.

   > [!div class="mx-imgBorder"]
   > ![Messaggio log di diagnostica](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Altre funzionalità del diagramma di processo

È possibile selezionare **Arresta** o **Sospendi** dalla barra degli strumenti in base alle esigenze. Una volta che il processo è stato sospeso, è possibile riprenderlo dall'ultimo output.

> [!div class="mx-imgBorder"]
> ![Arresta o Sospendi processo](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

Selezionare **Riepilogo processi** nella parte superiore destra del diagramma del processo per visualizzare le proprietà e le configurazioni del processo locale.

> [!div class="mx-imgBorder"]
> ![Riepilogo processi locali](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Limitazioni

* I sink di output Live non sono supportati nell'esecuzione locale.

* Esegui processo localmente con la funzione JavaScript è supportato solo nel sistema operativo Windows.

* Il codice personalizzato C# e le funzioni Azure Machine Learning non sono supportate. 

* Solo le opzioni di input cloud supportano i [criteri temporali](stream-analytics-out-of-order-and-late-events.md) , mentre le opzioni di input locali non lo sono.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: creare un processo di analisi di flusso usando Visual Studio Code](quick-create-visual-studio-code.md)
* [Esplora analisi di flusso di Azure con Visual Studio Code](visual-studio-code-explore-jobs.md)
* [Eseguire test locali delle query di Analisi di flusso con dati di esempio con Visual Studio Code](visual-studio-code-local-run.md)
* [Testare i processi di analisi di flusso di Azure localmente con input Live con Visual Studio Code](visual-studio-code-local-run-live-input.md)
