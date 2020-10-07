---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 88be632e17912012618ab559f22f97487ad26c9c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88723495"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analizzare i moduli per trovare le coppie chiave-valore e le tabelle

Quindi, usare il modello appena sottoposto a training per analizzare un documento da cui estrarre le coppie chiave-valore e le tabelle. Chiamare l'API **[Analyze Form](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** eseguendo il codice seguente in un nuovo script Python. Prima di eseguire lo script, apportare queste modifiche:

1. Sostituire `<file path>` con il percorso del file del proprio modulo (ad esempio, C:\temp\file.pdf). Può anche essere l'URL di un file remoto. Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Test** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Sostituire `<model_id>` con l'ID modello ricevuto nella sezione precedente.
1. Sostituire `<endpoint>` con l'endpoint ottenuto con la chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `<file type>` con il tipo di file. I tipi supportati sono `application/pdf`, `image/jpeg`, `image/png`, `image/tiff`.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione.

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    
    # <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    


    ---



1. Salvare il codice in un file con estensione py. Ad esempio, *form-recognizer-analyze.py*.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognizer-analyze.py`.

Quando si chiama l'API **Analyze Form**, si riceverà una risposta `201 (Success)` che include l'intestazione **Operation-Location**. Il valore di questa intestazione corrisponde all'ID da usare per tenere traccia dei risultati dell'operazione di analisi. Lo script precedente stampa il valore di questa intestazione sulla console.

## <a name="get-the-analyze-results"></a>Ottenere i risultati dell'analisi

Aggiungere il codice seguente in fondo allo script Python. Usa il valore di ID della chiamata precedente in una nuova chiamata API per recuperare i risultati dell'analisi. L'operazione **Analyze Form** è asincrona, per cui lo script chiama l'API a intervalli regolari finché non vengono ottenuti i risultati. Si consiglia un intervallo di almeno un secondo.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
