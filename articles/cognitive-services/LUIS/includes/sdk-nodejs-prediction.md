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
ms.openlocfilehash: 9c15e4217c5331346c5a95329bae7e2a4f0e0841
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732043"
---
Usare la libreria client di runtime di LUIS (Language Understanding) per Node.js per:

* Previsioni per slot
* Previsioni per versione

[Documentazione di riferimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Pacchetto di runtime (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Prerequisiti

* Risorsa di runtime di LUIS (Language Understanding): [Crearne una nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Un ID app LUIS: usare l'ID app IoT pubblica `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. La query utente usata nel codice di avvio rapido è specifica di tale app.

## <a name="setting-up"></a>Configurazione

### <a name="get-your-language-understanding-luis-runtime-key"></a>Ottenere la chiave di runtime di Language Understanding (LUIS)

Ottenere la [chiave di runtime](../luis-how-to-azure-subscription.md) creando una risorsa di runtime LUIS. Conservare la chiave e il relativo endpoint per il passaggio successivo.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Creare un nuovo file JavaScript (Node.js)

Creare un nuovo file JavaScript, denominato `luis_prediction.js`, nell'editor o nell'IDE preferito.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Installare la libreria NPM per il runtime di LUIS

Nella directory dell'applicazione installare le dipendenze con il comando seguente:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Modello a oggetti

Il client di creazione di Language Understanding (LUIS) è un oggetto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) contenente la chiave di creazione che esegue l'autenticazione in Azure.

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* [Previsioni](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) per slot di `staging` o `production`
* [Previsioni per versione](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client del runtime di previsioni di LUIS (Language Understanding):

* [Previsioni per slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file `luis_prediction.js` nell'editor o nell'IDE preferito. Aggiungere le dipendenze seguenti:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticare il client

1. Creare le variabili per le informazioni LUIS necessarie:

    Aggiungere le variabili per gestire la chiave di previsione derivata da una variabile di ambiente denominata `LUIS_RUNTIME_KEY`. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedere alla variabile sarà necessario chiudere e ricaricare l'editor, la shell o l'ambiente di sviluppo integrato in cui è in esecuzione. I metodi verranno creati in un secondo momento.

    Creare una variabile che contenga il nome della risorsa `LUIS_RUNTIME_ENDPOINT`.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Creare una variabile per l'ID app come variabile di ambiente denominata `LUIS_APP_ID`. Impostare la variabile di ambiente sull'app IoT pubblica, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Creare una variabile per impostare lo slot pubblicato `production`.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Creare un oggetto msRest.ApiKeyCredentials con la chiave e usarlo con l'endpoint per creare un oggetto [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest).

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Ottenere previsioni dal runtime

Aggiungere il metodo seguente per creare la richiesta al runtime di previsioni.

L'espressione utente fa parte dell'oggetto [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest).

Per soddisfare la richiesta, il metodo **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** richiede numerosi parametri, ad esempio l'ID app, il nome dello slot e l'oggetto richiesta di previsioni. Le altre opzioni, ad esempio per l'output dettagliato, la visualizzazione di tutte le finalità e il log, sono facoltative.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Codice main per le previsioni

Usare il metodo main seguente per collegare variabili e metodi tra loro e ottenere le previsioni.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node luis_prediction.js` dalla directory dell'applicazione.

```console
node luis_prediction.js
```

Il risultato della previsione restituisce un oggetto JSON:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>Pulire le risorse

Una volta completate le operazioni con le previsioni, pulire le risorse usate in questo argomento di avvio rapido eliminando il file e le relative sottodirectory.
