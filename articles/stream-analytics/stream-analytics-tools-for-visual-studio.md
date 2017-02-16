---
title: Usare gli strumenti di Analisi di flusso di Azure per Visual Studio | Microsoft Docs
description: Esercitazione introduttiva per gli strumenti di Analisi di flusso di Azure per Visual Studio
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: 
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 81a5678051b026a16bdae2f2eab7ead2c17f9563
ms.openlocfilehash: 4874c52b24d9c69fa1d1648035102aaef276f944


---
# <a name="use-azure-stream-analytics-tool-for-visual-studio"></a>Usare gli strumenti di Analisi di flusso di Azure per Visual Studio
Sono ora disponibili pubblicamente gli strumenti di Analisi di flusso di Azure per Visual Studio. Questi strumenti consentono agli utenti di sfruttare meglio Analisi di flusso per risolvere i problemi e scrivere query complesse, anche in locale. Consentono inoltre di esportare un processo di Analisi di flusso in un progetto di Visual Studio.

## <a name="introduction"></a>Introduzione
In questa esercitazione si apprenderà come usare gli strumenti di Analisi di flusso di Azure per Visual Studio per creare, scrivere, testare in locale, gestire ed eseguire il debug dei processi di Analisi di flusso di Azure. 

Dopo aver completato questa esercitazione, si sarà in grado di:
* Acquisire familiarità con gli strumenti di Analisi di flusso di Azure per visual Studio.
* Configurare e distribuire un processo di Analisi di flusso.
* Testare il processo in locale con dati di esempio locali.
* Usare il monitoraggio per risolvere i problemi.
* Esportare i processi esistenti nei progetti.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, sono necessari i prerequisiti seguenti:
* Completare i passaggi precedenti a **Creare un processo di Analisi di flusso** nell'[esercitazione Compilare una soluzione IoT con Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Visual Studio 2015, Visual Studio 2013 Update 4 oppure Visual Studio 2012. Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. Visual Studio 2017 non è attualmente supportato. 
* Microsoft Azure SDK per .NET versione 2.7.1 o successiva.  Installarlo usando il [programma di installazione della piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
* Installazione degli [strumenti di Analisi di flusso di Azure per Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Creare un progetto di Analisi di flusso
Nel **menu File** in Visual studio scegliere **Nuovo progetto**. Selezionare **Analisi di flusso** dall'elenco di modelli a sinistra e fare clic su **Applicazione di Analisi di flusso di Azure**.
Inserire il nome del progetto, il percorso e il nome della soluzione in fondo, come per gli altri progetti.

![Creare un progetto di Analisi di flusso di Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Si noterà un progetto **Toll** generato in **Esplora soluzioni**.

![Creare un progetto di Analisi di flusso di Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Scegliere la sottoscrizione corretta
1. Aprire **Esplora server** in Visual Studio dal menu **Visualizza**.
2. Accedere con l'account di Azure. 

## <a name="define-input-sources"></a>Definire le origini di input
1.  In **Esplora soluzioni** espandere il nodo **Input** e rinominare **Input.json** in **EntryStream.json**. Fare doppio clic su **EntryStream.json**.
2.  L'**ALIAS DI INPUT** dovrebbe ora essere **EntryStream**. Si noti che l'alias di input verrà usato nello script della query. 
3.  Il tipo di origine è **Flusso di dati**.
4.  L'origine è **hub eventi**.
5.  Lo spazio dei nomi del bus di servizio deve essere **tollData** nell'elenco a discesa.
6.  Nome hub eventi deve essere impostato su **entry**.
7.  Nome criterio hub eventi è **RootManageSharedAccessKey** (il valore predefinito).
8.  Selezionare **JSON** per **FORMATO DI SERIALIZZAZIONE EVENTI** e **UTF8** per **CODIFICA**.
   
   Le impostazioni vengono visualizzate in questo modo:
   
   ![Definire le origini di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9.  Fare clic su **Salva** nella parte inferiore della pagina per terminare la procedura guidata. A questo punto è possibile aggiungere un'altra origine di input per creare il flusso di uscita. Fare clic con il pulsante destro del mouse sul nodo degli input e fare clic su **Nuovo elemento**.
   
   ![Definire le origini di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Nella nuova finestra, selezionare **Input di Analisi di flusso di Azure** e modificare il nome in **ExitStream.json**. Fare clic su **Aggiungi**.
   
   ![Definire le origini di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Fare doppio clic su **ExitStream.json** nel progetto e seguire i passaggi come per il flusso di entrata. Assicurarsi di immettere i valori del nome hub eventi come nello screenshot seguente.
   
   ![Definire le origini di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   A questo punto sono stati definiti due flussi di input.
   
   ![Definire le origini di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Successivamente, si aggiungeranno dati di riferimento per il file BLOB contenente i dati di registrazione dell'automobile.
   
12. Fare clic con il tasto destro del mouse sul nodo **Input** nel progetto, quindi seguire lo stesso processo per gli input di flusso ma selezionare **DATI DI RIFERIMENTO** anziché Flusso dati e Alias di Input come **Registrazione**.
   
   ![Definire le origini di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Selezionare l'account di archiviazione che contiene **tolldata**. Il nome del contenitore deve essere **tolldata** e il **MODELLO PERCORSO** deve essere **registration.json**. Il nome file fa distinzione tra maiuscole e minuscole e deve contenere solo lettere minuscole.
14. Fare clic su **Salva** per completare la procedura guidata.

Ora sono definiti tutti gli input.

## <a name="define-output"></a>Definire l'output
1.  In **Esplora soluzioni** espandere il nodo **Input** e fare doppio clic su **Output.json**.
2.  Impostare l'alias di output su **output** e quindi Sink sul database SQL.
2.  Inserire il nome del database: **TollDataDB**.
3.  Immettere **tolladmin** nel campo **NOME UTENTE**, **123toll!** nel campo **PASSWORD** e **TollDataRefJoin** nel campo **TABELLA**.
4.  Fare clic su **Save**.

![Definire l'output](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="azure-stream-analytics-query"></a>Query di Analisi di flusso di Azure
Questa esercitazione ha lo scopo di rispondere a diverse domande aziendali relative ai dati dei caselli e costruire query di Analisi di flusso da poter usare in Analisi di flusso di Azure per fornire risposte pertinenti.
Prima di iniziare il primo processo di Analisi di flusso, verranno esaminati alcuni semplici scenari e la sintassi delle query.

### <a name="introduction-to-azure-stream-analytics-query-language"></a>Introduzione al linguaggio di query di Analisi di flusso di Azure
Si supponga di dover contare il numero di veicoli che entra in un casello. Trattandosi di un flusso continuo di eventi, è necessario stabilire un "periodo di tempo". La domanda dovrà essere posta come segue: "Quanti veicoli entrano in un casello ogni tre minuti?". Questo tipo di conteggio viene detto a cascata.

Si osservi la query di Analisi di flusso di Azure che risponde a questa domanda:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Come si può notare, Analisi di flusso di Azure usa un linguaggio di query simile a SQL e aggiunge alcune estensioni per specificare gli aspetti temporali della query.

Per maggiori dettagli, vedere gli articoli di MSDN sui costrutti relativi alla [gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e alla [funzione di windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) usati nella query.

Dopo aver scritto la prima query di Analisi di flusso di Azure, occorre testarla con file di dati di esempio disponibili nella cartella TollApp nel percorso seguente:

**..\TollApp\TollApp\Data**

Questa cartella contiene i file seguenti: •   Entry.json •   Exit.json •   Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Domanda: Numero di veicoli che entrano in un casello
Nel progetto, fare doppio clic su Script.asaql per aprire lo script nell'editor e incollare al suo interno lo script indicato nella sezione precedente. L'editor di query supporta Intellisense, i colori sintassi e l'indicatore di errore.

![Edit query](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="testing-azure-stream-analytics-queries-locally"></a>Eseguire test locali delle query di analisi di flusso di Azure

1. Innanzitutto, è possibile compilare la query per verificare la presenza di errori di sintassi. [Da definire]
2. Per convalidare la query con i dati di esempio è possibile usare i dati di esempio locali facendo clic con il tasto destro del mouse sull'input e selezionando **Add local input** (Aggiungi input locale) dal menu contestuale.
   
   ![Add local input (Aggiungi input locale)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
   Nella finestra popup selezionare i dati di esempio dal percorso locale. Fare clic su **Save**.
   
   ![Add local input (Aggiungi input locale)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Verrà aggiunto automaticamente un file denominato **local_EntryStream.json** nella cartella degli input.
   
   ![Add local input (Aggiungi input locale)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Fare clic su Esecuzione locale nell'editor di query. oppure premere il tasto F5.
   
   ![Esecuzione locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   È possibile trovare il percorso di output dall'output della console e premere un tasto qualsiasi per aprire la cartella dei risultati.
   
   ![Esecuzione locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Controllare i risultati nella cartella locale.
   
   ![Esecuzione locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Input di esempio
È inoltre possibile campionare dati di input dalle origini degli input nel file locale. Fare clic con il tasto destro del mouse sul file di configurazione degli input e selezionare **Dati di esempio**. 

![Dati di esempio](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Si noti che al momento è possibile campionare solamente Hub eventi o l'hub IoT. Non sono supportate altre origini di input.  Nella finestra di dialogo popup indicare il percorso locale in cui salvare i dati di esempio. Fare clic su **Esempio**.

![Dati di esempio](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Nella finestra di output viene visualizzato lo stato dell'operazione. 

![Dati di esempio](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-azure-stream-analytics-query-to-azure"></a>Inviare ad Azure query di Analisi di flusso di Azure
Nell'**Editor di query** fare clic su **Submit To Azure** (Invia ad Azure) nell'editor di script.

![Inviare un processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
Selezionare Create a New Azure Stream Analytics Job (Crea un nuovo progetto di Analisi di flusso di Azure). Inserire il nome del processo come indicato di seguito. Scegliere la sottoscrizione corretta. Fare clic su Invia.

![Inviare un processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-job"></a>Avviare il processo
A questo punto il processo è stato creato e viene aperta automaticamente la visualizzazione del processo. Fare clic sul pulsante **VERDE** per avviare il processo.

![Avviare il processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
Scegliere l'impostazione predefinita e fare clic su **Avvia**.
 
![Avviare il processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

Si noti che lo stato del processo viene modificato in **In esecuzione** e che sono presenti eventi di input/output.

![Avviare il processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Controllare i risultati in Visual Studio
1. Aprire Esplora server di Visual Studio e fare clic con il pulsante destro del mouse sulla tabella **TollDataRefJoin** .
2. Fare clic su **Mostra dati tabella** per visualizzare l'output del processo.
   
   ![Selezione di "Mostra dati tabella" in Esplora server](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Visualizzare le metriche di processo
È possibile trovare alcune statistiche di base sul processo in **Job Metrics** (Metriche di processo). 

![Job Metrics (Metriche di processo)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-job-in-server-explorer"></a>Elencare i processi in Esplora server
Fare clic su **Processi di Analisi di flusso** in **Esplora server** e fare clic su **Aggiorna**. Il processo dovrebbe essere visualizzato in **Processi di Analisi di flusso**.

![Elencare i processi](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-job-view"></a>Aprire la visualizzazione del processo
Espandere il nodo del processo e fare doppio clic sul nodo **Job View** (Visualizzazione processo) per aprire la visualizzazione del processo.

![Visualizzazione del processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Esportare un processo esistente in un progetto
Esistono due modi per esportare un processo esistente in un progetto.
1. Fare clic con il pulsante destro del mouse sul nodo **Processi di Analisi di flusso** in **Esplora server**. Fare clic su **Export to New Stream Analytics Project** (Esporta in un nuovo progetto di Analisi di flusso) nel menu contestuale.
   
   ![Esportare un processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Verrà visualizzato il progetto generato in **Esplora soluzioni**.
   
   ![Esportare un processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
2. Nella visualizzazione dei processi, fare clic su **Generate Project** (Genera progetto).
   
   ![Esportare un processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni
 
1. Il test locale non funziona se la query dispone di funzioni geospaziali. 
2. Nessun supporto dell'editor per l'aggiunta o la modifica di funzioni JavaScript definite dall'utente.
3. Il test locale non supporta il salvataggio dell'output in formato JSON. 
4. Nessun supporto per l'output di Power BI e ADLS.



## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)





<!--HONumber=Feb17_HO1-->


