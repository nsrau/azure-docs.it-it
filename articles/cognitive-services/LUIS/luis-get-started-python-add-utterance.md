---
title: "Esercitazione: Come aggiungere espressioni a un'app di Language Understanding con Python | Microsoft Docs"
description: Questa esercitazione illustra come chiamare un'app di Language Understanding con Python.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 3e1ca2ea874b6b39ac8a1b1eaa94fe9ff1894ea3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264302"
---
# <a name="tutorial-add-utterances-to-app-using-python"></a>Esercitazione: Aggiungere espressioni all'app con Python
In questa esercitazione si scrive un programma per aggiungere un'espressione a una finalità usando le API di creazione in Python.

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare un progetto console di Visual Studio 
> * Aggiungere il metodo per chiamare l'API di Language Understanding per aggiungere l'espressione ed eseguire il training dell'app
> * Aggiungere il file JSON con espressioni di esempio per la finalità BookFlight
> * Eseguire la console ed esaminare lo stato del training per le espressioni

Per altre informazioni, vedere la documentazione tecnica per le API di [aggiunta dell'espressione di esempio alla finalità](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45), [training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) e [stato del training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Per questo articolo è necessario un account gratuito di [Language Understanding][LUIS] per creare la propria applicazione.

## <a name="prerequisites"></a>prerequisiti

* [Python 3.6](https://www.python.org/downloads/) o versione successiva.
* **[Consigliato]** [Visual Studio Code](https://code.visualstudio.com/) per IntelliSense e debug.
* **[Chiave di creazione](luis-concept-keys.md#authoring-key)** di Language Understanding. È possibile trovare questa chiave in Account Settings (Impostazioni account) nel sito Web di [Language Understanding](luis-reference-regions.md).
* [**ID dell'applicazione**](./luis-get-started-create-app.md) di Language Understanding esistente. L'ID applicazione viene visualizzato nel dashboard delle applicazioni. L'applicazione di Language Understanding con le finalità e le entità usate nel file `utterances.json` deve esistere prima di eseguire il codice in `add-utterances.js`. Il codice riportato in questo articolo non crea le finalità e le entità. Aggiunge solo le espressioni per le entità e le finalità esistenti. 
* **ID della versione** all'interno dell'applicazione che riceve le espressioni. L'ID predefinito è "0.1"
* Creare un nuovo file denominato `add-utterances-3-6.py` in VSCode.

> [!NOTE] 
> Il file `add-utterances-3-6.py` completo è disponibile nel [**repository GitHub LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/python).


## <a name="write-the-python-code"></a>Scrivere il codice Python

1. Copiare i frammenti di codice seguenti:

   [!code-python[Console app code that adds an utterance Python 3.6](~/samples-luis/documentation-samples/authoring-api-samples/python/add-utterances-3-6.py)]

## <a name="specify-utterances-to-add"></a>Specificare le espressioni da aggiungere
Creare e modificare il file `utterances.json` per specificare la **matrice di espressioni** da aggiungere all'app di Language Understanding. La finalità e le entità **devono** essere già presenti nell'app di Language Understanding.

> [!NOTE]
> L'applicazione di Language Understanding con le finalità e le entità usate nel file `utterances.json` deve esistere prima di eseguire il codice in `add-utterances.js`. Il codice riportato in questo articolo non crea le finalità e le entità. Aggiunge solo le espressioni per le entità e le finalità esistenti.

Il campo `text` contiene il testo dell'espressione. Il campo `intentName` deve corrispondere al nome di una finalità nell'app di Language Understanding. Il campo `entityLabels` è obbligatorio. Se non si vuole etichettare alcuna entità, fornire un elenco vuoto come mostrato nell'esempio seguente:

Se l'elenco entityLabels non è vuoto, è necessario che `startCharIndex` e `endCharIndex` contrassegnino l'entità a cui si fa riferimento nel campo `entityName`. Entrambi gli indici sono conteggi in base zero, ovvero il 6 nell'esempio si riferisce alla "S" di Seattle e non allo spazio prima della S maiuscola.

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

Eseguire l'applicazione da una riga di comando con Python 3.6.

Chiamando add-utterance senza argomenti verrà aggiunta un'espressione all'app senza eseguirne il training.

````
> python add-utterances-3-6.py
````

Questo esempio crea un file con `results.json` che contiene i risultati della chiamata all'API di aggiunta espressioni. Il campo `response` è in questo formato per le espressioni aggiunte. `hasError` è false, a indicare che l'espressione è stata aggiunta.  

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
> python add-utterances-3-6.py -train
````

> [!NOTE]
> Le espressioni duplicate non vengono aggiunte di nuovo, ma non causano un errore. `response` contiene l'ID dell'espressione originale.

Quando si chiama l'esempio con l'argomento `-train`, viene creato un file `training-results.json` che indica che la richiesta di eseguire il training dell'app di Language Understanding è stata inserita in coda. 

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
> python add-utterances-3-6.py -status
````
## <a name="clean-up-resources"></a>Pulire le risorse
Al termine dell'esercitazione, rimuovere Visual Studio e l'applicazione console se non sono più necessari. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Compilare un'app di Language Understanding a livello di codice](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website

