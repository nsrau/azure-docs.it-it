---
title: Ottenere il modello con una chiamata REST in C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 8cefd8357893657d94959cb853004b34b0ec9d8d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505844"
---
## <a name="prerequisites"></a>Prerequisiti

* Chiave di avvio.
* Importare l'[app TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) dal repository GitHub cognitive-services-language-understanding.
* L'ID applicazione di LUIS per l'app TravelAgent importata. L'ID applicazione viene visualizzato nel dashboard delle applicazioni.
* ID della versione all'interno dell'applicazione che riceve le espressioni. L'ID predefinito è "0.1".
* [Python 3.6](https://www.python.org/downloads/) o versione successiva.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>File JSON delle espressioni di esempio

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]


## <a name="change-model-programmatically"></a>Modificare il modello a livello di codice

Usare Vai a per aggiungere all'applicazione un'[API](https://aka.ms/luis-apim-v3-authoring) entità basata su Machine Learning. 

1. Creare un file denominato `model.py`. Aggiungere il codice seguente:

    ```python
    ########### Python 3.6 #############
    import requests
    
    # Starter key
    LUIS_authoringKey  = "YOUR-KEY"
    
    LUIS_APP_ID = "YOUR-APP-ID"
    
    # Authoring endpoint, example: westus.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"
    
    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_ID}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_ID}/train'
    
    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}
    
    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())
    
    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())
    
    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())
    
    addUtterances()
    train()
    trainStatus()
    ```
1. Sostituire i valori seguenti:

    * `YOUR-KEY` con la chiave di avvio
    * `YOUR-ENDPOINT` con l'endpoint, ad esempio `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` con l'ID app

1. Da un prompt dei comandi nella stessa directory in cui è stato creato il file immettere il comando seguente per eseguire il file:

    ```console
    python model.py
    ```  

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, eliminare il file dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per un'app](../luis-concept-best-practices.md)