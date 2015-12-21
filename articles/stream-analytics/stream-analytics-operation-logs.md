<properties 
	pageTitle="Usare i log delle operazioni e dei servizi in Analisi di flusso | Microsoft Azure" 
	description="Come usare i log delle operazioni di Analisi di flusso" 
	keywords="log dei servizi"
	services="stream-analytics" 
	documentationCenter="" 
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

# Informazioni sui log delle operazioni e dei servizi di Analisi di flusso

Tutti i servizi di Azure forniscono messaggi di registrazione operativi agli utenti per registrare i dettagli relativi alle operazioni di gestione. Nell’analisi di flusso di Azure, queste informazioni possono essere utilizzate per operazioni relative al debug, quali la visualizzazione dello stato del processo, dell’avanzamento del processo, e dei messaggi di errore per rilevare l’avanzamento di un processo nel tempo, dall’avvio, all’eleborazione, fino all’output.

## Trovare i log delle operazioni nel portale di gestione di Azure

E’ possibile accedere ai log delle operazioni in due modi:

- Dashboard del processo di analisi di flusso  
- Servizi di gestione nel portale di Azure  

## Dashboard del processo di analisi di flusso

Un collegamento per i log di un processo di analisi di flusso corrispondenti viene visualizzato nella scheda Dashboard del processo. Se si fa clic su tale collegamento, si impostano i filtri in modo da visualizzare i log più recenti di tale processo specifico.

  ![Selezionare i log dei servizi di gestione](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)

## Servizi di gestione

Per passare manualmente ai log delle operazioni per l’analisi di flusso e altri servizi nel portale di Azure:

1.	Fare clic su **Servizi di gestione** nel [Portale di Azure](https://manage.windowsazure.com).
2.	Selezionare **Analisi di flusso** per **Tipo** e il nome del processo per **Nome servizio**.  

  ![Selezionare analisi di flusso](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)

## Trovare i log di controllo nel portale di anteprima di Azure ##

Per trovare i log operativi del processo di analisi di flusso nel portale di anteprima di Azure, fare clic su **Sfoglia** e quindi selezionare **Log di controllo**.

  ![Selezione analisi di flusso del portale di anteprima di Azure](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)

Verrà aperto un pannello che mostra gli eventi degli ultimi 7 giorni per tutte le risorse nella sottoscrizione. È possibile filtrare per visualizzare gli eventi di un tipo o di un intervallo di tempo specificato selezionando il comando **Filtra**.

  ![Selezione analisi di flusso del portale di anteprima di Azure](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)

## Ottenere i dettagli dei log

È possibile filtrare per stato e intervallo di tempo per visualizzare i log per il processo.

Nel portale di gestione di Azure fare clic sul pulsante **Dettagli** nella parte inferiore della finestra per visualizzare altri dettagli sull'evento selezionato.

  ![Selezionare i dettagli](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)

Nel portale di anteprima di Azure fare clic su una voce di log per visualizzare gli eventi dettagliati al suo interno.

  ![Selezione dettagli del portale di anteprima di Azure](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)

A questo punto è possibile aprire il pannello **Dettagli** facendo clic sull'evento.

  ![Selezione dettagli del portale di anteprima di Azure](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)

## Debug di un processo non riuscito

Nel portale di gestione di Azure, fare clic sull’icona Cerca e digitare "non riuscito". In questo modo si visualizzeranno tutti i log dei processi non riusciti.

  ![Eseguire il debug di un processo non riuscito](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)

Nel portale di anteprima di Azure è possibile filtrare per livello di messaggio per visualizzare gli eventi **Critici**.

  ![Debug del portale di anteprima di Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)

È possibile selezionare uno qualsiasi dei processi non riusciti e fare clic su **Dettagli** per ulteriori informazioni sull'errore. Alcuni messaggi di errore forniscono inoltre informazioni su come attenuare il problema.

  ![Dettagli operazione](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)

Nel caso in cui sia necessario contattare il [Supporto](http://azure.microsoft.com/support/options/) o fornire informazioni al team tramite il [forum MSDN](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics), tenere presente i dettagli dell'operazione, in particolare l’**ID di correlazione**.

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1210_2015-->