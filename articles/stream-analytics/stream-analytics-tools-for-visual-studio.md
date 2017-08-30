---
title: Usare gli strumenti di Analisi di flusso di Azure per Visual Studio | Microsoft Docs
description: Esercitazione introduttiva per gli strumenti di Analisi di flusso di Azure per Visual Studio
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: samacha
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Usare gli strumenti di Analisi di flusso di Azure per Visual Studio
## <a name="introduction"></a>Introduzione
Questa esercitazione illustra come usare gli strumenti di Analisi di flusso di Azure per Visual Studio per creare, scrivere, testare in locale, gestire ed eseguire il debug dei processi di Analisi di flusso di Azure. 

Dopo aver completato questa esercitazione, si sarà in grado di:
* Usare gli strumenti di Analisi di flusso di Azure per Visual Studio.
* Configurare e distribuire un processo di Analisi di flusso.
* Testare il processo in locale con dati di esempio locali.
* Usare il monitoraggio per risolvere i problemi.
* Esportare i processi esistenti nei progetti.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:
* Completare i passaggi che precedono "Creare un processo di Analisi di flusso" nell'esercitazione [Compilare una soluzione IoT con Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Usare Visual Studio 2015, Visual Studio 2013 Update 4 oppure Visual Studio 2012. Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. Visual Studio 2017 non è supportato. 
* Usare Azure SDK per .NET versione 2.7.1 o successiva. Eseguire l'installazione usando [Installazione guidata piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
* Installare gli [strumenti di Analisi di flusso di Azure per Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Creare un progetto di Analisi di flusso
1. In Visual Studio fare clic sul menu **File** e selezionare **Nuovo progetto**. 

2. Nell'elenco dei modelli a sinistra selezionare **Analisi di flusso** e quindi fare clic su **Applicazione Analisi di flusso di Azure**.

3. Immettere il **Nome**, il **Percorso** e il **Nome della soluzione** del progetto come per altri progetti.

    ![Finestra Nuovo progetto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    Verrà generato un progetto **Toll** in **Esplora soluzioni**.

    ![Progetto Toll generato in Esplora soluzioni](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Scegliere la sottoscrizione corretta
1. In Visual Studio fare clic sul menu **Visualizza** e aprire **Esplora server**.

2. Accedere con l'account Azure. 

## <a name="define-the-input-sources"></a>Definire le origini di input
1.  In **Esplora soluzioni** espandere il nodo **Inputs** e rinominare **Input.json** in **EntryStream.json**. Fare doppio clic su **EntryStream.json**.
2.  L'**Alias di input** è ora **EntryStream**. L'alias di input viene usato nello script di query. 
3.  In **Tipo di origine** selezionare **Flusso dati**.
4.  In **Origine** selezionare **Hub eventi**.
5.  In **Spazio dei nomi del bus di servizio** selezionare l'opzione **TollData**.
6.  In **Nome hub eventi** selezionare **voce**.
7.  In **Nome criterio hub eventi** selezionare **RootManageSharedAccessKey** (valore predefinito).
8.  In **Formato di serializzazione eventi** selezionare **Json**. 
9.  In **Codifica** selezionare **UTF-8**. Le impostazioni dovrebbero essere simili allo screenshot seguente:

    ![Finestra di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. Per completare la procedura guidata fare clic su **Salva**. A questo punto è possibile aggiungere un'altra origine di input per creare il flusso di uscita. Fare clic con il pulsante destro del mouse sul nodo **Inputs** e selezionare **Nuovo elemento**.

    ![Nuovo elemento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. Nella finestra selezionare **Input di Analisi di flusso di Azure** e modificare il **Nome** in **ExitStream.json**. Fare clic su **Aggiungi**.

    ![Finestra Aggiungi nuovo elemento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. Fare doppio clic su **ExitStream.json** nel progetto e seguire gli stessi passaggi del flusso di entrata. Assicurarsi di immettere **exit** in **Nome hub eventi** come mostrato nella schermata seguente:

    ![Finestra ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    A questo punto sono presenti due flussi di input definiti:

    ![Flussi di input di entrata e di uscita](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    Aggiungere successivamente l'input di dati di riferimento per il file BLOB contenente i dati di registrazione dell'automobile.

13. Fare clic con il pulsante destro del mouse sul nodo **Inputs** nel progetto e quindi seguire gli stessi passaggi degli input di flusso. In **Alias di input** immettere **Registrazione** e in **Tipo di origine** selezionare **Dati di riferimento**.

    ![Finestra Registrazione](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. In **Account di archiviazione** selezionare l'opzione **tolldata**. In **Contenitore** selezionare **tolldata** e in **Modello percorso** immettere **registration.json**. Il nome file fa distinzione tra maiuscole e minuscole e deve contenere solo lettere minuscole.
15. Per completare la procedura guidata fare clic su **Salva**.

Tutti gli input sono ora definiti.

## <a name="define-the-output"></a>Definire l'output
1.  In **Esplora soluzioni** espandere il nodo **Inputs** e fare doppio clic su **Output.json**.

2.  In **Alias di output** immettere **output**. 
3.  In **Sink** selezionare **Database SQL**.
4.  In **Database** selezionare **TollDataDB**.
5.  In **Nome utente** immettere **tolladmin**. 
6.  In **Password** immettere **123toll!**.
7.  In **Tabella** immettere **TollDataRefJoin**.
8.  Fare clic su **Salva**.

    ![Finestra Output](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>Creare una query di Analisi di flusso
Lo scopo di questa esercitazione è di rispondere a varie domande aziendali correlate ai dati dei caselli. Crea anche query di Analisi di flusso che possono essere usate in Analisi di flusso per fornire risposte pertinenti.
Prima di iniziare il primo processo di Analisi di flusso, esaminiamo uno scenario semplice e la sintassi delle query.

### <a name="introduction-to-the-stream-analytics-query-language"></a>Introduzione al linguaggio di query di Analisi di flusso di Azure
Si supponga di dover contare il numero di veicoli che entra in un casello. Trattandosi di un flusso continuo di eventi, è necessario stabilire un periodo di tempo. La domanda dovrà essere posta come segue: "Quanti veicoli passano un casello ogni tre minuti?" Questo tipo di conteggio viene detto a cascata.

Si osservi la query di Analisi di flusso che risponde a questa domanda:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Analisi di flusso usa un linguaggio di query simile a SQL e aggiunge alcune estensioni per specificare gli aspetti temporali della query.

Per maggiori dettagli, vedere gli articoli di MSDN sui costrutti relativi alla [gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e alla [funzione di windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) usati nella query.

Ora che è stata scritta la prima query di Analisi di flusso, è necessario eseguirne il test. Usare i file di dati di esempio che si trovano nella cartella TollApp nel percorso seguente:

..\TollApp\TollApp\Data

Questa cartella contiene i file seguenti:
*   Entry.json
*   Exit.json
*   registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>Contare il numero di veicoli che passano un casello
Nel progetto fare doppio clic su **Script.asaql** per aprire lo script nell'**Editor di query**. Copiare e incollare nell'editor lo script della sezione precedente. L'editor di query supporta IntelliSense, la sintassi colorata e l'indicatore di errore.

![Editor di query](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Eseguire test locali delle query di Analisi di flusso

1. Per compilare la query e vedere se sono presenti errori di sintassi, fare clic con il pulsante destro del mouse sul progetto e selezionare **Genera**. 

2. Per convalidare la query rispetto ai dati di esempio, è possibile usare dati di esempio locali. Fare clic con il pulsante destro del mouse sull'input e selezionare **Add local input** (Aggiungi input locale).

    ![Add local input (Aggiungi input locale)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. Nella finestra popup selezionare i dati di esempio dal percorso locale. Fare clic su **Salva**.

    ![Finestra Add local input (Aggiungi input locale)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    Verrà aggiunto automaticamente un file denominato **local_EntryStream.json** nella cartella degli input.

    ![File aggiunti alla cartella degli input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. Nell'**Editor di query** fare clic su **Esecuzione locale**. In alternativa premere F5.

    ![Esecuzione locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![Output esecuzione locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    Premere un tasto qualsiasi per visualizzare l'output nella finestra **Risultato esecuzione locale ASA** in Visual Studio. 

    ![Finestra Risultato esecuzione locale ASA](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. Fare clic su **Apri cartella risultati** per controllare i file di output nel formato sia CSV che JSON.

    ![Output di Apri cartella risultati](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>Dati di input di esempio
È possibile anche campionare dati di input da origini di input in un file locale. 
1. Fare clic con il pulsante destro del mouse sul file di configurazione degli input e selezionare **Dati di esempio**. 

   ![Dati di esempio](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    È possibile campionare solo l'hub eventi o l'hub IoT per ora. Non sono supportate altre origini di input.

2. Nella finestra popup immettere il percorso locale usato per salvare i dati di esempio. Fare clic su **Esempio**.

    ![Finestra Dati di esempio](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    Nella finestra **Output** viene visualizzato lo stato dell'operazione. 

    ![Finestra Output](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Inviare ad Azure una query di Analisi di flusso
1. Nell'**Editor di query** fare clic su **Invia ad Azure** nell'editor di script.

    ![Inviare ad Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Selezionare **Creare un nuovo processo di analisi di flusso di Azure**. Immettere il **Nome processo** e selezionare la **Sottoscrizione** corretta. Fare clic su **Submit**.

    ![Finestra di invio del processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>Avviare un processo
Dopo che il processo è stato creato, viene aperta automaticamente la visualizzazione del processo. 
1. Per avviare il processo fare clic sulla **freccia verde**.

    ![Avviare un processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Selezionare l'impostazione predefinita e fare clic su **Avvia**.
 
    ![Finestra Avvia processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    Lo **Stato** del processo viene impostato su **In esecuzione** e vengono visualizzati gli **Eventi di input** e gli **Eventi di output**.

    ![Stato In esecuzione in Riepilogo processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>Controllare i risultati in Visual Studio
1. In Visual Studio aprire **Esplora server** e fare clic con il pulsante destro del mouse sulla tabella **TollDataRefJoin**.
2. Selezionare **Mostra dati tabella** per vedere l'output del processo.
   
    ![Selezione di Mostra dati tabella in Esplora server](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>Visualizzare le metriche del processo
È possibile trovare alcune statistiche di base sul processo in **Job Metrics** (Metriche di processo). 

![Finestra Job Metrics (Metriche di processo)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Elencare il processo in Esplora server
In **Esplora server** fare clic su **Processi di Analisi di flusso** e quindi fare clic su **Aggiorna**. Il processo viene visualizzato in **Processi di Analisi di flusso**.

![Processi di Analisi di flusso elencati in Esplora server](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Aprire la visualizzazione del processo
Per aprire la visualizzazione del processo, espandere il nodo del processo e fare doppio clic sul nodo **Job View** (Visualizzazione processo).

![Nodo Job View (Visualizzazione processo)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Esportare un processo esistente in un progetto
Esistono due modi per esportare un processo esistente in un progetto.

In **Esplora server** fare clic con il pulsante destro del mouse sul nodo del processo nel nodo **Processi di Analisi di flusso** e selezionare **Esporta in un nuovo progetto di analisi di flusso**.

![Esportare in un nuovo progetto di analisi di flusso](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

Il progetto viene generato in **Esplora soluzioni**.

![Progetto generato in Esplora soluzioni](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
È inoltre possibile usare la visualizzazione del processo e fare clic su **Genera progetto**.

![Genera progetto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni
 
- Non è disponibile alcun supporto per l'output di Power BI e dell'archivio Azure Data Lake.
- Non è disponibile alcun supporto dell'editor per l'aggiunta o la modifica di funzioni JavaScript definite dall'utente.

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

