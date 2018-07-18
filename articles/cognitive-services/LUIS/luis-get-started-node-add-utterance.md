---
title: "Esercitazione: Come aggiungere espressioni a un'app di Language Understanding Intelligent Service (LUIS) con Node.js | Microsoft Docs"
description: Questa esercitazione illustra come chiamare un'app di Language Understanding Intelligent Service (LUIS) con Node.js.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: a1e028395ce4b1679a367ce110c2ba120128c501
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264227"
---
# <a name="tutorial-add-utterances-app-using-nodejs"></a>Esercitazione: Aggiungere espressioni all'app con Node.js 
In questa esercitazione si scrive un programma per aggiungere un'espressione a una finalità usando le API di creazione in Node.js.

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare un progetto console di Visual Studio 
> * Aggiungere il metodo per chiamare l'API di LUIS per aggiungere l'espressione ed eseguire il training dell'app
> * Aggiungere il file JSON con espressioni di esempio per la finalità BookFlight
> * Eseguire la console ed esaminare lo stato del training per le espressioni

Per altre informazioni, vedere la documentazione tecnica per le API di [aggiunta dell'espressione di esempio alla finalità](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) e [stato del training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Per questo articolo è necessario un account gratuito di [LUIS][LUIS] per creare la propria applicazione.

## <a name="prerequisites"></a>prerequisiti

* La versione più recente di [**Node.js**](https://nodejs.org/en/download/) con NPM.
* Dipendenze NPM per questo articolo: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* **[Consigliato]** [Visual Studio Code](https://code.visualstudio.com/) per IntelliSense e debug.
* **[Chiave di creazione](luis-concept-keys.md#authoring-key)** di LUIS. È possibile trovare questa chiave in Account Settings (Impostazioni account) nel sito Web di [LUIS](luis-reference-regions.md).
* [**ID dell'applicazione**](./luis-get-started-create-app.md) di LUIS esistente. L'ID applicazione viene visualizzato nel dashboard delle applicazioni. L'applicazione di LUIS con le finalità e le entità usate nel file `utterances.json` deve esistere prima di eseguire il codice in `add-utterances.js`. Il codice riportato in questo articolo non crea le finalità e le entità. Aggiunge solo le espressioni per le entità e le finalità esistenti. 
* **ID della versione** all'interno dell'applicazione che riceve le espressioni. L'ID predefinito è "0.1"
* Creare un nuovo file denominato progetto `add-utterances.js` in VSCode.

> [!NOTE] 
> Il file `add-utterances.js` completo è disponibile nel [**repository GitHub LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/tree/master/examples/add-utterances/nodejs).


## <a name="write-the-nodejs-code"></a>Scrivere il codice Node.js

Aggiungere le dipendenze NPM al file.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=16-19 "NPM Dependencies")]

Aggiungere le costanti LUIS al file. Copiare il codice seguente e immettere la propria chiave di creazione, insieme all'ID applicazione e all'ID versione.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=22-29 "LUIS key and IDs")]

Aggiungere il nome e il percorso del file di caricamento che contiene le espressioni. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=31-33 "Add upload file")]

Aggiungere le variabili che contengono i valori della riga di comando.

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=35-49 "Add upload file")]


Aggiungere la funzione `sendUtteranceToApi` per inviare e ricevere chiamate HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=135-151 "Send the HTTP request")]

Aggiungere l'oggetto JSON di configurazione usato dalla funzione `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=52-59 "Add configuration information for adding utterance")]

Aggiungere la funzione `addUtterance` per gestire la richiesta API e la risposta usate da `SendUtteranceToApp`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=62-92 "Add configuration information for adding utterance")]

Aggiungere l'oggetto JSON di configurazione usato dalla funzione `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=94-101 "Add configuration information for training LUIS")]

Aggiungere la funzione `train` per avviare il processo di training. 

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=103-133 "Train the application")]

Aggiungere il codice che sceglie l'azione da eseguire (aggiunta di espressione o training) in base alle variabili della riga di comando.

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=153-184 "Train the application")]

## <a name="specify-utterances-to-add"></a>Specificare le espressioni da aggiungere
Creare e modificare il file `utterances.json` per specificare la **matrice di espressioni** da aggiungere all'app di LUIS. La finalità e le entità **devono** essere già presenti nell'app di LUIS.

> [!NOTE]
> L'applicazione di LUIS con le finalità e le entità usate nel file `utterances.json` deve esistere prima di eseguire il codice in `add-utterances.js`. Il codice riportato in questo articolo non crea le finalità e le entità. Aggiunge solo le espressioni per le entità e le finalità esistenti.

Il campo `text` contiene il testo dell'espressione. Il campo `intentName` deve corrispondere al nome di una finalità nell'app di LUIS. Il campo `entityLabels` è obbligatorio. Se non si vuole etichettare alcuna entità, fornire un elenco vuoto come mostrato nell'esempio seguente.

Se l'elenco entityLabels non è vuoto, è necessario che `startCharIndex` e `endCharIndex` contrassegnino l'entità a cui si fa riferimento nel campo `entityName`. Entrambi gli indici sono conteggi in base zero, ovvero il 6 nell'esempio in alto si riferisce alla "S" di Seattle e non allo spazio prima della S maiuscola.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>Aggiungere un'espressione dalla riga di comando

Eseguire l'applicazione da una riga di comando con Node.js.

Chiamando add-utterance senza argomenti verrà aggiunta un'espressione all'app senza eseguirne il training.
````
> node add-utterances.js
````

Questo esempio crea un file con `results.json` che contiene i risultati della chiamata dell'API di aggiunta espressioni. Il campo `response` è in questo formato per le espressioni aggiunte. `hasError` è false, a indicare che l'espressione è stata aggiunta.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Aggiungere un'espressione ed eseguire il training dalla riga di comando
Chiamare add-utterance con l'argomento `-train` per inviare una richiesta di training e successivamente richiedere lo stato del training. Lo stato viene inserito in coda subito dopo l'inizio del training. I dettagli sullo stato vengono scritti in un file.

````
> node add-utterances.js -train
````

> [!NOTE]
> Le espressioni duplicate non vengono aggiunte di nuovo, ma non causano un errore. `response` contiene l'ID dell'espressione originale.

Quando si chiama l'esempio con l'argomento `-train`, viene creato un file `training-results.json` che indica che la richiesta di eseguire il training dell'app di LUIS è stata inserita in coda. 

Di seguito sono riportati i risultati di una richiesta di training riuscita:
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

Dopo che la richiesta di training è stata inserita in coda, il completamento del training può richiedere qualche istante.

## <a name="get-training-status-from-the-command-line"></a>Ottenere lo stato del training dalla riga di comando
Chiamare l'esempio con l'argomento `-status` per verificare lo stato del training e scrivere i dettagli dello stato in un file.

````
> node add-utterances.js -status
````

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine dell'esercitazione, rimuovere Visual Studio e l'applicazione console se non sono più necessari. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Compilare un'app di Language Understanding Intelligent Service (LUIS) a livello di codice](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
