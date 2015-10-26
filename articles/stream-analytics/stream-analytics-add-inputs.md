<properties 
	pageTitle="Aggiungere input | Microsoft Azure" 
	description="Aggiungere input a processi di analisi di flusso | segmento del percorso di apprendimento."
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


# Aggiungere input

I processi di analisi di flusso di Azure possono essere connessi a uno o più output, che definisce una connessione a una origine dati esistente. Quando i dati vengono inviati a tale origine dati, vengono utilizzati dal processo di analisi di flusso ed elaborati in tempo reale. L'analisi di flusso si integra perfettamente con gli [Hub eventi di Azure](http://azure.microsoft.com/services/event-hubs/) e l’[archivio BLOB di Azure](./storage/storage-dotnet-how-to-use-blobs.md)all'interno e all'esterno della sottoscrizione processo. Esistono due tipi di input nell’analisi di flusso: dati di riferimento e flussi di dati.

- **Flussi di dati**: I processi di analisi di flusso devono includere almeno un input del flusso dei dati che deve essere utilizzato e trasformato dal processo stesso. L'archivio BLOB di Azure e Hub eventi di Azure sono supportati come origini di input del flusso dei dati. Gli Hub eventi di Azure vengono usati per raccogliere flussi di eventi da più dispositivi, servizi e applicazioni connessi. Si può usare l'archivio BLOB di Azure come origine di input per l'inserimento di dati in blocco come flusso.  
- **Dati di riferimento**: l’analisi di flusso supporta un secondo tipo di input ausiliario denominato dati di riferimento. Al contrario dei dati in continua evoluzione, questi dati sono statici o cambiano molto lentamente. In genere vengono utilizzati per l'esecuzione di ricerche e correlazioni con flussi di dati per creare un set di dati più ampio. L'archivio BLOB di Azure attualmente è l'unica origine di input supportata per i dati di riferimento.  

Per aggiungere un input al processo di analisi di flusso:

1. Nel portale di Azure fare clic su **Input** e poi scegliere **Aggiungi un input** nel processo di analisi di flusso.

    ![Aggiungere input](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)

    Nel portale di anteprima di Azure fare clic sul riquadro **Input** nel processo di analisi di flusso.

    ![Aggiungere input al portale di anteprima di Azure](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)

2. Specificare il tipo di input: **Flusso di dati** o **Dati di riferimento**.

    ![Aggiungere dati](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)

    ![Aggiungere dati al portale di anteprima di Azure](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)

3. Se si crea un input del flusso di dati, specificare il tipo di origine per l'input. Questo passaggio può essere ignorato durante la creazione dei dati di riferimento, poiché al momento è supportato soltanto l’archivio BLOB.

    ![Aggiungere flusso di dati](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)

    ![Aggiungere dati al portale di anteprima del flusso di dati](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)

4. Fornire un nome descrittivo per l'input nella finestra di Alias di Input. Questo nome verrà utilizzare nella query del processo in un secondo momento per fare riferimento all'input.

    Compilare il resto delle proprietà di connessione necessarie per connettersi all'origine dati. Questi campi variano in base al tipo di origine e di input e vengono definiti in modo dettagliato [qui](stream-analytics-create-a-job.md).

    ![Aggiungere l'hub di eventi](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)

5. Specificare le impostazioni di serializzazione per i dati di input:
	- Per assicurarsi che le query funzionino nel modo previsto, specificare il **Formato di serializzazione eventi** dei dati in arrivo. I formati di serializzazione supportati sono JSON, CSV e Avro.
	- Verificare la **Codifica** per i dati. Al momento UTF-8 è l'unico formato di codifica supportato.

    ![Impostazioni della serializzazione dei dati](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)

    ![Impostazioni di serializzazione dei dati nel portale di anteprima di Azure](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)

6. Dopo aver completato la creazione dell’input, l’analisi di flusso verificherà che è possibile connettersi all'origine di input. È possibile visualizzare lo stato dell'operazione di connessione di prova nell'hub di notifica.

    ![Connessione di test](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)

    ![Test di connessione nel portale di anteprima di Azure](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO3-->