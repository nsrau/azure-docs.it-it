<properties 
	pageTitle="Come avviare processi di streaming in Analisi di flusso | Microsoft Azure" 
	description="Come eseguire un processo di streaming in Analisi di flusso di Azure | segmento del percorso di apprendimento."
    keywords="processi di streaming"
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

# Come eseguire un processo di streaming in Analisi di flusso di Azure

Dopo che sono stati specificati un processo di input, una query e un output, è possibile avviare il processo dell’analisi di flusso.

Per avviare il processo:

1.	Nel portale di Azure, dal dashboard del processo fare clic su **Start** nella parte inferiore della pagina.

    ![Pulsante Avvia processo](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

    Nel portale di anteprima di Azure fare clic su **Start** nella parte superiore della pagina del processo.

    ![Portale di anteprima di Azure, pulsante Avvia processo](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)

2.	Specificare un valore per **Avvia output** per determinare quando questo processo inizierà a produrre output. L'impostazione predefinita per i processi che non sono stati precedentemente avviati è **Ora di inizio processo**, il che significa che il processo avvia immediatamente l'elaborazione dati. È anche possibile specificare un tempo **personalizzato** passato (per l'uso di dati cronologici) o futuro (per posticipare l'elaborazione a un secondo momento). Nei casi in cui un processo è stato precedentemente avviato e arrestato, è disponibile l'opzione **Ultimo arresto** per consentire di riprendere il processo dall'ultima data di output ed evitare la perdita di dati.

    ![Ora di avvio del processo di streaming](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

    ![Portale di anteprima di Azure, ora di avvio del processo di streaming](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)

3.	Confermare la selezione. Lo stato del processo cambierà in *Avvio in corso* e passerà rapidamente a *In esecuzione* dopo aver avviato il processo. È possibile monitorare lo stato di avanzamento dell'operazione di **Avvio** nell'**Hub di notifica**:

    ![avanzamento del processo di streaming](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

    ![Portale di anteprima di Azure, avanzamento del processo di streaming](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1210_2015-->