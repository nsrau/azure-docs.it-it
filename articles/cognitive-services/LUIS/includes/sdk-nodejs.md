---
title: includere file
description: includere file
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 8/13/2020
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.author: diberry
ms.openlocfilehash: e52aba2fe6189c8964fd6525581b0bd6ee6def48
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246290"
---
Usare le librerie client di LUIS (Language Understanding) per Node.js per:

* Creare un'app
* Aggiungere una finalità, un'entità basata Machine Learning, con un'espressione di esempio
* Eseguire il training e pubblicare l'app
* Eseguire query sul runtime di previsione

[Documentazione di riferimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) |  [Codice sorgente della libreria di creazione](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) e [previsione](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [NPM di creazione](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring) e [previsione](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org)
* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Dopo aver creato la sottoscrizione di Azure, [creare una risorsa di creazione Language Understanding](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa [creata](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) sono necessari per connettere l'applicazione alla creazione di Language Understanding. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido. Per provare il servizio, è possibile usare il piano tariffario gratuito (`F0`).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-javascript-application"></a>Creare una nuova applicazione JavaScript

1. In una finestra della console creare una nuova directory per l'applicazione e accedervi.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Inizializzare la directory come applicazione JavaScript creando un file `package.json`. 

    ```console
    npm init -y
    ```

1. Creare un file denominato `index.js` per il codice JavaScript. 

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>Installare le librerie NPM

Nella directory dell'applicazione installare le dipendenze con i comandi seguenti, eseguiti una riga alla volta:

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring 
npm install @azure/cognitiveservices-luis-runtime
```

`package.json` dovrebbe essere simile al seguente:

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>Modello a oggetti di creazione

Il client di creazione di Language Understanding (LUIS) è un oggetto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) contenente la chiave di creazione che esegue l'autenticazione in Azure.

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* Apps: [aggiungi](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [elimina](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [pubblica](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Espressioni di esempio: [aggiunta in base al batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [eliminazione in base all'ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Funzionalità: gestione di [elenchi di frasi](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modello: gestione di [finalità](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) ed entità
* Criterio: gestione di [criteri](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Training: [training](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) dell'app e polling per lo [stato del training](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versioni](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest): gestione con clonazione, esportazione ed eliminazione

## <a name="prediction-object-model"></a>Modello a oggetti di previsione

Il client di creazione di Language Understanding (LUIS) è un oggetto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) contenente la chiave di creazione che esegue l'autenticazione in Azure.

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* [Previsioni](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) per slot di `staging` o `production`
* [Previsioni per versione](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Aprire il file `index.js` nell'editor o nell'IDE preferito, quindi aggiungere le dipendenze seguenti.

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Aggiungere il codice boilerplate 

1. Aggiungere il metodo `quickstart` e la relativa chiamata. Questo metodo include la maggior parte del codice rimanente. Questo metodo viene chiamato alla fine del file. 

    ```javascript
    const quickstart = async () => {
    
        // add calls here
    
    
    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. Aggiungere il codice rimanente nel metodo di avvio rapido se non diversamente specificato. 

## <a name="create-variables-for-the-app"></a>Creare variabili per l'app

1. Creare le variabili in cui contenere la chiave di creazione e i nomi delle risorse.

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. Creare le variabili in cui contenere gli endpoint, il nome dell'app, la versione e il nome della finalità.

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) con la chiave e usarlo con l'endpoint per creare un oggetto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest).

[!code-javascript[Create LUIS authoring client object](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Creare un'app LUIS

Un'app LUIS contiene il modello di elaborazione del linguaggio naturale (NLP) che include finalità, entità ed espressioni di esempio.

Create il metodo [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) di un oggetto [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) per creare l'app. Il nome e le impostazioni cultura della lingua sono proprietà obbligatorie.

[!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Creare la finalità per l'app
L'oggetto primario in un modello di app LUIS è la finalità. La finalità è allineata a un raggruppamento di _intenzioni_ delle espressioni dell'utente. Un utente può porre una domanda o fare un'affermazione per una risposta _con finalità_ specifica da un bot o da un'altra applicazione client. Alcuni esempi di intenzioni sono la prenotazione di un volo, la richiesta di informazioni meteo per una città di destinazione e la richiesta di informazioni di contatto per il servizio clienti.

Usare il metodo [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) con il nome della finalità univoca e quindi passare l'ID app, l'ID versione e il nuovo nome della finalità.

Il valore `intentName` è hardcoded per `OrderPizzaIntent` come parte delle variabili descritte nella sezione [Creare variabili per l'app](#create-variables-for-the-app).

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Creare le entità per l'app

Sebbene le entità non siano obbligatorie, sono in genere presenti nella maggior parte delle app. L'entità estrae le informazioni necessarie per soddisfare l'intenzione dell'utente dall'espressione dell'utente. Sono disponibili numerosi tipi di entità [predefinite](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) e personalizzate, ognuna con i propri modelli DTO (Data Transformation Object).  Le entità predefinite comuni da aggiungere all'app includono [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

È importante tenere presente che le entità non sono contrassegnate con una finalità. Possono in genere essere applicate a molte finalità. Solo le espressioni utente di esempio sono contrassegnate per un'unica finalità specifica.

I metodi di creazione per le entità sono parte della classe [Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest). Ogni tipo di entità ha il proprio modello DTO (Data Transformation Object).

Il codice di creazione entità crea un'entità basata su Machine Learning con le sottoentità e le funzionalità applicate alle sottoentità `Quantity`.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Il codice di creazione entità crea un'entità basata su Machine Learning con le sottoentità e le funzionalità applicate alle sottoentità 'Quantity'.":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

Inserire il metodo seguente sopra il metodo `quickstart` per individuare l'ID della sottoentità Quantity, in modo da assegnare le funzionalità a tale sottoentità.

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Aggiungere un'espressione di esempio alla finalità

Per determinare l'intenzione di un'espressione ed estrarre le entità, l'app necessita di esempi di espressioni. Gli esempi devono fare riferimento a un'unica finalità specifica e contrassegnare tutte le entità personalizzate. Non è necessario contrassegnare le entità predefinite.

Aggiungere le espressioni di esempio creando un elenco di oggetti [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest), un oggetto per ogni espressione di esempio. Ogni esempio deve contrassegnare tutte le entità con un dizionario di coppie nome/valore con nome dell'entità e valore dell'entità. Il valore dell'entità deve corrispondere esattamente a quanto visualizzato nel testo dell'espressione di esempio.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Il valore dell'entità deve corrispondere esattamente a quanto visualizzato nel testo dell'espressione di esempio.":::

Chiamare [examples.add](https://docs.microsoft.com//javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#add-string--string--examplelabelobject--models-examplesaddoptionalparams-) con l'ID app, l'ID versione e l'esempio.

[!code-javascript[Add example utterance to a specific intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Eseguire il training dell'app

Dopo aver creato il modello, è necessario eseguire il training dell'app LUIS per questa versione del modello. Un modello sottoposto a training può essere usatoin un [contenitore](../luis-container-howto.md) o [pubblicato](../luis-how-to-publish-app.md) negli slot di staging o di produzione.

Il metodo [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) richiede l'ID app e l'ID versione.

Il training di un modello di dimensioni ridotte, come quello mostrato in questa esercitazione di avvio rapido, verrà eseguito molto rapidamente. Per le applicazioni a livello di produzione, il training dell'app deve includere una chiamata di polling al metodo [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) per determinare quando o se il training è riuscito. La risposta è un elenco di oggetti [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) con uno stato separato per ogni oggetto. Affinché il training venga considerato completato, è necessario che tutti gli oggetti abbiano esito positivo.

[!code-javascript[Train the app's version](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-a-language-understanding-app"></a>Pubblicare un'app Language Understanding

Pubblicare l'app LUIS tramite il metodo [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-). Questo metodo consente di pubblicare la versione con training corrente nello slot specificato nell'endpoint. L'applicazione client usa questo endpoint per inviare espressioni utente per la previsione della finalità e l'estrazione di entità.

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>Autenticare il client del runtime di previsione

Usare un oggetto msRest.ApiKeyCredentials con la chiave e con l'endpoint per creare un oggetto [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest).

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Create LUIS runtime client object](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Ottenere previsioni dal runtime

Aggiungere il codice seguente per creare la richiesta al runtime di previsione. L'espressione utente fa parte dell'oggetto [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest).

Per soddisfare la richiesta, il metodo **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** richiede numerosi parametri, ad esempio l'ID app, il nome dello slot e l'oggetto richiesta di previsioni. Le altre opzioni, ad esempio per l'output dettagliato, la visualizzazione di tutte le finalità e il log, sono facoltative.

[!code-javascript [Get prediction based on query](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node index.js` nel file quickstart.

```console
node index.js
```