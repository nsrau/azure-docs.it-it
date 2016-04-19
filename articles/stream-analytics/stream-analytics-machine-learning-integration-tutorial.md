<properties 
	pageTitle="Esercitazione: Analisi dei sentimenti con l'analisi di flusso di Azure e Azure Machine Learning | Microsoft Azure" 
	description="Come sfruttare la funzione definita dall'utente e Machine Learning nei processi di Analisi di flusso"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"
/>

# Esercitazione: Eseguire l'analisi dei sentimenti con analisi di flusso e Machine Learning #

Questa esercitazione è stata realizzata per consentire la configurazione rapida dell'integrazione di un semplice processo di Analisi di flusso con Machine Learning. Si sfrutterà un modello di Machine Learning per l'analisi dei sentimenti proveniente dalla raccolta Cortana Intelligence per analizzare il flusso di dati di testo e determinare il punteggio dei sentimenti in tempo reale. Questa esercitazione è utile per comprendere scenari come l'analisi dei sentimenti in tempo reale in un flusso di dati di Twitter, l'analisi dei record delle chat dei clienti con il personale del supporto, i commenti in forum/blog/video e diversi altri scenari di assegnazione punteggi predittivi in tempo reale.
  
In questa esercitazione viene fornito un file CSV di esempio contenente testo (come illustrato sotto nella figura 1) come input nell'archivio BLOB di Azure. Il processo applicherà il modello di analisi dei sentimenti come funzione definita dall'utente ai dati del testo di esempio dell'archivio BLOB. Il risultato finale verrà inserito nello stesso nell'archivio BLOB di Azure in un altro file CSV. Un diagramma di questa configurazione è disponibile sotto nella figura 2. Per uno scenario più realistico, questo input per l'archivio BLOB può essere sostituito con il flusso di dati di Twitter provenienti da un input di Hub eventi di Azure. È anche possibile compilare una visualizzazione in tempo reale di [Power BI](https://powerbi.microsoft.com/) del sentimento di aggregazione. Le iterazioni future di questo articolo includeranno tali estensioni.

Figura 1:

![Figura 1 dell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

Figura 2:

![Figura 2 dell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

## Prerequisiti

I prerequisiti per questo articolo sono i seguenti:

1.	Una sottoscrizione di Azure attiva
2.	Un file CSV contenente alcuni dati. Quello illustrato nella figura 2 è fornito [in GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) per il download o è possibile crearne uno nuovo. Questa esercitazione è stata scritta presumendo che venga usato quello disponibile per il download.

In generale, verranno eseguiti i passaggi seguenti:

1.	Caricare il file di input CSV in Archiviazione BLOB
2.	Aggiungere un modello di analisi dei sentimenti dalla raccolta Cortana Intelligence all'area di lavoro di Machine Learning
3.	Distribuire questo modello come servizio Web nell'area di lavoro di Azure Machine Learning
4.	Creare un processo di Analisi di flusso che chiami questo servizio Web come funzione per determinare il sentimento per l'input di testo
5.	Avviare il processo di Analisi di flusso ed esaminare l'output 


## Caricare il file di input CSV in Archiviazione BLOB

Per questo passaggio, è possibile usare qualsiasi file CSV, incluso quello specificato nell'introduzione. Per caricare il file, è possibile usare [Azure Storage Explorer](http://storageexplorer.com/) o Visual Studio, ma anche un codice personalizzato. Per questa esercitazione vengono forniti esempi per Visual Studio.

1.	Espandere Azure e fare clic con il pulsante destro del mouse su **Archiviazione**. Scegliere **Associa archiviazione esterna** e specificare **Nome account** e **Chiave account**.  

    ![Esplora server nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Espandere l'archiviazione associata, scegliere **Crea contenitore BLOB** e immettere un nome logico. Una volta creato, fare doppio clic sul contenitore per visualizzarne i contenuti (per il momento sarà vuoto).

    ![Creazione BLOB nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Caricare il file CSV facendo clic sull'icona **Carica BLOB** e quindi scegliere **file dal disco locale**.

## Aggiungere il modello di analisi dei sentimenti dalla raccolta Cortana Intelligence

1.	Scaricare il [modello di analisi predittiva dei sentimenti](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) nella raccolta Cortana Intelligence.  
2.	Fare clic su **Open** in Studio:  

    ![Apertura di Machine Learning Studio nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Effettuare l'accesso per passare all'area di lavoro. Scegliere la posizione più adatta alla propria.
4.	Fare clic su **Run** nella parte inferiore di Studio.  
5.	Una volta in esecuzione, fare clic su **Deploy Web Service**.
6.	Il modello di analisi dei sentimenti è pronto per essere usato. Per la convalida, fare clic sul pulsante **test** e immettere il testo di input, ad esempio "I love Microsoft". Il test restituirà un risultato simile al seguente:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![Dati di analisi nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

Fare clic sul collegamento della cartella di lavoro **Excel 2010 or earlier** per ottenere la chiave API e l'URL che saranno necessari in seguito per configurare il processo di Analisi di flusso. Questo passaggio è obbligatorio solo per sfruttare un modello di Machine Learning dell'area di lavoro di un altro account di Azure. L'esercitazione presume che sia necessario per questo scenario.

![Esperimento di analisi nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)

Prendere nota dell'URL del servizio Web e della chiave di accesso dal file di Excel scaricato, come mostrato di seguito:

![Riepilogo rapido nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Creare un processo di Analisi di flusso che usi il modello di Machine Learning

1.	Passare al [portale di gestione di Azure](https://manage.windowsazure.com).  
2.	Fare clic su **Nuovo**, **Servizi dati**, **Analisi di flusso** e **Creazione rapida**. Specificare il **Nome processo** e l'**Area** appropriata per il processo e scegliere un **Account di archiviazione per il monitoraggio a livello di area**.    
3.	Una volta creato il processo, passare alla scheda **Input** e fare clic su **Aggiungi input**.  

    ![Aggiunta di input di Machine Learning ai dati nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	Nella prima pagina della finestra della procedura guidata **Aggiungi input** selezionare **Flusso dati** e fare clic su Avanti. Nella seconda pagina selezionare **Archiviazione BLOB** come input e fare clic su **Avanti**.
5.	Nella pagina **Impostazioni archiviazione BLOB** della procedura guidata specificare il nome del contenitore BLOB dell'account di archiviazione definito in precedenza durante il caricamento dei dati. Fare clic su **Avanti**. Scegliere **CSV** come **Formato di serializzazione degli eventi**. Accettare le impostazioni predefinite per le altre **Impostazioni di serializzazione**. Fare clic su **OK**.  
6.	Passare alla scheda **Output** e fare clic su **Aggiungi un output**.  

    ![Aggiunta di output nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Scegliere **Archivio BLOB** e specificare gli stessi parametri a eccezione del contenitore. **Input** è stato configurato per la lettura dal contenitore denominato "test" in cui è stato caricato il file **CSV**. Per **Output**, inserire "testoutput". I nomi dei contenitori devono essere diversi. Verificare che questo contenitore esista.
8.	Fare clic su **Avanti** per configurare le **Impostazioni di serializzazione** dell'output. Come per Input, scegliere **CSV** e fare clic su **OK**.
9.	Passare alla scheda **Funzioni** e fare clic su **Aggiungi una funzione di Machine Learning**.  

    ![Aggiunta di una funzione di Machine Learning nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	Nella pagina **Impostazioni servizio Web Machine Learning** individuare l'area di lavoro, il servizio Web e l'endpoint predefinito di Machine Learning. Per questa esercitazione, applicare manualmente le impostazioni per acquisire familiarità con la configurazione di un servizio Web per qualsiasi area di lavoro purché si conoscano l'URL e la CHIAVE. Specificare l'**URL** e la **Chiave API** dell'endpoint. Fare quindi clic su **OK**.

    ![Servizio Web di Machine Learning nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	Passare alla scheda **Query** e modificare la query come mostrato di seguito:

```
	WITH subquery AS (  
		SELECT text, sentiment(text) as result from input  
	)  
	  
	Select text, result.[Score]  
	Into output  
	From subquery  
```

12. Fare clic su **Salva** per salvare la query.    

## Avviare il processo di Analisi di flusso ed esaminare l'output

1.	Fare clic su **Avvia** nella parte inferiore del processo. 
2.	Nella finestra di dialogo **Avvia query** scegliere **Ora personalizzata** e selezionare un orario precedente al caricamento del file CSV in Archiviazione Blob. Fare clic su **OK**.  

    ![Ora personalizzata nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Passare ad Archiviazione BLOB con lo strumento usato quando è stato caricato il file CSV. Per questa esercitazione è stato usato Visual Studio.
4.	In pochi minuti dall'avvio del processo, viene creato il contenitore di output in cui viene caricato un file CSV.  
5.	Fare doppio clic sul file per aprire l'editor CSV predefinito e visualizzare un contenuto simile al seguente:  

    ![Visualizzazione csv nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Conclusioni

In questa esercitazione è stato creato un processo di Analisi di flusso che legge i flussi di dati di testo e l'analisi dei sentimenti applicata in tempo reale. Tutto questo è stato fatto senza doversi preoccupare delle difficoltà derivanti dalla compilazione di un modello di analisi dei sentimenti. Questo è uno dei vantaggi della suite Cortana Intelligence.

È possibile osservare anche le metriche correlate alla funzione Azure Machine Learning. Fare clic sulla scheda **MONITOR**. Sono presenti tre metriche correlate alla funzione.
  
- FUNCTION REQUESTS indica il numero di richieste per il servizio Web di Machine Learning.  
- FUNCTION EVENTS indica il numero di eventi nella richiesta. Per impostazione predefinita, ogni richiesta per il servizio Web ML contiene fino a 1000 eventi.  
- FAILED FUNCTION REQUESTS indica il numero di richieste non riuscite per il servizio Web di Machine Learning.  

    ![Visualizzazione monitoraggio ML nell'esercitazione su Machine Learning e Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0406_2016-->