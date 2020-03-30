---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: ff4c33aea3d3ce604f44c38e6e3856242388b0e9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372190"
---
Usare la libreria client di previsione di LUIS (Language Understanding) per Python per:

* Ottenere previsioni per slot
* Ottenere previsioni per versione

[Documentazione di riferimento](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Pacchetto runtime di previsioni (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [ Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Prerequisites

* Account del portale di Language Understanding (LUIS) - [Creare un account gratuito](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurazione

### <a name="get-your-language-understanding-luis-runtime-key"></a>Ottenere la chiave di runtime di Language Understanding (LUIS)

Ottenere la [chiave di runtime](../luis-how-to-azure-subscription.md) creando una risorsa di runtime LUIS. Conservare la chiave e il relativo endpoint per il passaggio successivo.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Creare un nuovo file Python

Creare un nuovo file Python denominato `prediction_quickstart.py` nell'editor o nell'IDE preferito.

### <a name="install-the-sdk"></a>Installare l'SDK

Nella directory dell'applicazione installare la libreria client del runtime di previsioni di LUIS (Language Understanding) per Python con il comando seguente:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modello a oggetti

Il client del runtime di previsioni di LUIS (Language Understanding) è un oggetto [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) che esegue l'autenticazione in Azure e contiene la chiave della risorsa.

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* Previsioni per [slot di staging o produzione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Previsioni per [versione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client del runtime di previsioni di LUIS (Language Understanding) per Python:

* [Previsioni per slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file `prediction_quickstart.py` nell'editor o nell'IDE preferito. Aggiungere le dipendenze seguenti:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticare il client

1. Creare le variabili per le informazioni LUIS necessarie:

    Aggiungere le variabili per gestire la chiave di previsione derivata da una variabile di ambiente denominata `LUIS_RUNTIME_KEY`. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedere alla variabile sarà necessario chiudere e ricaricare l'editor, la shell o l'ambiente di sviluppo integrato in cui è in esecuzione. I metodi verranno creati in un secondo momento.

    Creare una variabile che contenga il nome della risorsa `LUIS_RUNTIME_ENDPOINT`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Creare una variabile per l'ID app come variabile di ambiente denominata `LUIS_APP_ID`. Impostare la variabile di ambiente sull'app IoT pubblica, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Creare una variabile per impostare lo slot pubblicato `production`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Creare un oggetto credenziali con la chiave e usarlo con l'endpoint per creare un oggetto [LUISRuntimeClientConfiguration]https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python().

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Ottenere previsioni dal runtime

Aggiungere il metodo seguente per creare la richiesta al runtime di previsioni.

L'espressione utente fa parte dell'oggetto [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python).

Per soddisfare la richiesta, il metodo **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** richiede numerosi parametri, ad esempio l'ID app, il nome dello slot e l'oggetto richiesta di previsioni. Le altre opzioni, ad esempio per l'output dettagliato, la visualizzazione di tutte le finalità e il log, sono facoltative. La richiesta restituisce un oggetto [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python).

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Codice main per le previsioni

Usare il metodo main seguente per collegare variabili e metodi tra loro e ottenere le previsioni.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python prediction_quickstart.py` dalla directory dell'applicazione.

```console
python prediction_quickstart.py
```

Nella console di avvio rapido viene visualizzato l'output:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Una volta completate le operazioni con le previsioni, pulire le risorse usate in questo argomento di avvio rapido eliminando il file e le relative sottodirectory.
