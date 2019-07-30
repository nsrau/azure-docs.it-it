---
title: Importare le espressioni con node. js-LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni su come compilare un'app LUIS a livello di codice a partire da dati preesistenti in formato CSV usando l'API di creazione LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 79a372087e162fedc5b2e014a5cd4976df3cb2ce
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637818"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Compilare un'app LUIS a livello di codice con Node.js

LUIS fornisce un'API a livello di codice che esegue le stesse operazioni del sito Web [LUIS](luis-reference-regions.md). In questo modo è possibile risparmiare tempo quando si dispone di dati preesistenti e sarebbe più veloce creare un'app LUIS a livello di codice anziché immettere manualmente le informazioni. 

## <a name="prerequisites"></a>Prerequisiti

* Accedere al sito Web [Luis](luis-reference-regions.md) e trovare la [chiave di creazione](luis-concept-keys.md#authoring-key) in Impostazioni account. Questa chiave viene usata per chiamare l'API di creazione.
* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Questa esercitazione inizia con un CSV per un file di log di richieste degli utenti di un'ipotetica azienda. Il download è disponibile [qui](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Installare la versione più recente di Node.js con NPM. Scaricarla [qui](https://nodejs.org/en/download/).
* **[Consigliato]** Visual Studio Code per IntelliSense e debug. Scaricarlo [qui](https://code.visualstudio.com/) gratuitamente.

Tutto il codice di questa esercitazione è disponibile nel [repository GitHub Azure-Samples Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv). 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Eseguire il mapping dei dati preesistenti a finalità ed entità
Anche se si dispone di un sistema che non è stato creato considerando LUIS, se contiene dati testuali mappati a cose diverse che gli utenti intendono realizzare, è possibile che si riesca a eseguire il mapping delle categorie di input utente esistenti alle finalità in LUIS. Se è possibile identificare parole o frasi importanti in ciò che gli utenti hanno detto, è possibile eseguire il mapping di queste parole a delle entità.

Aprire il [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) file. Questo file contiene un log di richieste degli utenti poste a un ipotetico servizio di domotica e include una suddivisione in categorie, ciò che l'utente ha detto e alcune colonne con informazioni utili estratte da quanto richiesto. 

![File CSV dei dati preesistenti](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Si noti che il contenuto della colonna **RequestType** potrebbe rappresentare le finalità e che la colonna **Request** riporta espressioni di esempio. Gli altri campi potrebbero costituire delle entità, se si presentano nell'espressione. Poiché sono presenti finalità, entità ed espressioni di esempio, ci sono tutti i requisiti per una semplice app di esempio.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Procedura per generate un'app LUIS da dati non LUIS
Per generare una nuova app LUIS dal file CSV:

* Analizzare i dati dal file CSV:
    * Convertire in un formato che è possibile caricare in LUIS usando l'API di creazione. 
    * Dai dati analizzati, raccogliere informazioni su Intent ed entità. 
* Eseguire le chiamate API di creazione a:
    * Creare l'app.
    * Aggiungere gli Intent e le entità raccolti dai dati analizzati. 
    * Dopo aver creato l'app LUIS, è possibile aggiungere le espressioni di esempio ottenute dai dati analizzati. 

È possibile visualizzare questo flusso di programma nell'ultima parte del `index.js` file. Copiare o [scaricare](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) questo codice e salvarlo in `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analizzare il file CSV

Le voci di colonna che contengono le espressioni nel file CSV devono essere analizzate in un formato JSON che LUIS è in grado di comprendere. Questo formato JSON deve contenere un campo `intentName` che identifica la finalità dell'espressione. Deve anche contenere un campo `entityLabels`, che può essere vuoto nel caso in cui non siano presenti entità nell'espressione. 

Ad esempio, la voce relativa a "Turn on the lights" esegue il mapping a questo codice JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

In questo esempio `intentName` deriva dalla richiesta dell'utente sotto l'intestazione di colonna **Request** nel file CSV, mentre `entityName` deriva dalle altre colonne con informazioni chiave. Ad esempio, se è presente una voce per **Operation** o **Device** e tale stringa è presente anche nella richiesta effettiva, può essere etichettata come entità. Il codice seguente illustra questo processo di analisi. È possibile copiarlo oppure [scaricarlo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) e salvarlo in `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Creare l'app LUIS
Dopo aver analizzato i dati in JSON, aggiungerli a un'app LUIS. Il codice seguente crea l'app LUIS. Copiarlo oppure [scaricarlo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) e salvarlo in `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Aggiungere finalità
Dopo aver creato un'app, è necessario aggiungervi le finalità. Il codice seguente crea l'app LUIS. Copiarlo oppure [scaricarlo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) e salvarlo in `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Aggiungi entità
Il codice seguente aggiunge le entità all'app LUIS. Copiarlo oppure [scaricarlo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) e salvarlo in `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Aggiungere espressioni
Dopo aver definito le entità e le finalità nell'app LUIS, è possibile aggiungere le espressioni. Il codice seguente usa l'API [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09), che consente di aggiungere fino a 100 espressioni alla volta.  Copiarlo oppure [scaricarlo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) e salvarlo in `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Eseguire il codice


### <a name="install-nodejs-dependencies"></a>Installare le dipendenze di Node.js
Installare le dipendenze di Node.js da NPM nel terminale/riga di comando.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Modificare le impostazioni di configurazione
Per usare questa applicazione, è necessario modificare i valori del file index.js con la propria chiave endpoint e specificare il nome che si desidera assegnare all'app. È anche possibile definire la lingua dell'app o modificare il numero di versione.

Aprire il file index.js e modificare questi valori nella parte superiore del file.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Esecuzione dello script
Eseguire lo script da un terminale/riga di comando con Node.js.

```console
> node index.js
```

oppure

```console
> npm start
```

### <a name="application-progress"></a>Avanzamento dell'applicazione
Mentre l'applicazione è in esecuzione, la riga di comando mostra lo stato di avanzamento. L'output della riga di comando include il formato delle risposte di LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Aprire l'app LUIS
Al termine dello script, è possibile accedere a [Luis](luis-reference-regions.md) e visualizzare l'app Luis creata in **app personali**. Le espressioni aggiunte dovrebbero apparire sotto le finalità**TurnOn**, **TurnOff** e **None**.

![Finalità TurnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Test e training dell'app sul sito Web LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Questa applicazione di esempio usa le API LUIS seguenti:
- [creazione app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [aggiunta finalità](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [aggiunta entità](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [aggiunta espressioni](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
