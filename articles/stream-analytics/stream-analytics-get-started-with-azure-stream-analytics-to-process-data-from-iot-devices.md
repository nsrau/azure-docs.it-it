---
title: Flussi di dati in tempo reale IoT e Analisi di flusso di Azure | Documentazione Microsoft
description: Tag dei sensori IoT e flussi di dati con l'elaborazione dei dati in tempo reale e l'analisi di flusso
keywords: soluzione IoT, introduzione a IoT
services: stream-analytics
documentationcenter: 
author: SnehaGunda
manager: jhubbard
editor: cgronlun
ms.assetid: 3e829055-75ed-469f-91f5-f0dc95046bdb
ms.service: stream-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sngun
ms.openlocfilehash: a4b2fda6c5cc5ea341618ec5fa8638a5c887bf84
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introduzione all'analisi di flusso di Azure per elaborare dati dai dispositivi IoT
Questa esercitazione illustra come creare la logica di elaborazione del flusso per raccogliere dati da dispositivi IoT (Internet delle cose). Verrà usato un caso d'uso reale di IoT per dimostrare come compilare una soluzione in modo rapido ed economico.

## <a name="prerequisites"></a>prerequisiti
* [Sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/)
* File di dati e query di esempio scaricabili da [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Scenario
Contoso, una società che opera nel settore dell'automazione industriale, ha completamente automatizzato il processo di produzione. I macchinari dello stabilimento hanno sensori in grado di emettere flussi di dati in tempo reale. In questo scenario, un responsabile del reparto produzione vuole avere informazioni in tempo reale dai dati del sensore per individuare modelli ricorrenti e intraprendere azioni correttive. Verrà usato il linguaggio di query di Analisi di flusso (SAQL) sui dati dei sensori per trovare modelli interessanti nel flusso dei dati in ingresso.

In questo caso, i dati vengono generati da un dispositivo SensorTag di Texas Instruments. Il payload dei dati è in formato JSON ed è simile al seguente:

    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

In uno scenario reale possono essere presenti centinaia di questi sensori che generano eventi come un flusso. Idealmente, un dispositivo gateway dovrebbe eseguire codice per effettuare il push degli eventi a [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) o [hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/). Il processo di analisi di flusso ha lo scopo di inserire questi eventi dagli hub eventi ed eseguire query di analisi in tempo reale a fronte dei flussi. È quindi possibile inviare i risultati a uno degli [output supportati](stream-analytics-define-outputs.md).

Per semplicità d'uso, questa guida introduttiva include un file di dati di esempio acquisito da dispositivi SensorTag reali. È possibile eseguire query sui dati di esempio e visualizzare i risultati. Nelle esercitazioni successive, verrà illustrato come connettere il processo agli input e agli output e distribuirli al servizio di Azure.

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.
1. Nel [portale di Azure](http://portal.azure.com), fare clic sul segno più e quindi digitare **STREAM ANALYTICS** nella finestra di testo a destra. Selezionare quindi **Stream Analytics job** nell'elenco dei risultati.
   
    ![Creare un nuovo processo di Analisi di flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)
2. Immettere un nome univoco per il processo e verificare che la sottoscrizione sia quella corretta per il processo. Creare quindi un nuovo gruppo di risorse o selezionarne uno esistente.
3. Selezionare una località per il processo. Per accelerare l'elaborazione e ridurre i costi del trasferimento dati, è consigliabile selezionare la stessa località del gruppo di risorse e dell'account di archiviazione previsto.
   
    ![Informazioni sulla creazione di un nuovo processo di Analisi di flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)
   
   > [!NOTE]
   > È consigliabile creare un solo account di archiviazione di questo tipo per ogni area. Questa risorsa di archiviazione verrà condivisa tra tutti i processi di Analisi di flusso creati in tale area.
   > 
   > 
4. Nel dashboard selezionare la casella in cui inserire il processo e quindi fare clic su **CREA**.
   
    ![creazione del processo in corso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)
5. Dovrebbe essere visualizzato un messaggio di tipo 'Distribuzione avviata...' nella parte superiore destra della finestra del browser. Viene quindi visualizzato il messaggio che indica il completamento dell'attività.
   
    ![creazione del processo in corso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

## <a name="create-an-azure-stream-analytics-query"></a>Creare una query di analisi di flusso di Azure
Dopo aver creato il processo, aprirlo ed eseguire una query. Per accedere al processo è sufficiente fare clic sul riquadro relativo.

![Riquadro del processo](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Nel riquadro **Job Topology** (Topologia processo) fare clic sulla casella **QUERY** per aprire l'editor di query. L'editor di **query** consente di inserire una query T-SQL che esegue la trasformazione dei dati di eventi in ingresso.

![Riquadro della query](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Query: Archiviazione dei dati non elaborati
La forma più semplice di query è una query pass-through che archivia tutti i dati di input nell'output designato. Scaricare il file di dati di esempio da [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) a un percorso nel proprio computer. 

1. Incollare la query dal file PassThrough.txt. 
   
    ![Test del flusso di input](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)
2. Fare clic sui tre puntini accanto all'input e selezionare la casella **Upload sample data from file** (Caricare i dati di esempio dal file).
   
    ![Test del flusso di input](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)
3. Nel riquadro che verrà visualizzato a destra, selezionare il file di dati HelloWorldASA-InputStream.json dalla posizione di download e fare clic su **OK** nella parte inferiore del riquadro.
   
    ![Test del flusso di input](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)
4. Per elaborare la query di test a fronte del set di dati di esempio, fare clic sull'ingranaggio **Test** nella parte superiore sinistra della finestra. Al completamento del processo, sotto alla query verrà visualizzata una finestra contenente i risultati dell'elaborazione.
   
    ![Risultati del test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Query: Filtro dei dati in base a una condizione
È possibile filtrare i risultati in base a una condizione. Ai fini di questa esercitazione, si vogliono visualizzare solo i risultati relativi agli eventi provenienti da "sensorA". La query si trova nel file Filtering.txt.

![Filtro di un flusso di dati](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Si noti che la query, con distinzione tra maiuscole e minuscole, confronta un valore di stringa. Fare di nuovo clic sull'icona a forma di ingranaggio **Test** per eseguire la query. La query restituirà 389 righe su 1860 eventi.

![Secondo risultato di output del test di query](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Query: Avviso per attivare un flusso di lavoro aziendale
La query verrà ora resa più dettagliata. Per ogni tipo di sensore, si vuole monitorare la temperatura media in una finestra di 30 secondi e visualizzare i risultati solo se tale temperatura supera i 100 gradi. A tale scopo, scrivere la query seguente e quindi fare clic su **Test** per visualizzare i risultati. La query si trova nel file ThresholdAlerting.txt.

![Query di filtro per 30 secondi](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

I risultati visualizzati conterranno ora solo 245 righe e i nomi dei sensori in cui temperatura media è superiore a 100. La query raggruppa il flusso di eventi in base al nome del sensore (**dspl**) su una **finestra a cascata** di 30 secondi. Le query temporali devono specificare come si vuole definire l'avanzamento del tempo. Usando la clausola **TIMESTAMP BY** è stata specificata la colonna **OUTPUTTIME** per associare l'ora a tutti i calcoli temporali. Per informazioni dettagliate, vedere gli articoli di MSDN relativi alla [gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e alle [funzioni finestra](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Query: rilevare l'assenza di eventi
La scrittura di una query per trovare una mancanza di eventi di input consente di determinare l'ultima volta in cui un sensore ha inviato dati e non ha quindi inviato alcun evento per i 5 secondi successivi. La query si trova nel file AbsenseOfEvent.txt.

![Rilevare l'assenza di eventi](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

In questo caso viene usato un **LEFT OUTER JOIN** per lo stesso flusso di dati (self-join). In caso di **INNER JOIN** viene restituito un risultato solo quando viene trovata una corrispondenza.  In caso di **LEFT OUTER JOIN**, se un evento del lato sinistro del join è senza corrispondenza viene restituita una riga con valore NULL per tutte le colonne del lato destro. Questa tecnica è molto utile per trovare un'assenza di eventi. Per altre informazioni sui [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx), vedere la documentazione MSDN.

## <a name="conclusion"></a>Conclusioni
Lo scopo di questa esercitazione è illustrare come scrivere diverse query nel linguaggio di query di Analisi di flusso e visualizzare i risultati nel browser. Si tratta, tuttavia, di informazioni di base, perché Analisi di flusso consente di eseguire molte altre attività. Analisi di flusso supporta un'ampia gamma di input e output e può anche usare le funzioni di Azure Machine Learning per offrire uno strumento efficace per l'analisi dei flussi di dati. Per iniziare a esplorare Analisi di flusso, è possibile usare la [mappa di apprendimento](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Per altre informazioni su come scrivere le query, vedere l'articolo sui [modelli di query comuni](stream-analytics-stream-analytics-query-patterns.md).

