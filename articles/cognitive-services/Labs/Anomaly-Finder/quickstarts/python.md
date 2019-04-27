---
title: Come usare l'API Ricerca di anomalie con Python - Servizi cognitivi Microsoft | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare Ricerca di anomalie con Python in Servizi cognitivi.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: df894fee3328b2199b8f7436adabfd3db62f4004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217293"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Usare l'API Ricerca di anomalie con Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Questo articolo fornisce informazioni ed esempi di codice per aiutare a iniziare rapidamente a usare l'API Ricerca di anomalie con Python per acquisire i risultati sulle anomalie per dati di serie temporali.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Recupero di punti di anomalie con l'API Ricerca di anomalie usando Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Esempio di dati di serie temporali

L'esempio di punti dati di una serie temporale è riportato di seguito.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Esempio di analisi dei dati e acquisizione dei punti di anomalie con Python

Verificare che python3 sia installato, quindi creare un file python eseguibile denominato detect.py. Inserire in detect.py il codice riportato di seguito. Prima di eseguire il codice, ricordarsi di sostituire il valore `[YOUR_SUBSCRIPTION_KEY]` con la propria chiave di sottoscrizione valida.
Sostituire `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` con i propri punti dati.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>Risposta di esempio

Una risposta con esito positivo viene restituita in JSON. Di seguito viene riportata una risposta di esempio.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App Python](../tutorials/python-tutorial.md)
