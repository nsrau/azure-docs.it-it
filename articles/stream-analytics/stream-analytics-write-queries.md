<properties 
	pageTitle="Come scrivere query in Analisi di flusso | Microsoft Azure" 
	description="Scrivere query in Analisi di flusso ed eseguire query sui dati | segmento del percorso di apprendimento."
	keywords="come scrivere query, eseguire query sui dati, scrivere una query, scrittura di query"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>  

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>

# Come scrivere query in Analisi di flusso

La scrittura di query per la logica di elaborazione del flusso in Analisi di flusso di Azure viene implementata come una "query in esecuzione" definita prima che un processo venga avviato ed eseguita sui dati nel momento in cui raggiunge il processo. La trasformazione dei dati è espressa in un linguaggio di query di tipo SQL, che è sostanzialmente un sottoinsieme di T-SQL con alcune estensioni di linguaggio aggiunte come [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) usato per esprimere la semantica temporale.

## Scrittura di query: ##

1. Nel processo di Analisi di flusso nel portale di gestione di Azure, fare clic su **Query**.

    ![Query di selezione](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    Nel portale di Azure fare clic su **Query**.

    ![Selezionare l'anteprima della query](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.	I nuovi processi dispongono di un modello di query per rendere più semplice l’inizio. Il modello di query esegue una query "pass-through" che proietta tutti i campi dagli eventi di input nell'output.

    - Se sono stati definiti almeno un input e un output per il processo, è possibile sostituire i campi segnaposto "[YourOutputAlias]" e "[YourInputAlias]" con gli alias dell’input e dell’output che si desidera utilizzare per primi. È inoltre possibile creare e testare la query nel portale di Azure classico senza la definizione di input e output del processo.
    - Se si desidera eseguire un'elaborazione più complessa rispetto a un semplice pass-through, è possibile modificare la definizione della query. Per iniziare a usare la creazione di query, esaminiamo alcuni modelli di query comuni [qui](stream-analytics-stream-analytics-query-patterns.md).
  
    ![Eseguire query sui dati, finestra](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)

## Per verificare se le query sui dati funzionano: ##

È possibile verificare che la query funzioni come previsto eseguendola nel browser su uno o più file JSON locali contenenti i dati di test. Il test non avvia il processo e non ha implicazioni relative alla fatturazione.

> [AZURE.NOTE] Attualmente il test delle query nel browser non è supportato nel portale di Azure.

1.	Assicurarsi che non siano presenti errori nella query (in caso contrario il pulsante Test verrà disabilitato) e quindi fare clic sul pulsante Test.

    ![Eseguire query sui dati, test](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)

2.	Verrà chiesto di specificare i file per ognuno degli input a cui si fa riferimento nella query. In questo esempio, la query del modello viene lasciata come è, pertanto è richiesto l’input denominato "yourinputalias".

    ![Verificare query sui dati](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)

3.	Passare a un file di test. Diversi file di esempio sono disponibili su [github] (https://github.com/Azure/azure-stream-analytics/tree/master/Sample dati) ed è anche possibile recuperare dati di esempio dai propri input del flusso di dati tramite la funzione Dati di esempio nella scheda Input.

    ![Input della query](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)

4.	Dopo aver chiuso la finestra di dialogo, verrà eseguita la query sui dati di test e i risultati saranno visualizzati nella parte inferiore della pagina Query.

    ![Riepilogo della query](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)

## Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->