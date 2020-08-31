---
title: 'Avvio rapido: Estrarre i dati dei biglietti da visita usando Python - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà l'API REST Riconoscimento modulo con Python per estrarre i dati dalle immagini di biglietti da visita in lingua inglese.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 8132358dcd0ad9d87dc6687afd2adef1942f3b67
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88823905"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Avvio rapido: Estrarre i dati dei biglietti da visita usando l'API REST Riconoscimento modulo con Python

In questo argomento di avvio rapido si userà l'API REST Riconoscimento modulo di Azure con Python per estrarre e identificare informazioni rilevanti nei biglietti da visita.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:
- [Python](https://www.python.org/downloads/) installato, se si vuole eseguire l'esempio in locale.
- Un'immagine di un biglietto da visita. Per questo argomento di avvio rapido è possibile usare un'[immagine di esempio](../media/business-card-english.jpg).

> [!NOTE]
> Questa guida di avvio rapido usa un file locale. Per usare un'immagine di biglietto da visita accessibile tramite URL, vedere la [documentazione di riferimento](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync).

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analizzare un biglietto da visita

Per iniziare ad analizzare un biglietto da visita, chiamare l'API di **[analisi biglietti da visita](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** usando lo script Python seguente. Prima di eseguire lo script, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `<path to your business card>` con il percorso del documento modulo locale.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.

    ```python
    ########### Python Form Recognizer Async Business cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Salvare il codice in un file con estensione py. Ad esempio, *form-recognizer-businesscards.py*.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognizer-businesscards.py`.

Si riceverà una risposta `202 (Success)` contenente un'intestazione **Operation-Location**, che lo script stamperà nella console. Questa intestazione contiene un ID operazione che è possibile usare per eseguire una query sullo stato dell'operazione asincrona e ottenere i risultati. Nel valore di esempio seguente, la stringa dopo `operations/` è l'ID operazione.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>Ottenere i risultati del biglietto da visita

Dopo aver chiamato l'API di **analisi dei biglietti da visita**, chiamare l'API di **[recupero dei risultati dell'analisi dei biglietti da visita](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** per ottenere lo stato dell'operazione e i dati estratti. Aggiungere il codice seguente in fondo allo script Python. Viene usato il valore dell'ID operazione in una nuova chiamata API. Questo script chiama l'API a intervalli regolari finché non vengono ottenuti i risultati. Si consiglia un intervallo di almeno un secondo.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Salvare lo script.
1. Usare nuovamente il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Esaminare i risultati
![Biglietto da visita della società Contoso](../media/business-card-english.jpg)

Questo esempio illustra l'output JSON restituito da Riconoscimento modulo. L'esempio è stato troncato per migliorarne la leggibilità.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Lo script visualizzerà le risposte sulla console fino al completamento dell'operazione di **analisi biglietto da visita**. Il nodo `"readResults"` contiene tutto il testo riconosciuto. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. Il nodo `"documentResults"` contiene i valori specifici del biglietto da visita individuati dal modello. Qui sono disponibili coppie chiave/valore utili, come il nome della società, il nome e cognome, il numero di telefono e così via.


## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata l'API REST Riconoscimento modulo con Python per estrarre il contenuto di un biglietto da visita. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
