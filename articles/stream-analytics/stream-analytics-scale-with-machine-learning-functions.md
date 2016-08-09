<properties
	pageTitle="Ridimensionare il processo di Analisi di flusso con funzioni di Azure Machine Learning | Microsoft Azure"
	description="Informazioni su come ridimensionare correttamente i processi di Analisi di flusso, con il partizionamento, le unità di streaming e altro ancora, quando si usano funzioni di Azure Machine Learning."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"
/>

# Ridimensionare il processo di Analisi di flusso con funzioni di Azure Machine Learning

Spesso è piuttosto semplice impostare un processo di Analisi di flusso e usarlo per analizzare alcuni dati di esempio. Cosa fare quando è necessario eseguire lo stesso processo con volume di dati più elevato? Bisogna capire come configurare il processo di Analisi di flusso per il ridimensionamento. Questo documento illustra in particolare gli aspetti specifici del ridimensionamento di processi di Analisi di flusso con funzioni di Machine Learning. Per informazioni su come ridimensionare processi di Analisi di flusso in generale, vedere l'articolo relativo al [ridimensionamento dei processi](stream-analytics-scale-jobs.md).

## Che cos'è una funzione di Azure Machine Learning in Analisi di flusso?

Una funzione di Machine Learning in Analisi di flusso può essere usata come una normale chiamata di funzione nel linguaggio di query di Analisi di flusso. Tuttavia, le chiamate di funzione sono in realtà richieste del servizio Web Azure Machine Learning. I servizi Web Machine Learning supportano l'invio in batch di più righe, dette mini-batch, nella stessa chiamata all'API del servizio Web per migliorare la velocità effettiva globale. Per altri dettagli, vedere gli articoli relativi alle [funzioni di Azure Machine Learning in Analisi di flusso](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) e ai [servizi Web Azure Machine Learning](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## Configurare un processo di Analisi di flusso con funzioni di Machine Learning

Quando si configura una funzione di Machine Learning per un processo di Analisi di flusso è necessario prendere in considerazione due parametri, le dimensioni batch delle chiamate di funzione di Machine Learning e le unità di streaming (SU) di cui viene effettuato il provisioning per il processo di Analisi di flusso. Per determinare i relativi valori appropriati, occorre prima scegliere tra latenza e velocità effettiva, vale a dire la latenza del processo di Analisi di flusso e la velocità effettiva di ogni unità di streaming. È sempre possibile aggiungere unità di streaming a un processo per aumentare la velocità effettiva di una query di Analisi di flusso con partizionamento efficiente, anche se le unità di streaming aumentano il costo di esecuzione del processo.

È quindi importante determinare la *tolleranza* della latenza nell'esecuzione di un processo di Analisi di flusso. La latenza aggiuntiva dovuta all'esecuzione di richieste del servizio Azure Machine Learning aumenta naturalmente con le dimensioni batch, che si sommano alla latenza del processo di Analisi di flusso. D'altra parte, l'aumento delle dimensioni batch consente al processo di analisi di flusso di elaborare *più eventi con lo stesso numero* * di richieste del servizio Web Machine Learning. L'aumento della latenza del servizio Web Machine Learning è spesso sublineare all'aumento delle dimensioni. È quindi importante stabilire le dimensioni batch più convenienti per un servizio Web Machine Learning in una determinata situazione. Le dimensioni batch predefinite per le richieste al servizio Web sono pari a 1000 e possono essere modificate usando l'[API REST di Analisi di flusso](https://msdn.microsoft.com/library/mt653706.aspx "API REST di Analisi di flusso") o il [client di PowerShell per Analisi di flusso](stream-analytics-monitor-and-manage-jobs-use-powershell.md "Client di PowerShell per Analisi di flusso").

Dopo aver determinato le dimensioni batch, è possibile determinare la quantità di unità di streaming (SU) in base al numero di eventi che la funzione deve elaborare al secondo. Per altre informazioni sulle unità di streaming, vedere l'articolo relativo al [ridimensionamento dei processi di Analisi di flusso](stream-analytics-scale-jobs.md#configuring-streaming-units).

In generale, sono presenti 20 connessioni simultanee al servizio Web Machine Learning ogni 6 unità di streaming, ma anche i processi per 1 unità di streaming e per 3 unità di streaming ricevono 20 connessioni simultanee. Ad esempio, se la velocità dei dati di input è di 200.000 eventi al secondo e le dimensioni batch hanno il valore predefinito di 1000, la latenza del servizio Web risultante con un mini-batch di 1000 eventi è di 200 ms. Ciò significa che ogni connessione può inviare 5 richieste al servizio Web Machine Learning in un secondo. Con 20 connessioni, il processo di Analisi di flusso può elaborare 20.000 eventi in 200 ms, ovvero 100.000 eventi al secondo. Quindi, per elaborare 200.000 eventi al secondo, il processo di Analisi di flusso necessita di 40 connessioni simultanee, pari a 12 unità di streaming. Il diagramma seguente illustra le richieste dal processo di Analisi di flusso all'endpoint di servizio Web Machine Learning. Ogni 6 unità di streaming sono presenti al massimo 20 connessioni simultanee al servizio Web Machine Learning.

![Esempio di processo per ridimensionare Analisi di flusso con funzioni di Machine Learning 2](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Esempio di processo per ridimensionare Analisi di flusso con funzioni di Machine Learning 2")

In generale, posto che ***B*** sta per dimensioni batch e ***L*** sta per latenza del servizio Web per le dimensioni batch B in millisecondi, la velocità effettiva di un processo di Analisi di flusso con ***N*** unità di streaming è:

![Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning")

Prendendo in considerazione anche il numero massimo di chiamate simultanee sul lato del servizio Web Machine Learning, è consigliabile impostare il valore massimo, che attualmente è 200.

Per altre informazioni su questa impostazione, vedere l'articolo relativo al [ridimensionamento di servizi Web Machine Learning](../machine-learning/machine-learning-scaling-webservice.md).

## Esempio: Analisi di valutazione

L'esempio seguente include un processo di Analisi di flusso con la funzione di Machine Learning di analisi di valutazione, come descritto nell'[esercitazione sull'integrazione tra Analisi di flusso e Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

La query è una semplice query completamente partizionata seguita dalla funzione di **valutazione**, come illustrato di seguito:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )
    
    Select text, result.[Score]
    Into output
    From subquery

Si consideri uno scenario in cui, con una velocità effettiva di 10.000 tweet al secondo è necessario creare un processo di Analisi di flusso per eseguire l'analisi di valutazione dei tweet, o eventi. Usando 1 unità di streaming, questo processo di Analisi di flusso può riuscire a gestire il traffico? Mantenendo le dimensioni batch predefinite, pari a 1000, il processo deve riuscire a gestire l'input. La funzione di Machine Learning aggiuntiva deve anche generare meno di un secondo di latenza, ovvero la latenza generale predefinita del servizio Web Machine Learning di analisi di valutazione, con dimensioni batch predefinite pari a 1000. La latenza **totale** o end-to-end del processo di Analisi di flusso sarebbe normalmente di pochi secondi. Esaminare ora più da vicino questo processo di Analisi di flusso, *in particolare* le chiamate di funzione di Machine Learning. Con dimensioni batch pari a 1000, una velocità effettiva di 10.000 eventi richiederà circa 10 richieste al servizio Web. Anche con 1 unità di streaming, il numero di connessioni simultanee è sufficiente per gestire il traffico di input.

Ma cosa accade se la frequenza degli eventi di input aumenta di 100 volte e il processo di Analisi di flusso deve elaborare 1.000.000 di tweet al secondo? Sono disponibili due opzioni:

1.  Aumentare le dimensioni batch.
2.  Partizionare il flusso di input per elaborare gli eventi in parallelo.

Con la prima opzione aumenta la **latenza** del processo.

Con la seconda opzione è necessario effettuare il provisioning di altre unità di streaming e quindi generare un numero maggiore di richieste simultanee al servizio Web Machine Learning. Ciò significa che aumenta il **costo** del processo.


Si supponga che la latenza del servizio Web Machine Learning di analisi di valutazione sia pari a 200 ms per batch di 1000 eventi o meno, 250 ms per batch di 5.000 eventi, 300 ms per batch di 10.000 eventi o 500 ms per batch di 25.000 eventi.

1. Usando la prima opzione, **non** effettuando quindi il provisioning di altre unità di streaming, sarebbe possibile aumentare le dimensioni batch a **25.000**. Questo consentirebbe al processo di elaborare 1.000.000 di eventi con 20 connessioni simultanee al servizio Web Machine Learning, con una latenza di 500 ms per ogni chiamata. La latenza aggiuntiva del processo di Analisi di flusso dovuta alle richieste della funzione di valutazione rispetto alle richieste del servizio Web Machine Learning aumenterebbe da **200 ms** a **500 ms**. Si noti tuttavia che **non è possibile** aumentare le dimensioni batch all'infinito, dato che i servizi Web Machine Learning richiedono dimensioni del payload della richiesta pari a 4 MB. Le richieste al servizio Web di dimensioni inferiori scadono dopo 100 secondi di attività.
2. Usando la seconda opzione le dimensioni batch rimangono pari a 1000. Con una latenza del servizio Web di 200 ms, ogni 20 connessioni simultanee al servizio Web sarebbe possibile elaborare 1000 * 20 * 5 = 100.000 eventi al secondo. Per elaborare 1.000.000 di eventi al secondo, quindi, il processo richiederebbe 60 unità di streaming. Rispetto alla prima opzione, il processo di Analisi di flusso invierebbe più richieste batch al servizio Web, generando così un costo maggiore.

Di seguito è riportata una tabella della velocità effettiva del processo di Analisi di flusso per diverse unità di streaming e dimensioni batch, in numero di eventi al secondo.

| Unità di streaming (SU) | | | | Dimensioni batch (latenza ML) | |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| | | | | | |
| | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 ms) | 10\.000 (300 ms) | 25\.000 (500 ms) |
| 1 SU | 2\.500 | 5\.000 | 20\.000 | 30\.000 | 50\.000 |
| 3 SU | 2\.500 | 5\.000 | 20\.000 | 30\.000 | 50\.000 |
| 6 SU | 2\.500 | 5\.000 | 20\.000 | 30\.000 | 50\.000 |
| 12 SU | 5\.000 | 10\.000 | 40\.000 | 60\.000 | 100\.000 |
| 18 SU | 7\.500 | 15\.000 | 60\.000 | 90\.000 | 150\.000 |
| 24 SU | 10\.000 | 20\.000 | 80\.000 | 120\.000 | 200\.000 |
| … | … | … | … | … | … |
| 60 SU | 25\.000 | 50\.000 | 200\.000 | 300\.000 | 500\.000 |

A questo punto dovrebbe essere chiaro il funzionamento delle funzioni di Machine Learning in Analisi di flusso. I processi di Analisi di flusso eseguono il pull dei dati dalle origini dati e ogni pull restituisce un batch di eventi al processo di Analisi di flusso per l'elaborazione. Come influisce tale modello pull sulle richieste al servizio Web Machine Learning?

In genere, le dimensioni batch impostate per le funzioni di Machine Learning non sono esattamente divisibili per il numero di eventi restituiti da ogni pull del processo di Analisi di flusso. Quando ciò accade, il servizio web Machine Learning viene chiamato con batch parziali. Questo per non causare un sovraccarico della latenza dei processi nell'unione di eventi tra un pull e l'altro.

## Nuove metriche di monitoraggio correlate alle funzioni

Nell'area di monitoraggio di un processo di Analisi di flusso sono state aggiunte altre tre metriche relative alle funzioni. Le metriche sono FUNCTION REQUESTS, FUNCTION EVENTS e FAILED FUNCTION REQUESTS e sono illustrate nella figura seguente.

![Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning")

Di seguito sono riportate le rispettive definizioni.

**FUNCTION REQUESTS**: numero di richieste di funzione.

**FUNCTION EVENTS**: numero di eventi nelle richieste di funzione.

**FAILED FUNCTION REQUESTS**: numero di richieste di funzione non riuscite.

## Risultati principali  

Per riepilogare i punti principali, per ridimensionare un processo di Analisi di flusso con funzioni di Machine Learning è necessario prendere in considerazione gli elementi seguenti:

1.  Frequenza degli eventi di input.
2.  Latenza consentita per il processo di Analisi di flusso in esecuzione e dimensioni batch delle richieste al servizio Web Machine Learning.
3.  Unità di streaming di Analisi di flusso di cui è stato effettuato il provisioning e numero di richieste al servizio Web Machine Learning, oltre ai costi aggiuntivi correlati alle funzioni.

È stata usata come esempio una query di Analisi di flusso completamente partizionata. Se è necessaria una query più complessa, è possibile usare il [forum di analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics) per ottenere altre informazioni dal team di analisi di flusso.

## Passaggi successivi

Per altre informazioni su Analisi di flusso, vedere:

- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0727_2016-->