<properties
	pageTitle="Analisi dei sentimenti con Analisi di flusso di Azure e Azure Machine Learning | Microsoft Azure"
	description="Come usare una funzione definita dall'utente e Machine Learning in un processo di analisi di flusso"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"
/> 


<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/08/2016" 
	ms.author="jeffstok"
/> 

# Analisi dei sentimenti con Analisi di flusso di Azure e Azure Machine Learning #

Questo articolo è realizzato per consentire di configurare rapidamente l'integrazione di un semplice processo di Analisi di flusso con Machine Learning. Utilizzando un modello di Machine Learning per l'analisi dei sentimenti proveniente dalla raccolta Cortana Intelligence, verrà analizzato il flusso di dati di testo e determinato il punteggio dei sentimenti in tempo reale. Le informazioni di questo articolo illustrano scenari come l'analisi dei sentimenti in tempo reale in un flusso di dati di Twitter, l'analisi dei record delle chat dei clienti con il personale del supporto, la valutazione dei commenti in forum/blog/video e diversi altri scenari di determinazione di punteggi predittivi in tempo reale.

Questo articolo offre un file CSV di esempio con input di testo nell'archivio BLOB di Azure, come da figura seguente. Il processo applica il modello di analisi dei sentimenti come funzione definita dall'utente ai dati del testo di esempio dell'archivio BLOB. Il risultato finale viene inserito nello stesso archivio BLOB di Azure in un altro file CSV.

![Analisi di flusso e Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

L'immagine seguente illustra questa configurazione. Per uno scenario più realistico, è possibile sostituire l'archivio BLOB con il flusso di dati di Twitter provenienti da un input di Hub eventi di Azure. È anche possibile compilare una visualizzazione in tempo reale del sentimento di aggregazione in [Microsoft Power BI](https://powerbi.microsoft.com/).

![Analisi di flusso e Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

## Prerequisiti

I prerequisiti per completare le attività illustrate in questo articolo sono:

-	Una sottoscrizione di Azure attiva.
-	Un file CSV contenente alcuni dati. È possibile scaricare il file mostrato nella Figura 1 da [GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) oppure è possibile creare un file personalizzato. Per questo articolo si presuppone l'utilizzo del file da scaricare su GitHub.

In generale, per completare le attività illustrate in questo articolo, sarà necessario eseguire le operazioni seguenti:

1.	Caricare il file di input CSV in Archivio BLOB di Azure.
2.	Aggiungere un modello di analisi dei sentimenti dalla raccolta Cortana Intelligence all'area di lavoro di Azure Machine Learning.
3.	Distribuire questo modello come servizio Web nell'area di lavoro di Machine Learning.
4.	Creare un processo di Analisi di flusso che chiami questo servizio Web come funzione per determinare il sentimento per l'input di testo.
5.	Avviare il processo di Analisi di flusso ed esaminare l'output.

## Caricare il file di input CSV in Archiviazione BLOB

Per questo passaggio, è possibile utilizzare un file CSV qualsiasi, ad esempio il già citato file da scaricare da GitHub. Per caricare il file, è possibile usare [Esplora archivi di Microsoft Azure](http://storageexplorer.com/) o Visual Studio, ma anche un codice personalizzato. Gli esempi illustrati sono basati su Visual Studio.

1.	In Visual Studio fare clic su **Azure** > **Archiviazione** > **Associa archiviazione esterna**. Digitare il **nome dell'account** e la **chiave dell'account**.

    ![Analisi di flusso e Machine Learning, Esplora server](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Espandere l'archiviazione associata al Passaggio 1, scegliere **Crea contenitore BLOB**, quindi immettere un nome logico. Dopo aver creato il contenitore, aprirlo per visualizzarne il contenuto. In questa fase, sarà vuoto.

    ![Analisi di flusso e Machine Learning, creare BLOB](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Per caricare il file CSV, fare clic su **Carica BLOB** e quindi sul **file del disco locale**.

## Aggiungere il modello di analisi dei sentimenti dalla raccolta Cortana Intelligence.

1.	Scaricare il [modello di analisi predittiva dei sentimenti](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) dalla raccolta Cortana Intelligence.
2.	In Machine Learning Studio fare clic su **Apri in Studio**.

    ![Analisi di flusso e Machine Learning, aprire Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Effettuare l'accesso per passare all'area di lavoro. Selezionare la posizione più adatta alla propria.
4.	Fare clic su **Esegui** in basso.
5.	Una volta avviato il processo, fare clic su **Deploy Web Service** (Distribuisci servizio Web).
6.	Il modello di analisi dei sentimenti è pronto per essere usato. Per effettuare una verifica, fare clic sul pulsante **Test** e fornire l'input di testo, ad esempio "I love Microsoft". Dovrebbe comparire un risultato simile al seguente:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![Analisi di flusso e Machine Learning, dati di analisi](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

Nella colonna **App** fare clic sul collegamento **Excel 2010 or earlier workbook** (Cartella di lavoro di Excel 2010 o versione precedente) per ottenere la chiave API e l'URL da usare in seguito per configurare il processo di Analisi di flusso. Questo passaggio è obbligatorio solo per usare il modello di Machine Learning dell'area di lavoro di un altro account di Azure. L'articolo presume che sia necessario per questo scenario.

Prendere nota dell'URL del servizio Web e della chiave di accesso forniti nel file di Excel scaricato, come mostrato di seguito:

![Analisi di flusso e Machine Learning, panoramica rapida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Creare un processo di Analisi di flusso che usi il modello di Machine Learning

1.	Accedere al [portale di Azure](https://manage.windowsazure.com).
2.	Fare clic su **Nuovo** > **Servizi dati** > **Analisi di flusso** > **Creazione rapida**. Digitare un nome da assegnare al processo in **Nome processo**, immettere l'area del processo appropriata in **Area** e quindi selezionare l'account in **Account di archiviazione di monitoraggio regionale**.
3.	Dopo aver creato il processo, nella scheda **Input** fare clic su **Aggiungere un input**.

    ![Analisi di flusso e Machine Learning, aggiungere input di Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	Nella prima pagina della finestra della procedura guidata **Aggiungi input** selezionare **Flusso dati** e fare clic su **Avanti**. Nella pagina successiva fare clic su **Archivio BLOB** come input, quindi su **Avanti**.
5.	Nella pagina **Impostazioni archivio BLOB** della procedura guidata specificare il nome del contenitore BLOB dell'account di archiviazione definito in precedenza durante il caricamento dei dati. Fare clic su **Avanti**. In **Formato di serializzazione eventi** scegliere **CSV**. Per le altre **Impostazioni di serializzazione**, accettare le impostazioni predefinite. Fare clic su **OK**.
6.	Nella scheda **Output** fare clic su **Aggiungi un output**.

    ![Analisi di flusso e Machine Learning, aggiungere output](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Fare clic su **Archivio BLOB**, quindi immettere gli stessi parametri, ad eccezione del contenitore. Il valore di **Input** è stato configurato per la lettura dal contenitore denominato "test" in cui è stato caricato il file **CSV**. In **Output** immettere "testoutput". I nomi dei contenitori devono essere diversi. Verificare che il contenitore in questione esista.
8.	Fare clic su **Avanti** per configurare le **Impostazioni di serializzazione** dell'output. Come per **Input**, scegliere **CSV** e fare clic sul pulsante **OK**.
9.	Passare alla scheda **Funzioni** e fare clic su **Aggiungi una funzione di Machine Learning**.

    ![Analisi di flusso e Machine Learning, aggiungere funzione di Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	Nella pagina **Impostazioni servizio Web di Machine Learning** individuare l'area di lavoro, il servizio Web e l'endpoint predefinito di Machine Learning. Per questo articolo, applicare manualmente le impostazioni per acquisire familiarità con la configurazione di un servizio Web per qualsiasi area di lavoro purché si conoscano l'URL e la chiave API. Immettere l'**URL** e la **Chiave API** dell'endpoint. Fare clic su **OK**.

    ![Analisi di flusso e Machine Learning, servizio Web di Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	Nella scheda **Query** modificare la query come indicato di seguito:

 ```
 	WITH subquery AS (  
 		SELECT text, sentiment(text) as result from input  
  	)  
 
 	Select text, result.[Scored Labels]  
 	Into output  
 	From subquery  
 ```    
12.	Fare clic su **Salva** per salvare la query.

## Avviare il processo di Analisi di flusso ed esaminare l'output

1.	Fare clic su **Avvia** nella parte inferiore della pagina del processo.
2.	Nella finestra di dialogo **Avvia query** fare clic su **Ora personalizzata**, quindi selezionare un orario precedente al caricamento del file CSV in Archiviazione Blob. Fare clic su **OK**.

    ![Analisi di flusso e Machine Learning, ora personalizzata](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Passare all'archivio BLOB tramite lo strumento utilizzato per caricare il file CSV, ad esempio Visual Studio.
4.	A pochi minuti dall'avvio del processo, viene creato il contenitore di output in cui viene caricato un file CSV.
5.	Aprire il file nell'editor CSV predefinito. Dovrebbe essere visualizzato un risultato simile al seguente.

    ![Analisi di flusso e Machine Learning, visualizzazione CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Conclusione

In questo articolo viene illustrato come creare un processo dell'analisi di flusso che legge il flusso dei dati di testo e applica l'analisi dei sentimenti ai dati in tempo reale. Per eseguire queste operazioni, non è necessario preoccuparsi della complessità di compilare un modello di analisi dei sentimenti. Questo è uno dei vantaggi della suite Cortana Intelligence.

È possibile osservare anche le metriche correlate alla funzione Azure Machine Learning. Per farlo, fare clic sulla scheda **Monitoraggio**. Sono visualizzate tre metriche correlate alla funzione.

- **Richieste della funzione** indica il numero di richieste inviate al servizio Web di Machine Learning.
- **Eventi della funzione** indica il numero di eventi nella richiesta. Per impostazione predefinita, ogni richiesta a un servizio Web di Machine Learning contiene fino a 1.000 eventi.
- **Richieste della funzione non riuscite** indica il numero di richieste non riuscite per il servizio Web di Machine Learning.

    ![Analisi di flusso e Machine Learning, visualizzazione di monitoraggio di Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0921_2016-->