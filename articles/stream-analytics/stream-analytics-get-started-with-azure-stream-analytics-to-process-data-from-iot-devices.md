<properties
	pageTitle="Introduzione all'analisi di flusso di Azure per elaborare dati dai dispositivi IoT | Analisi di flusso"
	description="Tag dei sensori IoT e flussi di dati con l'elaborazione dei dati in tempo reale e l'analisi di flusso"
    keywords="soluzione IoT, introduzione a IoT"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/11/2016"
	ms.author="jeffstok"
/>  

# Introduzione all'analisi di flusso di Azure per elaborare dati dai dispositivi IoT

In questa esercitazione si apprenderà a creare la logica di elaborazione del flusso per raccogliere dati dai dispositivi Internet delle cose (IoT). Verrà illustrato un caso d'uso reale di Internet delle cose (IoT) per dimostrare come compilare la soluzione in modo rapido ed economico.

## Prerequisiti

-   [Sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/)
-   File di dati e query di esempio scaricabili da [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## Scenario

Contoso è una società che opera nel settore dell'automazione industriale e che ha completamente automatizzato il processo di produzione. I macchinari in questo stabilimento hanno sensori in grado di emettere flussi di dati in tempo reale. In questo scenario, un responsabile del reparto produzione vuole avere informazioni in tempo reale dai dati del sensore per individuare modelli ricorrenti e intraprendere azioni correttive. Verrà usato il linguaggio di query di analisi di flusso (SAQL) sui dati del sensore per trovare modelli interessanti sul flusso dei dati in ingresso.

In questo caso, i dati vengono generati da un dispositivo Texas Instrument Sensor Tag.

![Texas Instruments Sensor Tag](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)  

Il payload dei dati è in formato JSON ed è simile al seguente:

    
	{
    	"time": "2016-01-26T20:47:53.0000000",  
	    "dspl": "sensorE",  
    	"temp": 123,  
	    "hmdt": 34  
	}  
    
In uno scenario reale possono essere presenti centinaia di questi sensori che generano eventi come un flusso. Idealmente è presente anche un dispositivo gateway che esegue codice per il push degli eventi a [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) o [Hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/). Il processo di analisi di flusso ha lo scopo di inserire questi eventi dagli hub eventi ed eseguire query di analisi in tempo reale a fronte dei flussi. È quindi possibile inviare i risultati a uno degli [output supportati](stream-analytics-define-outputs.md).

Ai fini della semplicità d'uso, in questa guida introduttiva viene fornito un file di dati di esempio, acquisito da dispositivi SensorTag reali, su cui è possibile eseguire diverse query e visualizzarne i risultati. Nelle esercitazioni successive, verrà illustrato come connettere il processo agli input e agli output e distribuirli al servizio di Azure.

## Creare un processo di Analisi di flusso

Nel [portale di Azure](http://manage.windowsazure.com) aprire Analisi di flusso e fare clic su **"Nuovo"** nell'angolo in basso a sinistra della pagina per creare un nuovo processo di analisi.

![Creare un nuovo processo di Analisi di flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

Fare clic su "**Creazione rapida**".

Per l'impostazione **"Account di archiviazione di monitoraggio regionale"** selezionare **"Crea un nuovo account di archiviazione"** e assegnare un nome univoco qualsiasi. Analisi di flusso di Azure usa questo account per archiviare informazioni di monitoraggio per tutti i processi futuri.

> [AZURE.NOTE] È necessario creare l'account di archiviazione solo una volta per ogni area. In questo modo, l'archiviazione verrà condivisa tra tutti i processi di analisi di flusso creati in tale area.

Fare clic su "**Crea processo di Analisi di flusso**" in fondo alla pagina.

![Configurazione dell'account di archiviazione](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)  

## Query di Analisi di flusso di Azure

Fare clic sulla scheda Query per passare all'editor di query. La scheda Query contiene una query T-SQL che esegue la trasformazione dei dati eventi in ingresso.

## Archiviazione dei dati non elaborati

Il modo più semplice di eseguire query è avviare un pass-through che archivierà tutti i dati di input nell'output designato.

![Query sul processo di archiviazione](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)  

Scaricare il file di dati di esempio da [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) in un percorso nel computer locale. Copiare e incollare la query dal file **PassThrough.txt**. Fare clic sul pulsante Test seguente e selezionare il file di dati denominato **HelloWorldASA InputStream.json** nel percorso di download.

![Pulsante Test in Analisi di flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)  

![Test del flusso di input](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)  

È possibile visualizzare i risultati della query nel browser, come illustrato di seguito.

![Risultati del test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)  

## Filtro dei dati in base a una condizione

È possibile filtrare i risultati in base a una condizione. Ai fini di questa esercitazione, si desidera mostrare solo i risultati relativi agli eventi provenienti da "SensorA". La query si trova nel file **Filtering.txt**.

![Filtro di un flusso di dati](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)  

Si noti che in questo caso si sta confrontando un valore stringa che fa distinzione tra maiuscole e minuscole. Fare clic sul pulsante **Riesegui** per eseguire la query. La query deve restituire solo 389 righe di 1860 eventi.

![Secondo risultato di output del test di query](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)  

## Avvisi per attivare il flusso di lavoro aziendale

A questo punto è possibile rendere la query più dettagliata. Per ogni tipo di sensore, se si vuole monitorare la temperatura media in una finestra di 30 secondi e visualizzare i risultati solo tale temperatura supera i 100 gradi, si scriverà la query seguente e quindi si farà clic su **Riesegui** per visualizzare i risultati. La query si trova nel file **ThresholdAlerting.txt**.

![Query di filtro per 30 secondi](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)  

Si vedrà che ora i risultati contengono solo 245 righe con l'elenco dei sensori la cui temperatura media è maggiore di 100. In questa query è stato raggruppato il flusso di eventi da **dspl**, ovvero il nome del sensore, e in base a una **finestra a cascata** di 30 secondi. Quando si eseguono tali query temporali, è fondamentale specificare come si vuole indicare l'avanzamento del tempo. Tramite la clausola **TIMESTAMP BY** viene specificata la colonna "time" come modo per indicare l'avanzamento del tempo per tutti i calcoli temporali. Per informazioni dettagliate, vedere gli argomenti di MSDN su [gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [funzioni finestra](https://msdn.microsoft.com/library/azure/dn835019.aspx).

![Temperatura oltre 100](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)  

## Rilevare l'assenza di eventi

La scrittura di una query per trovare una mancanza di eventi di input è un'operazione piuttosto semplice. È possibile trovare l'ultima volta in cui un sensore ha inviato dati e poi non ha inviato alcun evento per il minuto successivo. La query si trova nel file **AbsenseOfEvent.txt**.

![Rilevare l'assenza di eventi](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)  

Qui viene usato un **LEFT OUTER JOIN** sullo stesso flusso di dati (self-join). Per un inner join, viene restituito un risultato solo quando viene trovata una corrispondenza. Invece, per un **LEFT OUTER JOIN**, se un evento dal lato sinistro del join è senza corrispondenza, viene restituita una riga con valore NULL per tutte le colonne della riga destra. Questa tecnica è molto utile per trovare un'assenza di eventi. Per altre informazioni , vedere la documentazione MSDN su [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx).

![risultati del join](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)  

## Conclusioni

Lo scopo di questa esercitazione è illustrare come scrivere diverse query nel linguaggio di query di Analisi di flusso e visualizzare i risultati nel browser. Si tratta, tuttavia, di informazioni di base, in quanto con analisi di flusso è possibile eseguire molte altre attività. Analisi di flusso supporta un'ampia gamma di input e output e può anche sfruttare le funzioni di Azure Machine Learning, ciò che lo rende uno strumento efficace per l'analisi dei flussi di dati. Per iniziare a esplorare Analisi di flusso, è possibile usare la [mappa di apprendimento](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/), mentre per altre informazioni sulla scrittura di query, vedere l'articolo sui [modelli di query comuni](./stream-analytics-stream-analytics-query-patterns.md).

<!---HONumber=AcomDC_0817_2016-->