<properties 
	pageTitle="Aggiungere output | Microsoft Azure" 
	description="Segmento del percorso di apprendimento relativo all’aggiunta di output."
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
	ms.date="11/10/2015" 
	ms.author="jeffstok"/>

# Aggiungere output

I processi di analisi di flusso di Azure possono essere connessi a uno o più output, che definisce una connessione a un sink di dati esistente. Poiché il processo di analisi di flusso elabora e trasforma i dati in entrata, un flusso di eventi di output viene scritto nell'output del processo.

Gli output dell’analisi di flusso sono utilizzabili per creare dashboard in tempo reale o avvisi, attivare i flussi di lavoro downstream o semplicemente archiviare i dati per l'elaborazione batch in un secondo momento. L’analisi di flusso dispone dell’integrazione di prima classe con diversi servizi di Azure, che sono documentati in dettaglio di seguito.

Per aggiungere un output al processo di analisi di flusso:

1. Nel portale di Azure fare clic su **Output** e poi scegliere **Aggiungi output** nel processo di analisi di flusso.

    ![Aggiungere output](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

    Nel portale di anteprima di Azure fare clic sul riquadro **Output** nel processo di analisi di flusso.

    ![Aggiungere output dal portale di Azure](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Specificare il tipo di output:

    ![Selezionare il tipo di dati](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

    ![Selezionare il tipo di dati nel portale di anteprima di Azure](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Fornire un nome descrittivo per l'output nella finestra di **Alias di output**. Questo nome verrà utilizzato nella query del processo in un secondo momento per fare riferimento all'output.
    
    Compilare il resto delle proprietà di connessione necessarie per connettersi all'output. Questi campi variano in base al tipo di output e vengono definiti in modo dettagliato di seguito.

    ![Aggiungere le proprietà](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. A seconda del tipo di output, può essere necessario specificare la modalità di serializzazione o formattazione dei dati. Di seguito sono descritte le impostazioni di serializzazione specifiche per ogni tipo di output.

    Compilare il resto delle proprietà di connessione necessarie per connettersi all'origine dati. Questi campi variano in base al tipo di origine e di input e vengono definiti in modo dettagliato [qui](stream-analytics-create-a-job.md).

    ![Aggiungere l'hub di eventi](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

    ![Aggiungere hub eventi nel portale di anteprima di Azure](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Nov15_HO3-->