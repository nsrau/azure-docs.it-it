---
title: includere file
description: includere file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, cog-serv-seo-aug-2020
ms.openlocfilehash: 4e7144c424fd2187436a05d135732532e95063a1
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545094"
---
Usare le librerie client di LUIS (Language Understanding) per Python per:

* Creare un'app
* Aggiungere una finalità, un'entità basata Machine Learning, con un'espressione di esempio
* Eseguire il training e pubblicare l'app
* Eseguire query sul runtime di previsione

[Documentazione di riferimento](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Codice sorgente della libreria di creazione](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/authoring) e [previsione](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/runtime) | [Pacchetto (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/sdk-3x/authoring_and_predict.py)

## <a name="prerequisites"></a>Prerequisiti

* La versione corrente di [Python 3.x](https://www.python.org/).
* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Dopo aver creato la sottoscrizione di Azure, [creare una risorsa di creazione Language Understanding](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa [creata](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) sono necessari per connettere l'applicazione alla creazione di Language Understanding. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido. Per provare il servizio, è possibile usare il piano tariffario gratuito (`F0`).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

1. In una finestra della console creare una nuova directory per l'applicazione e accedervi.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Creare un file denominato `authoring_and_predict.py` per il codice Python.

    ```console
    touch authoring_and_predict.py
    ```

### <a name="install-the-client-library-with-pip"></a>È possibile installare la libreria client con Pip

Nella directory dell'applicazione installare la libreria client di LUIS (Language Understanding) per Python con il comando seguente:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="authoring-object-model"></a>Modello a oggetti di creazione

Il client di creazione di Language Understanding (LUIS) è un oggetto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) contenente la chiave di creazione che esegue l'autenticazione in Azure.

## <a name="code-examples-for-authoring"></a>Esempi di codice per la creazione

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* App: [creazione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [eliminazione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [pubblicazione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Espressioni di esempio: [aggiunta in base al batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [eliminazione in base all'ID](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Funzionalità: gestione di [elenchi di frasi](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Modello: gestione di [finalità](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) ed entità
* Criterio: gestione di [criteri](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Training: [training](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) dell'app e polling per lo [stato del training](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versioni](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python): gestione con clonazione, esportazione ed eliminazione


## <a name="prediction-object-model"></a>Modello a oggetti di previsione

Il client del runtime di previsioni di LUIS (Language Understanding) è un oggetto [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) che esegue l'autenticazione in Azure e contiene la chiave della risorsa.

## <a name="code-examples-for-prediction-runtime"></a>Esempi di codice per il runtime di previsione

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* Previsioni per [slot di staging o produzione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Previsioni per [versione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Aggiungere le librerie client al file Python.

[!code-python[Add python libraries to code file](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=Dependencies)]


## <a name="add-boilerplate-code"></a>Aggiungere il codice boilerplate

1. Aggiungere il metodo `quickstart` e la relativa chiamata. Questo metodo include la maggior parte del codice rimanente. Questo metodo viene chiamato alla fine del file.

    ```python
    def quickstart():

        # add calls here, remember to indent properly

    quickstart()
    ```

1. Aggiungere il codice rimanente nel metodo di avvio rapido se non diversamente specificato.

## <a name="create-variables-for-the-app"></a>Creare variabili per l'app

Creare due set di variabili: il primo set modificato, il secondo set viene lasciato come visualizzato nell'esempio di codice. 

1. Creare le variabili in cui contenere la chiave di creazione e i nomi delle risorse.

    [!code-python[Variables you need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouChange)]

1. Creare le variabili in cui contenere gli endpoint, il nome dell'app, la versione e il nome della finalità.

    [!code-python[Variables you don't need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python).

[!code-python[Authenticate the client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Creare un'app LUIS

Un'app LUIS contiene il modello di elaborazione del linguaggio naturale (NLP) che include finalità, entità ed espressioni di esempio.

Create il metodo [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) di un oggetto [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) per creare l'app. Il nome e le impostazioni cultura della lingua sono proprietà obbligatorie.

[!code-python[Create a LUIS app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Creare la finalità per l'app
L'oggetto primario in un modello di app LUIS è la finalità. La finalità è allineata a un raggruppamento di _intenzioni_ delle espressioni dell'utente. Un utente può porre una domanda o fare un'affermazione per una risposta _con finalità_ specifica da un bot o da un'altra applicazione client. Alcuni esempi di intenzioni sono la prenotazione di un volo, la richiesta di informazioni meteo per una città di destinazione e la richiesta di informazioni di contatto per il servizio clienti.

Usare il metodo [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) con il nome della finalità univoca e quindi passare l'ID app, l'ID versione e il nuovo nome della finalità.

Il valore `intentName` è hardcoded per `OrderPizzaIntent` come parte delle variabili descritte nella sezione [Creare variabili per l'app](#create-variables-for-the-app).

[!code-python[Create intent for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Creare le entità per l'app

Sebbene le entità non siano obbligatorie, sono in genere presenti nella maggior parte delle app. L'entità estrae le informazioni necessarie per soddisfare l'intenzione dell'utente dall'espressione dell'utente. Sono disponibili numerosi tipi di entità [predefinite](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) e personalizzate, ognuna con i propri modelli DTO (Data Transformation Object).  Le entità predefinite comuni da aggiungere all'app includono [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

È importante tenere presente che le entità non sono contrassegnate con una finalità. Possono in genere essere applicate a molte finalità. Solo le espressioni utente di esempio sono contrassegnate per un'unica finalità specifica.

I metodi di creazione per le entità fanno parte della classe [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python). Ogni tipo di entità ha il proprio modello DTO (Data Transformation Object).

Il codice di creazione entità crea un'entità basata su Machine Learning con le sottoentità e le funzionalità applicate alle sottoentità `Quantity`.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Screenshot parziale del portale che mostra l'entità creata, un'entità di Machine Learning con le sottoentità e le funzionalità applicate alle sottoentità 'Quantity'.":::

[!code-python[Create entities for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddEntities)]

Posizionare il metodo seguente sopra il metodo `quickstart` per individuare l'ID della sottoentità Quantity, in modo da assegnare le funzionalità a tale sottoentità.

[!code-python[Find subentity id](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Aggiungere un'espressione di esempio alla finalità

Per determinare l'intenzione di un'espressione ed estrarre le entità, l'app necessita di esempi di espressioni. Gli esempi devono fare riferimento a un'unica finalità specifica e contrassegnare tutte le entità personalizzate. Non è necessario contrassegnare le entità predefinite.

Aggiungere le espressioni di esempio creando un elenco di oggetti [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python), un oggetto per ogni espressione di esempio. Ogni esempio deve contrassegnare tutte le entità con un dizionario di coppie nome/valore con nome dell'entità e valore dell'entità. Il valore dell'entità deve corrispondere esattamente a quanto visualizzato nel testo dell'espressione di esempio.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Screenshot parziale del portale che mostra l'entità creata, un'entità di Machine Learning con le sottoentità e le funzionalità applicate alle sottoentità 'Quantity'.":::

Chiamare [examples.add](https://docs.microsoft.com//python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#add-app-id--version-id--example-label-object--enable-nested-children-false--custom-headers-none--raw-false----operation-config-) con l'ID app, l'ID versione e l'esempio.

[!code-python[Add example utterance to intent](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Eseguire il training dell'app

Dopo aver creato il modello, è necessario eseguire il training dell'app LUIS per questa versione del modello. Un modello sottoposto a training può essere usatoin un [contenitore](../luis-container-howto.md) o [pubblicato](../luis-how-to-publish-app.md) negli slot di staging o di produzione.

Il metodo [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) richiede l'ID app e l'ID versione.

Il training di un modello di dimensioni ridotte, come quello mostrato in questa esercitazione di avvio rapido, verrà eseguito molto rapidamente. Per le applicazioni a livello di produzione, il training dell'app deve includere una chiamata di polling al metodo [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) per determinare quando o se il training è riuscito. La risposta è un elenco di oggetti [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) con uno stato separato per ogni oggetto. Affinché il training venga considerato completato, è necessario che tutti gli oggetti abbiano esito positivo.

[!code-python[Train the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Pubblicare l'app nello slot di produzione

Pubblicare l'app LUIS tramite il metodo [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-). Questo metodo consente di pubblicare la versione con training corrente nello slot specificato nell'endpoint. L'applicazione client usa questo endpoint per inviare espressioni utente per la previsione della finalità e l'estrazione di entità.

[!code-python[Publish app to production slot](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Autenticare il client del runtime di previsione

Usare l'oggetto credenziali con la chiave e con l'endpoint per creare un oggetto [LUISRuntimeClientConfiguration](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python).

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-python[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Ottenere previsioni dal runtime

Aggiungere il codice seguente per creare la richiesta al runtime di previsione.

L'espressione utente fa parte dell'oggetto [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python).

Per soddisfare la richiesta, il metodo **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** richiede numerosi parametri, ad esempio l'ID app, il nome dello slot e l'oggetto richiesta di previsioni. Le altre opzioni, ad esempio per l'output dettagliato, la visualizzazione di tutte le finalità e il log, sono facoltative. La richiesta restituisce un oggetto [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python).

[!code-python[Get prediction from runtime](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` nel file quickstart.

```console
python authoring_and_predict.py
```
