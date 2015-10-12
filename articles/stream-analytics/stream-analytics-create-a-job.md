<properties 
	pageTitle="Creare un processo | Microsoft Azure" 
	description="Segmento del percorso di apprendimento relativo alla creazione del processo."
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
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>

# Creare un processo

La risorsa di livello principale nell’analisi di flusso di Azure è il processo di analisi del flusso. È costituito da una o più origini dati di input, una query che esprime la trasformazione dei dati e uno o più destinazioni di output in cui vengono scritti i risultati.

Per iniziare a utilizzare l’analisi di flusso, creare un nuovo processo di analisi di flusso. Si noti che questa azione non ha implicazioni sulla fatturazione fino a quando il processo non viene avviato.

1.  Accedere al [portale di Microsoft Azure](http://manage.windowsazure.com) online o al portale di anteprima di Azure.
2.  Nel portale di Azure: **fare clic su nuovo**, quindi fare clic su **Servizi dati**, quindi fare clic su **Analisi di flusso** e **Creazione rapida**.

    ![Procedura guidata creazione rapida](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

    Nel portale di anteprima di Azure: fare clic su Nuovo, quindi fare clic su Dati e analisi e quindi fare clic su Analisi di flusso di Azure.

    ![Creazione processo portale di Azure](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)

3.  Specificare la configurazione desiderata per il processo di analisi di flusso.
	- Nella casella **Nome processo** immettere un nome per identificare il processo di analisi di flusso. Quando il **Nome processo**viene convalidato, appare un segno di spunta verde nella relativa casella. Il **Nome processo** può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 63 caratteri.
	- Utilizzare **Area** nel portale di Azure o **Posizione** nel portale di anteprima di Azure per specificare la posizione geografica in cui si desidera eseguire il processo.
	- Se si utilizza il portale di Azure, selezionare o creare un account di archiviazione da utilizzare come **Account di archiviazione di monitoraggio regionale**. Questo account di archiviazione viene utilizzato per archiviare i dati di monitoraggio per tutti i processi di Analisi del flusso in esecuzione all'interno dell'area.
	- Se si utilizza il portale di anteprima di Azure, specificare un **gruppo di risorse** nuovo o esistente per contenere le risorse correlate per l'applicazione.

4.  Dopo la configurazione delle nuove opzioni del processo di analisi di flusso, fare clic su **Crea processo di analisi di flusso**. La creazione del processo di analisi di flusso può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nell’hub delle notifiche.

    ![Hub Notifiche](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

    ![Creazione processo portale di anteprima di Azure](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)

5.  Il nuovo processo verrà visualizzato nell'elenco con uno stato **Creato**. Si noti che il pulsante**Avvia** è disabilitato. Prima di poter avviare il processo, è necessario configurare input, output, e la relativa query.

    ![Stato processo](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

    ![Stato del processo del portale di anteprima di Azure](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO1-->