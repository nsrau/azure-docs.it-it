---
title: Avvio rapido per la libreria client di Rilevamento anomalie per Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: c06cb7143756eed3d50fe1d2d03ce7ba8d6d9d4d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371647"
---
Questo argomento costituisce un'introduzione alla libreria client di Rilevamento anomalie per Python. Seguire questi passaggi per installare il pacchetto e iniziare a usare gli algoritmi forniti dal servizio. Il servizio Rilevamento anomalie consente di trovare le anomalie nei dati delle serie temporali applicando automaticamente i modelli di mapping più appropriati, indipendentemente dal settore, dallo scenario o dal volume di dati.

Usare la libreria client di Rilevamento anomalie per Python per:

* Rilevare anomalie nell'intero set di dati della serie temporale come richiesta batch
* Rilevare lo stato di anomalia del punto dati più recente nella serie temporale
* Rilevare i punti di modifica di una tendenza nel set di dati.

[Documentazione di riferimento della libreria](https://go.microsoft.com/fwlink/?linkid=2090370) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Pacchetto (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [Trovare il codice di esempio in GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.x](https://www.python.org/)
* [Llibreria di analisi dei dati Pandas](https://pandas.pydata.org/)
* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="creare una risorsa Rilevamento anomalie"  target="_blank">creare una risorsa Rilevamento anomalie <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Rilevamento anomalie. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.


## <a name="setting-up"></a>Configurazione

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

 Creare un nuovo file Python e importare le librerie seguenti.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Creare variabili per la chiave sotto forma di variabile di ambiente, percorso di un file di dati della serie temporale e località di Azure della sottoscrizione. Ad esempio: `westus2`.

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Modello a oggetti

Il client di Rilevamento anomalie è un oggetto [AnomalyDetectorClient](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) che esegue l'autenticazione ad Azure tramite la chiave dell'utente. Il client può eseguire il rilevamento anomalie in un intero set di dati usando [entire_detect()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) oppure nel punto dati più recente usando [Last_detect()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). La funzione [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090370) rileva i punti che contrassegnano le modifiche in una tendenza.

I dati delle serie temporali vengono inviati come serie di oggetti [Point](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) in un oggetto [Request](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python). L'oggetto `Request` contiene le proprietà per la descrizione dei dati (ad esempio, [Granularity](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)) e i parametri per il rilevamento delle anomalie.

La risposta del servizio Rilevamento anomalie è un oggetto [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python), [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) o [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370), a seconda del metodo usato.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Rilevamento anomalie per Python:

* [Autenticare il client](#authenticate-the-client)
* [Caricare il set di dati di una serie temporale da un file](#load-time-series-data-from-a-file)
* [Rilevare le anomalie nell'intero set di dati](#detect-anomalies-in-the-entire-data-set)
* [Rilevare lo stato di anomalia del punto dati più recente](#detect-the-anomaly-status-of-the-latest-data-point)
* [Rilevare i punti di modifica nel set di dati](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere la variabile relativa alla località di Azure all'endpoint e autenticare il client con la chiave.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Caricare i dati di una serie temporale da un file

Scaricare i dati di esempio per questo avvio rapido da [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Nel browser fare clic con il pulsante destro del mouse su **Raw** (Non elaborato).
2. Fare clic su **Salva link con nome**.
3. Salvare il file nella directory dell'applicazione come file con estensione csv.

I dati della serie temporale vengono formattati come file con estensione csv e vengono inviati all'API Rilevamento anomalie.

Caricare il file di dati con il metodo `read_csv()` della libreria Pandas e creare una variabile di elenco vuoto in cui archiviare la serie di dati. Scorrere il file e accodare i dati come oggetto [Point](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python). Questo oggetto contiene il timestamp e il valore numerico delle righe del file di dati CSV.

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Creare un oggetto [Request](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) con la serie temporale e la [granularità](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (o periodicità) dei relativi punti dati. Ad esempio: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Rilevare le anomalie nell'intero set di dati

Chiamare l'API per rilevare le anomalie nei dati dell'intera serie temporale usando il metodo [entire_detect()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) del client. Archiviare l'oggetto [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) restituito. Scorrere l'elenco `is_anomaly` della risposta e visualizzare l'indice dei valori `true`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

Chiamare l'API Rilevamento anomalie per determinare se il punto dati più recente è un'anomalia usando il metodo [last_detect()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) del client, quindi archiviare l'oggetto [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) restituito. Il valore `is_anomaly` della risposta è un valore booleano che specifica lo stato di anomalia del punto.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Rilevare i punti di modifica nel set di dati

Chiamare l'API per rilevare i punti di modifica nella serie temporale con il metodo [detect_change_point()](https://go.microsoft.com/fwlink/?linkid=2090370) del client. Archiviare l'oggetto [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) restituito. Scorrere l'elenco `is_change_point` della risposta e visualizzare l'indice dei valori `true`. Questi valori corrispondono agli indici dei punti di modifica della tendenza, se individuati.

[!code-python[detect change points](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=changePointDetection)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` e il nome del file.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]