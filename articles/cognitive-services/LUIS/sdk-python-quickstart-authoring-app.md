---
title: 'Guida introduttiva: Libreria client di creazione di Language Understanding (LUIS) per Python'
titleSuffix: Azure Cognitive Services
description: Introduzione alla libreria client LUIS per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/07/2019
ms.author: diberry
ms.openlocfilehash: 1b586af569a9406d7fe9fa3d05c198f62f32744f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672013"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-python"></a>Guida introduttiva: Libreria client di creazione di Language Understanding (LUIS) per Python

Introduzione alla libreria client di creazione di Language Understanding (LUIS) per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.  Language Understanding (LUIS) consente di applicare l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni pertinenti e dettagliate. 

Usare la libreria client di creazione di Language Understanding (LUIS) per Python per eseguire queste operazioni:

* Creare un'app.
* Aggiungere finalità, entità ed espressioni di esempio.
* Aggiungere funzionalità, ad esempio un elenco di frasi.
* Eseguire il training di un'app e pubblicarla.

[Documentazione di riferimento](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Pacchetto di creazione (PyPI)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Prerequisiti

* Account del portale di Language Understanding (LUIS): [È possibile crearne uno gratuitamente](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurazione

### <a name="get-your-language-understanding-luis-starter-key"></a>Ottenere la chiave di avvio di Language Understanding (LUIS)

Ottenere la [chiave di avvio](luis-how-to-azure-subscription.md#starter-key) e [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `LUIS_AUTHORING_KEY`, e una variabile di ambiente per l'area della chiave, denominata `LUIS_REGION`.

### <a name="install-the-python-library-for-luis"></a>Installare la libreria Python per LUIS

Nella directory dell'applicazione installare la libreria client di creazione di Language Understanding (LUIS) per Python con il comando seguente:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modello a oggetti

Il client di creazione di Language Understanding (LUIS) è un oggetto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) contenente la chiave di creazione che esegue l'autenticazione in Azure.

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* App: [creazione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [eliminazione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [pubblicazione](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Espressioni di esempio: [aggiunta in base al batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [eliminazione in base all'ID](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-) 
* Funzionalità: gestione di [elenchi di frasi](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) 
* Modello: gestione di [finalità](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) ed entità
* Criterio: gestione di [criteri](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Training: [training](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) dell'app e polling per lo [stato del training](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versioni](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python): gestione con clonazione, esportazione ed eliminazione


## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di creazione di Language Understanding (LUIS) per Python:

* [Creare un'app](#create-a-luis-app)
* [Aggiungere entità](#create-entities-for-the-app)
* [Aggiungere tipi](#create-intent-for-the-app)
* [Aggiungi espressioni di esempio](#add-example-utterance-to-intent)
* [Eseguire il training dell'app](#train-the-app)
* [Pubblicare l'app](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare una nuova applicazione Python nell'ambiente di sviluppo integrato o nell'editor preferito. Importare quindi le librerie seguenti. 

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Creare le variabili per l'endpoint e la chiave di Azure della risorsa. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python).

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Creare un'app LUIS

1. Creare un'app LUIS contenente il modello di elaborazione del linguaggio naturale (NLP) che include finalità, entità ed espressioni di esempio. 

1. Create il metodo [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) di un oggetto [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) per creare l'app. Il nome e le impostazioni cultura della lingua sono proprietà obbligatorie. 

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Creare la finalità per l'app
L'oggetto primario in un modello di app LUIS è la finalità. La finalità è allineata a un raggruppamento di _intenzioni_ delle espressioni dell'utente. Un utente può porre una domanda o fare un'affermazione per una risposta _con finalità_ specifica da un bot o da un'altra applicazione client. Alcuni esempi di intenzioni sono la prenotazione di un volo, la richiesta di informazioni meteo per una città di destinazione e la richiesta di informazioni di contatto per il servizio clienti.   

Usare il metodo [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) con il nome della finalità univoca e quindi passare l'ID app, l'ID versione e il nuovo nome della finalità. 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Creare le entità per l'app

Sebbene le entità non siano obbligatorie, sono in genere presenti nella maggior parte delle app. L'entità estrae le informazioni necessarie per soddisfare l'intenzione dell'utente dall'espressione dell'utente. Sono disponibili numerosi tipi di entità [predefinite](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) e personalizzate, ognuna con i propri modelli DTO (Data Transformation Object).  Le entità predefinite comuni da aggiungere all'app includono [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md), [ordinal](luis-reference-prebuilt-ordinal.md). 

Questo metodo **add_entities** crea un'entità semplice `Location` con due ruoli, un'entità semplice `Class` e un'entità composita `Flight`, e aggiunge diverse entità predefinite.

È importante tenere presente che le entità non sono contrassegnate con una finalità. Possono in genere essere applicate a molte finalità. Solo le espressioni utente di esempio sono contrassegnate per un'unica finalità specifica.

I metodi di creazione per le entità fanno parte della classe [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python). Ogni tipo di entità ha il proprio modello DTO (Data Transformation Object). 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Aggiungere un'espressione di esempio alla finalità

Per determinare l'intenzione di un'espressione ed estrarre le entità, l'app necessita di esempi di espressioni. Gli esempi devono fare riferimento a un'unica finalità specifica e contrassegnare tutte le entità personalizzate. Non è necessario contrassegnare le entità predefinite. 

Aggiungere le espressioni di esempio creando un elenco di oggetti [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python), un oggetto per ogni espressione di esempio. Ogni esempio deve contrassegnare tutte le entità con un dizionario di coppie nome/valore con nome dell'entità e valore dell'entità. Il valore dell'entità deve corrispondere esattamente a quanto visualizzato nel testo dell'espressione di esempio. 

Chiamare [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) con l'ID app, l'ID versione e l'elenco di esempi. La chiamata risponde con un elenco di risultati. È necessario controllare il risultato di ogni esempio per assicurarsi che sia stato aggiunto correttamente al modello. 

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]
    
## <a name="train-the-app"></a>Eseguire il training dell'app

Dopo aver creato il modello, è necessario eseguire il training dell'app LUIS per questa versione del modello. Un modello sottoposto a training può essere usatoin un [contenitore](luis-container-howto.md) o [pubblicato](luis-how-to-publish-app.md) negli slot di staging o di produzione. 

Il metodo [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) richiede l'ID app e l'ID versione. 

Il training di un modello di dimensioni ridotte, come quello mostrato in questa esercitazione di avvio rapido, verrà eseguito molto rapidamente. Per le applicazioni a livello di produzione, il training dell'app deve includere una chiamata di polling al metodo [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) per determinare quando o se il training è riuscito. La risposta è un elenco di oggetti [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) con uno stato separato per ogni oggetto. Affinché il training venga considerato completato, è necessario che tutti gli oggetti abbiano esito positivo.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Pubblicare un'app Language Understanding

Pubblicare l'app LUIS tramite il metodo [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-). Questo metodo consente di pubblicare la versione con training corrente nello slot specificato nell'endpoint. L'applicazione client usa questo endpoint per inviare espressioni utente per la previsione della finalità e l'estrazione di entità.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` nel file quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources) 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Creare un'app LUIS per determinare le intenzioni dell'utente](luis-quickstart-intents-only.md)

* [Che cos'è l'API Language Understanding (LUIS)?](what-is-luis.md)
* [Novità](whats-new.md)
* [Finalità](luis-concept-intent.md), [entità](luis-concept-entity-types.md), [espressioni di esempio](luis-concept-utterance.md) ed [entità predefinite](luis-reference-prebuilt-entities.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py).
