---
title: Usare gli strumenti di Analisi di flusso di Azure per Visual Studio | Microsoft Docs
description: Esercitazione introduttiva per gli strumenti di Analisi di flusso di Azure per Visual Studio
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
ms.openlocfilehash: b06eae6b85f2ca41390955cde2499af2531e2e12
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Usare gli strumenti di Analisi di flusso di Azure per Visual Studio
Sono ora disponibili pubblicamente gli strumenti di Analisi di flusso di Azure per Visual Studio. Questi strumenti consentono agli utenti di sfruttare meglio Analisi di flusso per risolvere i problemi e scrivere query complesse, anche in locale. Consentono anche di esportare un processo di Analisi di flusso in un progetto di Visual Studio.

## <a name="introduction"></a>Introduzione
Questa esercitazione illustra come usare gli strumenti di Analisi di flusso per Visual Studio per creare, scrivere, testare in locale, gestire ed eseguire il debug dei processi di Analisi di flusso. 

Dopo aver completato questa esercitazione, si sarà in grado di:

* Usare gli strumenti di Analisi di flusso per Visual Studio.
* Configurare e distribuire un processo di Analisi di flusso.
* Testare il processo in locale con dati di esempio locali.
* Usare il monitoraggio per risolvere i problemi.
* Esportare i processi esistenti nei progetti.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Completare i passaggi che precedono "Creare un processo di Analisi di flusso" nell'esercitazione [Compilare una soluzione IoT con Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Installare Visual Studio 2017, Visual Studio 2015 o Visual Studio 2013 Update 4. Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. 
* Seguire le [istruzioni di installazione](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) per installare gli strumenti di Analisi di flusso per Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Creare un progetto di Analisi di flusso
In Visual Studio selezionare **File** > **Nuovo progetto**. Nell'elenco dei modelli a sinistra selezionare **Analisi di flusso** e quindi **Applicazione Analisi di flusso di Azure**.
Nella parte inferiore della pagina inserire il **Nome** del progetto, il **Percorso** e il **Nome soluzione**, come per gli altri progetti.

![Creazione del nuovo progetto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Il progetto **Toll** viene generato in **Esplora soluzioni**.

![Progetto Toll in Esplora soluzioni](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Scegliere la sottoscrizione corretta
1. Dal menu **Visualizza** selezionare **Esplora server** in Visual Studio.

2. Accedere con l'account di Azure personale. 

## <a name="define-input-sources"></a>Definire le origini di input
1. In **Esplora soluzioni** espandere il nodo **Inputs** e rinominare **Input.json** in **EntryStream.json**. Fare doppio clic su **EntryStream.json**.

2. Per **Alias di input** specificare **EntryStream**. L'alias di input viene usato nello script di query.

3. In **Tipo di origine** selezionare **Flusso dati**.

4. In **Origine** selezionare **Hub eventi**.

5. In **Spazio dei nomi del bus di servizio** selezionare l'opzione **TollData** nell'elenco a discesa.

6. In **Nome hub eventi** selezionare **voce**.

7. In **Nome criterio hub eventi** selezionare **RootManageSharedAccessKey** (valore predefinito).

8. In **Formato di serializzazione eventi** selezionare **Json** e in **Codifica** selezionare **UTF8**.
   
   Le impostazioni avranno quindi un aspetto simile al seguente:
   
   ![Impostazioni di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. Fare clic su **Salva** nella parte inferiore della pagina per terminare la procedura guidata. A questo punto è possibile aggiungere un'altra origine di input per creare il flusso di uscita. Fare clic con il pulsante destro del mouse sul nodo **Inputs** e selezionare **Nuovo elemento**.
   
   ![Nuovo elemento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Nella finestra popup selezionare **Input di Analisi di flusso** e modificare il **Nome** in **ExitStream.json**. Selezionare **Aggiungi**.
   
    ![Aggiungi nuovo elemento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Fare doppio clic su **ExitStream.json** nel progetto e, per completare i campi, seguire la stessa procedura usata per il flusso di entrata. Assicurarsi di immettere **exit** in **Nome hub eventi**, come mostrato nella schermata seguente:
   
    ![Impostazioni ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   A questo punto sono stati definiti due flussi di input.
   
   ![Due flussi di input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Aggiungere successivamente l'input di dati di riferimento per il file BLOB contenente i dati di registrazione dell'automobile.
   
12. Fare clic con il pulsante destro del mouse sul nodo **Input** nel progetto e quindi seguire la stessa procedura usata per gli input di flusso. In **Tipo di origine** selezionare **Dati di riferimento** e in **Alias di input** immettere **Registrazione**.
   
    ![Impostazioni di registrazione](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Selezionare l'account di **Archiviazione** contenente l'opzione con **TollData**. Il nome del contenitore è **TollData** e il **Modello percorso** è **registration.json**. Il nome file fa distinzione tra maiuscole e minuscole e deve contenere solo lettere minuscole.

14. Fare clic su **Salva** per completare la procedura guidata.

Tutti gli input sono ora definiti.

## <a name="define-output"></a>Definire l'output
1. In **Esplora soluzioni** espandere il nodo **Inputs** e fare doppio clic su **Output.json**.

2. In **Alias di output** immettere **output**. In **Sink** selezionare **Database SQL**.

3. In **Database** immettere il nome **TollDataDB**.

4. In **Nome utente** immettere **tolladmin**. In **Password** immettere **123toll!**. In **Tabella** immettere **TollDataRefJoin**.

5. Selezionare **Salva**.

   ![Impostazioni di output](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Query di analisi di flusso
Lo scopo di questa esercitazione è di rispondere a varie domande aziendali correlate ai dati dei caselli. Sono state create query che possono essere usate in Analisi di flusso per fornire risposte pertinenti. Prima di iniziare il primo processo di Analisi di flusso, si esaminerà uno scenario semplice e la sintassi delle query.

### <a name="introduction-to-stream-analytics-query-language"></a>Introduzione al linguaggio di query di Analisi di flusso
Si supponga di dover contare il numero di veicoli che entrano in un casello. Trattandosi di un flusso continuo di eventi, è necessario stabilire un periodo di tempo. La domanda dovrà essere posta come segue: "Quanti veicoli entrano in un casello ogni tre minuti?". Questo tipo di misurazione viene comunemente definito conteggio a cascata.

Si osservi la query di Analisi di flusso che risponde a questa domanda:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Come si può notare, Analisi di flusso di Azure usa un linguaggio di query simile a SQL e aggiunge alcune estensioni per specificare gli aspetti temporali della query.

Per maggiori dettagli, vedere gli articoli di MSDN sui costrutti relativi alla [gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e alla [funzione di windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) usati nella query.

Dopo aver scritto la prima query di Analisi di flusso, occorre testarla usando i file di dati di esempio disponibili nella cartella TollApp nel percorso seguente:

**..\TollApp\TollApp\Data**

Questa cartella contiene i file seguenti:

* Entry.json
* Exit.json
* registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Domanda: Numero di veicoli che entrano in un casello
Nel progetto fare doppio clic su **Script.asaql** per aprire lo script nell'editor. Incollare nell'editor lo script della sezione precedente. L'editor di query supporta IntelliSense, i colori sintassi e l'indicatore di errore.

![Editor di query](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Eseguire test locali delle query di Analisi di flusso
Innanzitutto, è possibile compilare la query per verificare la presenza di errori di sintassi.

1. Per convalidare la query con i dati di esempio è possibile usare i dati di esempio locali facendo clic con il pulsante destro del mouse sull'input e selezionando **Aggiungere un input locale**.
   
   ![Add local input (Aggiungi input locale)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. Nella finestra popup selezionare i dati di esempio dal percorso locale. Selezionare **Salva**.
   
   ![Add local input (Aggiungi input locale)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Verrà aggiunto automaticamente un file denominato **local_EntryStream.json** nella cartella degli input.
   
   ![Elenco di file della cartella di input locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Nell'editor di query fare clic su **Esecuzione locale**. oppure premere il tasto F5.
   
   ![Esecuzione locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   È possibile trovare il percorso di output dall'output della console e premere un tasto qualsiasi per aprire la cartella dei risultati.
   
   ![Esecuzione locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Controllare i risultati nella cartella locale.
   
   ![Risultato nella cartella locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Input di esempio
È possibile anche campionare dati di input da origini di input in un file locale. Fare clic con il pulsante destro del mouse sul file di configurazione degli input e selezionare **Dati di esempio**. 

![Dati di esempio](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Tenere presente che, per il momento, è possibile campionare solo Hub eventi o hub IoT. Non sono supportate altre origini di input. Nella finestra di dialogo popup immettere il percorso locale usato per salvare i dati di esempio. Selezionare **Esempio**.

![Configurazione dati di esempio](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Nella finestra **Output** viene visualizzato lo stato dell'operazione. 

![Output dei dati di esempio](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Inviare ad Azure una query di Analisi di flusso
1. Nell'**Editor di query** selezionare **Invia ad Azure** nell'editor di script.

   ![Inviare ad Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Selezionare **Creare un nuovo processo di analisi di flusso di Azure**. In **Nome processo** immettere **TollApp**. Scegliere la **Sottoscrizione** corretta dall'elenco a discesa. Selezionare **Submit** (Invia).

   ![Inviare il processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Avviare il processo
A questo punto, il processo è stato creato e viene aperta automaticamente la visualizzazione del processo. 
1. Fare clic sulla freccia verde per avviare il processo.

   ![Pulsante Avvia processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Scegliere l'impostazione predefinita e fare clic su **Avvia**.
 
   ![Avviare il processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Osservare come lo stato del processo sia stato modificato in **In esecuzione** e siano presenti eventi di input/output.

   ![Metriche e riepilogo del processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Controllare i risultati in Visual Studio
1. Aprire Esplora server di Visual Studio e fare clic con il pulsante destro del mouse sulla tabella **TollDataRefJoin** .

2. Selezionare **Mostra dati tabella** per vedere l'output del processo.
   
   ![Mostra dati tabella](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Visualizzare le metriche di processo
Alcune statistiche di base sul processo sono disponibili in **Metriche del processo**. 

![Metriche del processo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Elencare il processo in Esplora server
In **Esplora server** selezionare **Processi di Analisi di flusso** e quindi **Aggiorna**. Il processo viene visualizzato in **Processi di Analisi di flusso**.

![Elenco processi](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Aprire la visualizzazione del processo
Espandere il nodo del processo e fare doppio clic sul nodo **Vista processi** per aprire la visualizzazione del processo.

![Job View (Visualizzazione processo)](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Esportare un processo esistente in un progetto
Esistono due modi per esportare un processo esistente in un progetto.
* In **Esplora server**, nel nodo **Processi di Analisi di flusso**, fare clic con il pulsante destro del mouse sul nodo del processo. Selezionare **Esporta in un nuovo progetto di analisi di flusso**.
   
   ![Esportare in un nuovo progetto di analisi di flusso](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Il progetto generato viene visualizzato in **Esplora soluzioni**.
   
    ![Processo in Esplora soluzioni](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* Nella vista processi selezionare **Genera progetto**.
   
   ![Genera progetto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni
 
* Il test locale non funziona se nella query sono incluse funzioni geospaziali.
* Nell'editor non è disponibile alcun supporto per l'aggiunta o la modifica di funzioni JavaScript definite dall'utente.
* Il test locale non supporta il salvataggio dell'output in formato JSON. 
* Non è disponibile alcun supporto per gli output di Power BI e ADLS.



## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


