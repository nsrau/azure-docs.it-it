---
title: 'Avvio rapido: Rilevare le anomalie di dati usando la libreria di rilevatore di anomalie e Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Usare l'API Rilevamento anomalie per rilevare le anomalie nelle serie di dati sia come batch che nei dati in streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 1d89ed8f40547142d41af9c587fc8fc000fa4dd9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503675"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Avvio rapido: Libreria client di rilevamento delle anomalie per Python

Iniziare con la libreria client di rilevatore di anomalie per .NET. Seguire questi passaggi per installare il pacchetto e provare a utilizzare il codice di esempio per le attività di base. Il servizio di rilevatore di anomalie consente di trovare le anomalie nei dati delle serie temporali utilizzando automaticamente i modelli di mapping più appropriati su di esso, indipendentemente dal settore, scenario o volume di dati.

## <a name="key-concepts"></a>Concetti chiave

Usare la libreria client di rilevatore di anomalie per Python per:

* Rilevare le anomalie in tutto il set di dati di serie temporale, come una richiesta batch
* Rilevare lo stato delle anomalie del punto dati più recente nella serie temporale

[Documentazione di riferimento della libreria](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [pacchetto (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [esempi](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure - [crearne uno gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Il [libreria di analisi dei dati di Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Configurazione

### <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di rilevatore di anomalie

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>modello a oggetti

Il client di rilevatore di anomalie è un [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) oggetto che esegue l'autenticazione in Azure usando la chiave. Il client fornisce due metodi di rilevamento delle anomalie: In un intero set di dati utilizzando [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)e scegliere i dati più recenti usando [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Dati delle serie temporali viene inviati come una serie di [punti](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) in un [richiesta](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) oggetto. Il `Request` oggetto contiene le proprietà per descrivere i dati ([granularità](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) , ad esempio) e i parametri per il rilevamento delle anomalie. 

La risposta di rilevatore di anomalie è un [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) oppure [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) oggetti a seconda del metodo usato. 

## <a name="getting-started"></a>Introduzione

Creare una nuova applicazione Python nel IDE o editor preferito. Quindi aggiungere le seguenti dichiarazioni di importazione del file. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> Questa Guida introduttiva presuppone di aver [creati una variabile di ambiente](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave del Rilevatore di anomalie, denominato `ANOMALY_DETECTOR_KEY`.

Creare variabili per la chiave come variabile di ambiente, il percorso a un file di dati di serie ora e la località di azure della sottoscrizione. Ad esempio: `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>Esempi di codice 

Questi frammenti di codice illustrano come eseguire le operazioni seguenti con la libreria client di rilevatore di anomalie per .NET:

* [L'autenticazione del client](#authenticate-the-client)
* [Caricare un set di dati di serie temporale da un file](#load-time-series-data-from-a-file)
* [Rilevare le anomalie nell'intero set di dati](#detect-anomalies-in-the-entire-data-set) 
* [Rilevare lo stato delle anomalie del punto dati più recente](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>L'autenticazione del client

Aggiungere la variabile località di azure per l'endpoint e autenticare il client con la chiave.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Caricare dati in serie temporale da un file

I dati di esempio per questa Guida introduttiva da scaricare [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Nel browser, fare doppio clic su **Raw**
2. Fare clic su **collegamento Salva come**
3. Salvare il file alla directory dell'applicazione, come file con estensione csv.

Questa serie temporale viene formattati come un file con estensione csv e verranno inviati per l'API rilevatore di anomalie.

Caricare il file di dati con la libreria di Pandas `read_csv()` (metodo) e verificare una variabile per archiviare la serie di dati di elenco vuoto. Scorrere il file e accodare i dati come una [punto](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) oggetto. Questo oggetto contiene il timestamp e il valore numerico dalle righe del file di dati CSV. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Creare un [richiedere](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) oggetto con la serie temporale e il [granularità](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (o della periodicità) dei punti di dati. Ad esempio: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Rilevare le anomalie nell'intero set di dati 

Chiamare l'API per rilevare le anomalie attraverso l'intera serie temporale tramite il client [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) (metodo). Store restituita [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) oggetto. Eseguire l'iterazione attraverso la risposta `is_anomaly` elencare e stampa l'indice di qualsiasi `true` valori. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

Chiamare l'API rilevatore di anomalie per determinare se il punto dati più recente è un'anomalia usando il client [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) metodo e archivio restituito [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) oggetto. La risposta `is_anomaly` valore è un valore booleano che specifica lo stato di tale punto delle anomalie.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione nell'IDE o nella riga di comando con il `python` comando e il nome del file.
 
## <a name="clean-up-resources"></a>Pulire le risorse

Se si desidera pulire e rimuovere una sottoscrizione a servizi cognitivi, è possibile eliminare la risorsa o gruppo di risorse. L'eliminazione del gruppo di risorse elimina anche eventuali altre risorse associate al gruppo di risorse.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

È anche possibile eseguire il comando di shell cloud seguente per rimuovere il gruppo di risorse e le risorse associate. L'operazione potrebbe richiedere alcuni minuti. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Rilevamento delle anomalie in streaming con Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Che cos'è il [API rilevatore di anomalie?](../overview.md)
* [Procedure consigliate](../concepts/anomaly-detection-best-practices.md) quando si usa l'API rilevatore di anomalie.
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
