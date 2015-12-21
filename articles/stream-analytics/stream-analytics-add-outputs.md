<properties 
	pageTitle="Come configurare gli output dei dati per i processi di Analisi di flusso | Microsoft Azure" 
	description="Configurare gli output per i processi di Analisi di flusso | segmento del percorso di apprendimento."
	keywords="output dei dati, spostamento dei dati"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="12/04/2015" 
	ms.author="jeffstok"/>

# Come configurare gli output dei dati per i processi di Analisi di flusso

I processi di Analisi di flusso di Azure possono essere connessi a uno o più output dei dati, che definisce una connessione a un sink di dati esistente. Poiché il processo di Analisi di flusso elabora e trasforma i dati in entrata, un flusso di eventi di output dei dati viene scritto nell'output del processo.

È possibile usare gli output dei dati di Analisi di flusso per creare dashboard o avvisi in tempo reale, attivare i flussi di lavoro degli spostamenti dei dati o semplicemente archiviare i dati per l'elaborazione batch in un secondo momento. L’analisi di flusso dispone dell’integrazione di prima classe con diversi servizi di Azure, che sono documentati in dettaglio di seguito.

Per aggiungere un output al processo di analisi di flusso:

1. Nel portale di Azure fare clic su **Output** e poi scegliere **Aggiungi output** nel processo di analisi di flusso.

    ![Aggiungere output](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

    Nel portale di anteprima di Azure fare clic sul riquadro **Output** nel processo di analisi di flusso.

    ![Aggiungere output dal portale di Azure](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Specificare il tipo di output:

    ![Scegliere il tipo di spostamento dei dati](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

    ![Portale di anteprima di Azure, selezionare il tipo di spostamento dei dati](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Fornire un nome descrittivo per l'output nella finestra di **Alias di output**. Questo nome verrà utilizzato nella query del processo in un secondo momento per fare riferimento all'output.
    
    Compilare il resto delle proprietà di connessione necessarie per connettersi all'output. Questi campi variano in base al tipo di output e vengono definiti in modo dettagliato di seguito.

    ![Aggiungere proprietà dell'output dei dati](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. A seconda del tipo di output, può essere necessario specificare la modalità di serializzazione o formattazione dei dati. Di seguito sono descritte le impostazioni di serializzazione specifiche per ogni tipo di output.

    Compilare il resto delle proprietà di connessione necessarie per connettersi all'origine dati. Questi campi variano in base al tipo di origine e di input e vengono definiti in modo dettagliato [qui](stream-analytics-create-a-job.md).

    ![Aggiungere l'output dei dati all'hub eventi](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

    ![Portale di anteprima di Azure, output dei dati nell'hub eventi](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)

> [Azure.Note]Qualsiasi elemento output aggiunto al processo, deve esistere prima che il processo venga avviato e gli eventi avviino il flusso. Ad esempio, se si utilizza l'archiviazione Blob come output, il processo non creerà un account di archiviazione automaticamente. Deve essere creato dall'utente prima che venga avviato il processo ASA.

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1210_2015-->