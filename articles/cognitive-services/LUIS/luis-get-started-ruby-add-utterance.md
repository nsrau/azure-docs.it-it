---
title: "Esercitazione: Come aggiungere espressioni a un'app di Language Understanding con Ruby | Microsoft Docs"
description: Questa esercitazione illustra come chiamare un'app di Language Understanding con Ruby.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 7a470fd551a58978e6f2be0450a2e2a6cd471fc4
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266057"
---
# <a name="tutorial-add-utterances-to-app-using-ruby"></a>Esercitazione: Aggiungere espressioni all'app con Ruby 
In questa esercitazione si scrive un programma per aggiungere un'espressione a una finalità usando le API di creazione in Ruby.

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare un progetto console di Visual Studio 
> * Aggiungere il metodo per chiamare l'API di Language Understanding per aggiungere l'espressione ed eseguire il training dell'app
> * Aggiungere il file JSON con espressioni di esempio per la finalità BookFlight
> * Eseguire la console ed esaminare lo stato del training per le espressioni

Per altre informazioni, vedere la documentazione tecnica per le API di [aggiunta dell'espressione di esempio alla finalità](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) e [stato del training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Per questo articolo è necessario un account gratuito di [Language Understanding][LUIS] per creare la propria applicazione.

## <a name="prerequisites"></a>prerequisiti

* [Ruby](http://rubyinstaller.org/) 
* **[Chiave di creazione](luis-concept-keys.md#authoring-key)** di Language Understanding. È possibile trovare questa chiave in Account Settings (Impostazioni account) nel sito Web di [Language Understanding](luis-reference-regions.md).
* [**ID dell'applicazione**](./luis-get-started-create-app.md) di Language Understanding esistente. L'ID applicazione viene visualizzato nel dashboard delle applicazioni. L'applicazione di Language Understanding con le finalità e le entità usate nel file `utterances.json` deve esistere prima di eseguire il codice in `add-utterances.rb`. Il codice riportato in questo articolo non crea le finalità e le entità. Aggiunge solo le espressioni per le entità e le finalità esistenti. 
* **ID della versione** all'interno dell'applicazione che riceve le espressioni. L'ID predefinito è "0.1"
* Creare un nuovo file denominato `add-utterances.rb` in VSCode.

> [!NOTE] 
> Il file `add-utterances.cs` completo e un file `utterances.json` di esempio sono disponibili nel [repository GitHub **LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/ruby/).


## <a name="write-the-ruby-code"></a>Scrivere il codice Ruby

Aggiungere le dipendenze al file.

   [!code-ruby[Ruby and LUIS Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=1-28 "Ruby and LUIS Dependencies")]

Aggiungere la richiesta GET usata per lo stato del training.

   [!code-ruby[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=30-40 "SendGet")]

Aggiungere la richiesta POST usata per creare le espressioni o avviare il training. 

   [!code-ruby[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=42-54 "SendPost")]

Aggiungere la funzione `AddUtterances`.

   [!code-ruby[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=56-61 "AddUtterances method")]


Aggiungere la funzione `Train`. 

   [!code-ruby[Train](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=63-69 "Train")]

Aggiungere la funzione `Status`.

   [!code-ruby[Status](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=71-75 "Status")]

Per gestire gli argomenti, aggiungere il codice principale.

   [!code-ruby[Main code](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=77-93 "Main code")]

## <a name="specify-utterances-to-add"></a>Specificare le espressioni da aggiungere
Creare e modificare il file `utterances.json` per specificare la **matrice di espressioni** da aggiungere all'app di Language Understanding. La finalità e le entità **devono** essere già presenti nell'app di Language Understanding.

> [!NOTE]
> L'applicazione di Language Understanding con le finalità e le entità usate nel file `utterances.json` deve esistere prima di eseguire il codice in `add-utterances.rb`. Il codice riportato in questo articolo non crea le finalità e le entità. Aggiunge solo le espressioni per le entità e le finalità esistenti.

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

Eseguire l'applicazione da una riga di comando con Ruby.

La chiamata ad `add-utterances.rb` con solo utterance.json come argomento aggiunge, ma non esegue il training di Language Understanding per le nuove espressioni.
````
> ruby add-utterances.rb ./utterances.json
````

Questo risultato visualizza i risultati dalla chiamata all'API di aggiunta delle espressioni. Il campo `response` è in questo formato per le espressioni aggiunte. `hasError` è false, a indicare che l'espressione è stata aggiunta.  

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
Chiamare add-utterance con l'argomento `-train` per inviare una richiesta di training.

````
> ruby add-utterances.rb ./utterances.json -train
````

> [!NOTE]
> Le espressioni duplicate non vengono aggiunte di nuovo, ma non causano un errore. `response` contiene l'ID dell'espressione originale.

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
Chiamare l'esempio con l'argomento `-status` per verificare lo stato del training.

````
> ruby add-utterances.rb ./utterances.json -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine dell'esercitazione, rimuovere Visual Studio e l'applicazione console se non sono più necessari.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Compilare un'app di Language Understanding a livello di codice](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
