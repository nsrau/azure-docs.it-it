---
title: Integrazione di Analisi di flusso di Azure e Machine Learning | Documentazione Microsoft
description: Come usare una funzione definita dall&quot;utente e Machine Learning in un processo di analisi di flusso
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/14/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d1ffb9aba0eb1e17d1efd913f536f6f3997fccbb
ms.openlocfilehash: 7b635b1810536f5b3eb1371d687e9c355e604e41
ms.lasthandoff: 02/14/2017

---

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Analisi dei sentimenti con Analisi di flusso di Azure e Azure Machine Learning
Questo articolo è realizzato per consentire di configurare rapidamente l'integrazione di un semplice processo di Analisi di flusso con Machine Learning. Utilizzando un modello di Machine Learning per l'analisi dei sentimenti proveniente dalla raccolta Cortana Intelligence, verrà analizzato il flusso di dati di testo e determinato il punteggio dei sentimenti in tempo reale. Le informazioni di questo articolo illustrano scenari come l'analisi dei sentimenti in tempo reale in un flusso di dati di Twitter, l'analisi dei record delle chat dei clienti con il personale del supporto, la valutazione dei commenti in forum/blog/video e diversi altri scenari di determinazione di punteggi predittivi in tempo reale.

Questo articolo offre un file CSV di esempio con input di testo nell'archivio BLOB di Azure, come da figura seguente. Il processo applica il modello di analisi dei sentimenti come funzione definita dall'utente ai dati del testo di esempio dell'archivio BLOB. Il risultato finale viene inserito nello stesso archivio BLOB di Azure in un altro file CSV. 

![Analisi di flusso e Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

L'immagine seguente illustra questa configurazione. Per uno scenario più realistico, è possibile sostituire l'archivio BLOB con il flusso di dati di Twitter provenienti da un input di Hub eventi di Azure. È anche possibile compilare una visualizzazione in tempo reale del sentimento di aggregazione in [Microsoft Power BI](https://powerbi.microsoft.com/) .    

![Analisi di flusso e Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Prerequisiti
I prerequisiti per completare le attività illustrate in questo articolo sono:

* Una sottoscrizione di Azure attiva.
* Un file CSV contenente alcuni dati. È possibile scaricare il file mostrato nella Figura 1 da [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv)oppure creare un file personalizzato. Per questo articolo si presuppone l'utilizzo del file da scaricare su GitHub.

In generale, per completare le attività illustrate in questo articolo, sarà necessario eseguire le operazioni seguenti:

1. Caricare il file di input CSV in Archivio BLOB di Azure.
2. Aggiungere un modello di analisi dei sentimenti dalla raccolta Cortana Intelligence all'area di lavoro di Azure Machine Learning.
3. Distribuire questo modello come servizio Web nell'area di lavoro di Machine Learning.
4. Creare un processo di Analisi di flusso che chiami questo servizio Web come funzione per determinare il sentimento per l'input di testo.
5. Avviare il processo di Analisi di flusso ed esaminare l'output.

## <a name="create-a-storage-blob-and-upload-the-csv-input-file"></a>Creare un BLOB di archiviazione e caricare il file di input CSV
Per questo passaggio, è possibile utilizzare un file CSV qualsiasi, ad esempio il già citato file da scaricare da GitHub. Caricare il file CSV è semplice: è disponibile la relativa opzione quando si crea un BLOB di archiviazione.

Per l'esercitazione, creare un nuovo account di archiviazione facendo clic su **Nuovo** e quindi cercare "account di archiviazione". Selezionare l'icona comparsa per l'account di archiviazione e indicare i dettagli per la creazione dell'account. Fornire un **Nome** (azuresamldemosa nell'esempio), creare o utilizzare un **Gruppo di risorse** esistente e specificare un **Percorso**. Per il percorso, è importante che tutte le risorse create in questa demo utilizzino lo stesso, se possibile.

![Creare un account di archiviazione](./media/stream-analytics-machine-learning-integration-tutorial/create-sa.png)

Una volta fatto, è possibile fare clic sul servizio BLOB e creare un contenitore BLOB.

![Creare un contenitore BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

Specificare quindi un **Nome** per il contenitore (azuresamldemoblob nell'esempio) e verificare che il **Tipo di accesso** sia impostato su "BLOB".

![Creare un tipo di accesso BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

A questo punto è possibile popolare il BLOB con i dati. Selezionare **File** e quindi selezionare il file nell'unità locale scaricato da GitHub. Per questa dimostrazione si selezioneranno BLOB in blocchi e 4 MB come dimensioni. Selezionare quindi **Carica** e il portale creerà un BLOB con l'esempio di testo.

![Creare un file di caricamento BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

Ora che i dati di esempio sono in un BLOB, è necessario abilitare il modello di analisi del sentiment nella raccolta Cortana Intelligence.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Aggiungere il modello di analisi dei sentimenti dalla raccolta Cortana Intelligence.
1. Scaricare il [modello di analisi predittiva dei sentimenti](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) dalla raccolta Cortana Intelligence.  
2. In Machine Learning Studio selezionare **Apri in Studio**.  
   
   ![Analisi di flusso e Machine Learning, aprire Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Effettuare l'accesso per passare all'area di lavoro. Selezionare la posizione più adatta alla propria.
4. Fare clic su **Esegui** in basso.  
5. Una volta avviato il processo, fare clic su **Deploy Web Service**(Distribuisci servizio Web).
6. Il modello di analisi dei sentimenti è pronto per essere usato. Per effettuare una verifica, fare clic sul pulsante **Test** e fornire l'input di testo, ad esempio "I love Microsoft". Dovrebbe comparire un risultato simile al seguente:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Analisi di flusso e Machine Learning, dati di analisi](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Nella colonna **App** selezionare il collegamento **Excel 2010 or earlier workbook** (Cartella di lavoro di Excel 2010 o versione precedente) per ottenere la chiave API e l'URL da usare in seguito per configurare il processo di Analisi di flusso. Questo passaggio è obbligatorio solo per usare il modello di Machine Learning dell'area di lavoro di un altro account di Azure. L'articolo presume che sia necessario per questo scenario.  

Prendere nota dell'URL del servizio Web e della chiave di accesso forniti nel file di Excel scaricato, come mostrato di seguito:  

![Analisi di flusso e Machine Learning, panoramica rapida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Creare un processo di Analisi di flusso che usi il modello di Machine Learning
1. Accedere al [portale di Azure](https://portal.azure.com).  
2. Fare clic su **Nuovo** > **Intelligence e analisi** > **Analisi di flusso**. Immettere un nome per il processo in **Nome processo**, specificare un gruppo di risorse esistente o crearne uno nuovo in base alle esigenze e immettere il percorso appropriato per il processo nel campo **Percorso**.    
   
   ![Creare un processo](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   
3. Dopo aver creato il processo, nella scheda **Input** selezionare **Aggiungere un input**.  
   
   ![Analisi di flusso e Machine Learning, aggiungere input di Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

4. Selezionare **Aggiungi** e quindi specificare un **Alias di Input**, selezionare **Flusso dati**, **Archivio BLOB** come input, quindi selezionare **Avanti**.  
5. Nella pagina **Impostazioni archivio BLOB** della procedura guidata specificare il nome del contenitore BLOB dell'account di archiviazione definito in precedenza durante il caricamento dei dati. Fare clic su **Avanti**. In **Formato di serializzazione eventi** scegliere **CSV**. Per le altre **Impostazioni di serializzazione** , accettare le impostazioni predefinite. Fare clic su **OK**.  
   
   ![Aggiungere il contenitore BLOB di input](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-blob.png)

6. Nella scheda **Output** selezionare **Aggiungi un output**.  
   
   ![Analisi di flusso e Machine Learning, aggiungere output](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

7. Fare clic su **Archivio BLOB**, quindi immettere gli stessi parametri, ad eccezione del contenitore. Il valore di **Input** è stato configurato per la lettura dal contenitore denominato "test" in cui è stato caricato il file **CSV**. In **Output**immettere "testoutput".
8. Verificare che le **Impostazioni di serializzazione** dell'output siano impostate su **CSV**, quindi selezionare il pulsante **OK**.
   
   ![Analisi di flusso e Machine Learning, aggiungere output](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

9. Passare alla scheda **Funzioni** e selezionare **Aggiungi una funzione di Machine Learning**.  
   
   ![Analisi di flusso e Machine Learning, aggiungere funzione di Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  

10. Nella pagina **Impostazioni servizio Web di Machine Learning** individuare l'area di lavoro, il servizio Web e l'endpoint predefinito di Machine Learning. Per questo articolo, applicare manualmente le impostazioni per acquisire familiarità con la configurazione di un servizio Web per qualsiasi area di lavoro purché si conoscano l'URL e la chiave API. Immettere l'**URL** e la **Chiave API** dell'endpoint. Fare clic su **OK**. Si noti che l'**Alias della funzione** è 'sentiment'.  
    
    ![Analisi di flusso e Machine Learning, servizio Web di Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/add-function-endpoints.png)    

11. Nella scheda **Query** modificare la query come indicato di seguito:    
    
    ```
    WITH sentiment AS (  
      SELECT text, sentiment(text) as result from datainput  
    )  
    
    Select text, result.[Scored Labels]  
    Into testoutput  
    From sentiment  
    ```    

12. Fare clic su **Salva** per salvare la query.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Avviare il processo di Analisi di flusso ed esaminare l'output
1. Fare clic su **Avvia** nella parte superiore della pagina del processo.
2. Nella finestra di dialogo **Avvia query** selezionare **Ora personalizzata**, quindi selezionare il giorno precedente al caricamento del file CSV nell'archivio BLOB. Fare clic su **OK**.  
3. Passare all'archivio BLOB tramite lo strumento utilizzato per caricare il file CSV, ad esempio Visual Studio.
4. A pochi minuti dall'avvio del processo, viene creato il contenitore di output in cui viene caricato un file CSV.  
5. Aprire il file nell'editor CSV predefinito. Dovrebbe essere visualizzato un risultato simile al seguente.  
   
   ![Analisi di flusso e Machine Learning, visualizzazione CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusione
In questo articolo viene illustrato come creare un processo dell'analisi di flusso che legge il flusso dei dati di testo e applica l'analisi dei sentimenti ai dati in tempo reale. Per eseguire queste operazioni, non è necessario preoccuparsi della complessità di compilare un modello di analisi dei sentimenti. Questo è uno dei vantaggi della suite Cortana Intelligence.

È possibile osservare anche le metriche correlate alla funzione Azure Machine Learning. Per farlo, selezionare la scheda **Monitoraggio**. Sono visualizzate tre metriche correlate alla funzione.  

* **Richieste della funzione** indica il numero di richieste inviate al servizio Web di Machine Learning.  
* **Eventi della funzione** indica il numero di eventi nella richiesta. Per impostazione predefinita, ogni richiesta a un servizio Web di Machine Learning contiene fino a 1.000 eventi.  
  
    ![Analisi di flusso e Machine Learning, visualizzazione di monitoraggio di Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/job-monitor.png)  


