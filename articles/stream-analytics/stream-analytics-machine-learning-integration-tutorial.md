---
title: Integrazione di Analisi di flusso di Azure con Azure Machine Learning
description: Questo articolo descrive come configurare rapidamente un semplice processo di analisi di flusso di Azure che integra Azure Machine Learning, usando una funzione definita dall'utente.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 26a1208131f1d9d3df7dccd8e27bda37992f043f
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236675"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Analisi dei sentimenti con analisi di flusso e Azure Machine Learning Studio di Azure (versione classica)

Questo articolo illustra come configurare un semplice processo di analisi di flusso di Azure che usa Azure Machine Learning Studio (classico) per l'analisi dei sentimenti. Si usa un modello di analisi dei sentimenti Machine Learning dal Cortana Intelligence Gallery per analizzare i dati di flusso di testo e determinare il punteggio del sentimento.

> [!TIP]
> Per migliorare le prestazioni e l'affidabilità, è consigliabile usare [funzioni definite dall'utente di Azure Machine Learning](machine-learning-udf.md) anziché le funzioni definite dall'utente di Azure Machine Learning Studio (versione classica).

È possibile applicare le informazioni apprese in questo articolo a scenari come i seguenti:

* Analisi in tempo reale del sentiment su flussi di dati di Twitter.
* Analisi dei record delle chat dei client con il personale di supporto.
* Valutazione dei commenti per forum, blog e video.
* Molti altri scenari di punteggio predittivo in tempo reale.

Il processo di Analisi di flusso creato applica il modello di analisi del sentiment come funzione definita dall'utente (UDF) ai dati del testo di esempio dell'archivio BLOB. L'output (il risultato dell'analisi del sentiment) viene scritto nello stesso archivio BLOB in un file CSV diverso. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

* Una sottoscrizione di Azure attiva.

* Un file CSV con alcuni dati di Twitter. È possibile scaricare un file di esempio da [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)oppure è possibile creare un file personalizzato. In uno scenario reale, si potrebbero ottenere i dati direttamente da un flusso di dati di Twitter.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Creare un contenitore di archiviazione e caricare il file di input CSV

In questo passaggio si carica un file CSV nel contenitore di archiviazione.

1. Nella portale di Azure selezionare **Crea una risorsa**  >  **archiviazione**  >  **account di archiviazione**.

2. Compilare la scheda *nozioni di base* con i dettagli seguenti e lasciare i valori predefiniti per i campi rimanenti:

   |Campo  |Valore  |
   |---------|---------|
   |Subscription|Scegliere la propria sottoscrizione.|
   |Resource group|Scegliere il gruppo di risorse.|
   |Nome dell'account di archiviazione|Immettere un nome per l'account di archiviazione. Il nome deve essere univoco in Azure.|
   |Location|Scegliere un paese. Tutte le risorse devono usare la stessa località.|
   |Tipo di account|BlobStorage|

   ![specificare i dettagli dell'account di archiviazione](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Selezionare **Rivedi e crea**. Selezionare quindi **Crea** per distribuire l'account di archiviazione.

4. Al termine della distribuzione, passare all'account di archiviazione. In **Servizio BLOB** selezionare **Contenitori**. Quindi selezionare **+ contenitore** per creare un nuovo contenitore.

   ![Creare un contenitore di archiviazione BLOB per l'input](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Specificare un nome per il contenitore e verificare che il **livello di accesso pubblico** sia impostato su **privato**. Al termine, selezionare **Crea**.

   ![specificare i dettagli del contenitore BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Passare al contenitore appena creato e selezionare **carica**. Caricare il file di **sampleinput.csv** scaricato in precedenza.

   ![Pulsante 'Carica' per un contenitore](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Aggiungere il modello di analisi dei sentimenti dalla raccolta Cortana Intelligence.

Ora che i dati di esempio sono in un BLOB, è possibile abilitare il modello di analisi del sentiment nella raccolta Cortana Intelligence.

1. Passare alla pagina del [modello di analisi predittiva del sentiment](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) nella raccolta Cortana Intelligence.  

2. Selezionare **Apri in studio (classico)**.  
   
   ![Analisi di flusso e Machine Learning, aprire Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Effettuare l'accesso per passare all'area di lavoro. Selezionare una località.

4. Selezionare **Run (Esegui** ) nella parte inferiore della pagina. Il processo viene eseguito e richiede circa un minuto.

   ![eseguire l'esperimento in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Al termine dell'esecuzione del processo, selezionare **Deploy Web Service** (Distribuisci servizio Web) nella parte inferiore della pagina.

   ![distribuire l'esperimento in Machine Learning Studio come servizio Web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Per verificare che il modello di analisi dei sentimenti sia pronto per l'uso, selezionare il pulsante **test** . Immettere testo, ad esempio "Mi piace Microsoft".

   ![testare l'esperimento in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Se il test funziona, viene visualizzato un risultato simile all'esempio seguente:

   ![risultati del test in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Nella colonna **app** selezionare il collegamento **cartella di lavoro Excel 2010 o versione precedente** per scaricare una cartella di lavoro di Excel. La cartella di lavoro contiene la chiave API e l'URL necessari in seguito per configurare il processo di Analisi di flusso.

    ![Analisi di flusso e Machine Learning, panoramica rapida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Creare un processo di Analisi di flusso che usi il modello di Machine Learning

È ora possibile creare un processo di Analisi di flusso che legge i tweet di esempio dal file CSV nell'archivio BLOB.

### <a name="create-the-job"></a>Creare il processo

Passare alla [portale di Azure](https://portal.azure.com) e creare un processo di analisi di flusso. Se non si ha familiarità con questo processo, vedere [creare un processo di analisi di flusso usando il portale di Azure](stream-analytics-quick-create-portal.md).

### <a name="configure-the-job-input"></a>Configurare l'input del processo

Il processo ottiene l'input dal file CSV caricato in precedenza nell'archivio BLOB.

1. Passare al processo di Analisi di flusso. In **topologia processo**selezionare l'opzione **input** . Selezionare **Aggiungi flusso**di  > **archiviazione BLOB**di input.

2. Compilare i dettagli di **archiviazione BLOB** con i valori seguenti:

   |Campo  |Valore  |
   |---------|---------|
   |Alias di input|Assegnare un nome all'input. Ricordare questo alias quando si scrive la query.|
   |Subscription|Selezionare la propria sottoscrizione.|
   |Account di archiviazione|Selezionare l'account di archiviazione creato nel passaggio precedente.|
   |Contenitore|Selezionare il contenitore creato nel passaggio precedente.|
   |Formato di serializzazione eventi|CSV|

3. Selezionare **Salva**.

### <a name="configure-the-job-output"></a>Configurare l'output del processo

Il processo invia i risultati allo stesso archivio BLOB da cui ottiene l'input.

1. Passare al processo di Analisi di flusso. In **topologia processo**selezionare l'opzione **output** . Selezionare **Aggiungi**  >  **Archivio BLOB**.

2. Compilare il modulo di **archiviazione BLOB** con questi valori:

   |Campo  |Valore  |
   |---------|---------|
   |Alias di input|Assegnare un nome all'input. Ricordare questo alias quando si scrive la query.|
   |Subscription|Selezionare la propria sottoscrizione.|
   |Account di archiviazione|Selezionare l'account di archiviazione creato nel passaggio precedente.|
   |Contenitore|Selezionare il contenitore creato nel passaggio precedente.|
   |Formato di serializzazione eventi|CSV|

3. Selezionare **Salva**.

### <a name="add-the-machine-learning-function"></a>Aggiungere la funzione di Machine Learning

In precedenza è stato pubblicato un modello di Machine Learning in un servizio Web. In questo scenario, quando si esegue il processo di Analisi di flusso, il processo invia ogni tweet di esempio dall'input al servizio Web per l'analisi del sentiment. Il servizio Web di Machine Learning restituisce un sentiment (`positive`, `neutral` o `negative`) e la probabilità che il tweet sia positivo.

In questa sezione si definisce una funzione nel processo di analisi dei flussi. La funzione può essere richiamata per inviare un tweet al servizio Web e ottenere la risposta.

1. Assicurarsi di avere a disposizione l'URL del servizio Web e la chiave API scaricati in precedenza nella cartella di lavoro di Excel.

2. Passare al processo di Analisi di flusso. Quindi selezionare **funzioni**  >  **+ Aggiungi**  >  **ml studio di Azure**

3. Compilare il form della **funzione Azure Machine Learning** con i valori seguenti:

   |Campo  |Valore  |
   |---------|---------|
   | Alias di funzione | Usare il nome `sentiment` e selezionare **specificare le impostazioni della funzione Azure Machine Learning manualmente**, che offre un'opzione per immettere l'URL e la chiave.      |
   | URL| Incollare l'URL del servizio Web.|
   |Chiave | Incollare la chiave API. |

4. Selezionare **Salva**.

### <a name="create-a-query-to-transform-the-data"></a>Creare una query per trasformare i dati

Analisi di flusso usa una query dichiarativa basata su SQL per esaminare l'input ed elaborarlo. In questa sezione si creerà una query che legge ogni tweet dall'input e quindi richiama la funzione di Machine Learning per eseguire l'analisi del sentiment. La query invia quindi il risultato all'output definito (archivio BLOB).

1. Tornare alla panoramica del processo di analisi di flusso.

2. In **Topologia processo** selezionare **Query**.

3. Immettere la query seguente:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   La query richiama la funzione creata in precedenza ( `sentiment` ) per eseguire l'analisi dei sentimenti su ogni tweet nell'input.

4. Selezionare **Save (Salva** ) per salvare la query.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Avviare il processo di Analisi di flusso e controllare l'output

È ora possibile avviare il processo di Analisi di flusso.

### <a name="start-the-job"></a>Avviare il processo

1. Tornare alla panoramica del processo di analisi di flusso.

2. Selezionare **inizia** nella parte superiore della pagina.

3. In **Avvia processo**selezionare **personalizzato**, quindi selezionare un giorno prima di quando è stato caricato il file CSV nell'archivio BLOB. Al termine, selezionare **Salva**.  

### <a name="check-the-output"></a>Controllare l'output

1. Consentire l'esecuzione per alcuni minuti fino a quando non vengono visualizzate attività nella casella **Monitoraggio**.

2. Se si dispone di uno strumento che in genere si utilizza per esaminare il contenuto dell'archiviazione BLOB, utilizzare tale strumento per esaminare il contenitore. In alternativa, seguire questa procedura nel portale di Azure:

      1. Nel portale di Azure trovare l'account di archiviazione e, all'interno dell'account, trovare il contenitore. Vengono visualizzati due file nel contenitore: il file che contiene i tweet di esempio e un file CSV generato dal processo di Analisi di flusso.
      2. Fare clic con il pulsante destro del mouse sul file generato e quindi scegliere **Scarica**.

3. Aprire il file CSV generato. Il contenuto visualizzato sarà simile al seguente:  

   ![Analisi di flusso e Machine Learning, visualizzazione CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Visualizzare le metriche

È possibile osservare anche le metriche correlate alla funzione Azure Machine Learning. Le metriche correlate alla funzione seguenti vengono visualizzate nella casella **monitoraggio** della panoramica del processo:

* **Richieste della funzione** indica il numero di richieste inviate al servizio Web di Machine Learning.  
* **Eventi della funzione** indica il numero di eventi nella richiesta. Per impostazione predefinita, ogni richiesta a un servizio Web di Machine Learning contiene fino a 1.000 eventi.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrare API REST e Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
