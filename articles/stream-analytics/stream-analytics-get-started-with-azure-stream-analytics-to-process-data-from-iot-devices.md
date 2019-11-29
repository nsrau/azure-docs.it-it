---
title: Elaborare flussi di dati in tempo reale con analisi di flusso di Azure
description: Tag dei sensori IoT e flussi di dati con l'elaborazione dei dati in tempo reale e l'analisi di flusso
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 1cc9c6dbb700664e732a67245563e9a211456767
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559797"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Elaborare flussi di dati in tempo reale con analisi di flusso di Azure

Questo articolo illustra come creare la logica di elaborazione del flusso per raccogliere i dati dai dispositivi Internet delle cose. Si usa un caso d'uso reale del Internet delle cose per dimostrare come compilare la soluzione in modo rapido ed economico.

## <a name="prerequisites"></a>Prerequisiti

* Creare una sottoscrizione gratuita di [Azure](https://azure.microsoft.com/pricing/free-trial/).
* Scaricare i file di dati e query di esempio da [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scenario

Contoso, una società che opera nel settore dell'automazione industriale, ha completamente automatizzato il processo di produzione. I macchinari dello stabilimento hanno sensori in grado di emettere flussi di dati in tempo reale. In questo scenario, un responsabile del reparto produzione vuole avere informazioni in tempo reale dai dati del sensore per individuare modelli ricorrenti e intraprendere azioni correttive. È possibile usare il linguaggio di query di analisi di flusso (SAQL) sui dati dei sensori per trovare modelli interessanti dal flusso di dati in ingresso.

In questo esempio i dati vengono generati da un dispositivo di tag del sensore Texas Instruments. Il payload dei dati è in formato JSON ed è simile al seguente:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

In uno scenario reale possono essere presenti centinaia di questi sensori che generano eventi come un flusso. Idealmente, un dispositivo gateway dovrebbe eseguire codice per effettuare il push degli eventi a [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) o [hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/). Il processo di analisi di flusso ha lo scopo di inserire questi eventi dagli hub eventi ed eseguire query di analisi in tempo reale a fronte dei flussi. È quindi possibile inviare i risultati a uno degli [output supportati](stream-analytics-define-outputs.md).

Per semplicità d'uso, questa guida introduttiva include un file di dati di esempio acquisito da dispositivi SensorTag reali. È possibile eseguire query sui dati di esempio e visualizzare i risultati. Nelle esercitazioni successive, verrà illustrato come connettere il processo agli input e agli output e distribuirli al servizio di Azure.

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nella [portale di Azure](https://portal.azure.com)selezionare **+ Crea una risorsa** dal menu di spostamento a sinistra. Quindi selezionare **processo di analisi di flusso** da **Analytics**.
   
    ![Creare un nuovo processo di Analisi di flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Immettere un nome univoco per il processo e verificare che la sottoscrizione sia quella corretta per il processo. Creare un nuovo gruppo di risorse o selezionarne uno esistente dalla sottoscrizione.

1. Selezionare una località per il processo. Usare la stessa località per il gruppo di risorse e tutte le risorse per aumentare la velocità di elaborazione e ridurre i costi. Dopo aver apportato le configurazioni, selezionare **Crea**.
   
    ![Informazioni sulla creazione di un nuovo processo di Analisi di flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Creare una query di analisi di flusso di Azure
Il passaggio successivo dopo la creazione del processo consiste nel scrivere una query. È possibile testare le query sui dati di esempio senza collegare un input o un output al processo.

Scaricare [HelloWorldASA-InputStream. JSON](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) da GitHub. Quindi, passare al processo di analisi di flusso di Azure nel portale di Azure.

Selezionare **query** in **topologia processo** dal menu a sinistra. Selezionare quindi **carica input di esempio**. Caricare il file di `HelloWorldASA-InputStream.json` e selezionare **OK**.

![Riquadro query dashboard di Analisi di flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Si noti che un'anteprima dei dati viene popolata automaticamente nella tabella di **Anteprima di input** .

![Anteprima dei dati di input di esempio](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Query: Archiviazione dei dati non elaborati

La forma più semplice di query è una query pass-through che archivia tutti i dati di input nell'output designato. Questa query è la query predefinita popolata in un nuovo processo di analisi di flusso di Azure.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Selezionare **query di test** e visualizzare i risultati nella tabella **risultati test** .

![Risultati del test dell'operazione per la query di Analisi di flusso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Query: Filtro dei dati in base a una condizione

Si proverà a filtrare i risultati in base a una condizione. Ai fini di questa esercitazione, si vogliono visualizzare solo i risultati relativi agli eventi provenienti da "sensorA".

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Incollare la query nell'editor e selezionare **Test query** per esaminare i risultati.

![Filtro di un flusso di dati](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Query: Avviso per attivare un flusso di lavoro aziendale

La query verrà ora resa più dettagliata. Per ogni tipo di sensore, si vuole monitorare la temperatura media in una finestra di 30 secondi e visualizzare i risultati solo se tale temperatura supera i 100 gradi.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![Query di filtro per 30 secondi](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Verranno visualizzati i risultati che contengono solo 245 righe e i nomi dei sensori in cui la media moderata è maggiore di 100. La query raggruppa il flusso di eventi in base al nome del sensore (**dspl**) su una **finestra a cascata** di 30 secondi. Le query temporali devono indicare come si desidera eseguire il tempo. Utilizzando la clausola **timestamp by** , è stata specificata la colonna **OUTPUTTIME** per associare i tempi a tutti i calcoli temporali. Per informazioni dettagliate, vedere la pagina relativa alle [funzioni](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics)di [gestione del tempo](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) e finestra.

### <a name="query-detect-absence-of-events"></a>Query: rilevare l'assenza di eventi

La scrittura di una query per trovare una mancanza di eventi di input Si noterà l'ultima volta in cui un sensore ha inviato dati e non ha inviato gli eventi per i 5 secondi successivi.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Rilevare l'assenza di eventi](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

In questo caso viene usato un **LEFT OUTER JOIN** per lo stesso flusso di dati (self-join). In caso di **INNER JOIN** viene restituito un risultato solo quando viene trovata una corrispondenza.  In caso di **LEFT OUTER JOIN**, se un evento del lato sinistro del join è senza corrispondenza viene restituita una riga con valore NULL per tutte le colonne del lato destro. Questa tecnica è molto utile per trovare un'assenza di eventi. Per ulteriori informazioni, vedere [join](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Conclusione

Lo scopo di questo articolo è illustrare come scrivere query del linguaggio di query di analisi di flusso diverse e visualizzare i risultati nel browser. Tuttavia, questo è solo per iniziare. Analisi di flusso supporta un'ampia gamma di input e output e può anche usare le funzioni di Azure Machine Learning per offrire uno strumento efficace per l'analisi dei flussi di dati. Per altre informazioni su come scrivere le query, vedere l'articolo sui [modelli di query comuni](stream-analytics-stream-analytics-query-patterns.md).

