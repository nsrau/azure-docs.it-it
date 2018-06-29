---
title: "Esercitazione: Come aggiungere espressioni a un'app di Language Understanding con JavaScript | Microsoft Docs"
description: Questa esercitazione illustra come chiamare un'app di Language Understanding con JavaScript.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265460"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>Esercitazione: Aggiungere espressioni all'app con JavaScript
In questa esercitazione si scrive un programma per aggiungere un'espressione a una finalità usando le API di creazione in Javascript.

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare un progetto console di Visual Studio 
> * Aggiungere il metodo per chiamare l'API di Language Understanding per aggiungere l'espressione ed eseguire il training dell'app
> * Aggiungere il file JSON con espressioni di esempio per la finalità BookFlight
> * Eseguire la console ed esaminare lo stato del training per le espressioni

Per altre informazioni, vedere la documentazione tecnica per le API di [aggiunta dell'espressione di esempio alla finalità](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) e [stato del training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Per questo articolo è necessario un account gratuito di [Language Understanding][LUIS] per creare la propria applicazione.

## <a name="prerequisites"></a>prerequisiti
* [**Chiave di creazione**](luis-concept-keys.md#authoring-key) di Language Understanding. 
* **ID applicazione** e **ID versione** dell'applicazione di Language Understanding esistente. 
* Un nuovo file denominato `add-utterances.html` in VSCode.

> [!NOTE] 
> Il file `add-utterances.html` completo è disponibile nel [**repository GitHub LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html).


## <a name="write-the-code"></a>Scrivere il codice
Creare `add-utterances.html` e aggiungere il codice seguente:

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>Visualizzare nel browser
1. Aprire il file in un browser.

2. Aggiungere l'ID creazione di Language Understanding, l'ID dell'applicazione di Language Understanding e modificare la versione se non è `0.1`.

3. Modificare la **matrice delle espressioni** da aggiungere all'applicazione. Le espressioni sono archiviate nella variabile utteranceJSON. Modificare questi valori per il proprio dominio e le esigenze relative alle espressioni. 

    ```json
    // example batch utterances
    var utteranceJSON = [
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
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. Selezionare il pulsante `Upload utterance`. I risultati di Language Understanding sono visualizzati sotto i pulsanti.

5. Selezionare il pulsante `Train model` per eseguire il training dell'applicazione con queste nuove espressioni.

6. Selezionare il pulsante `Train Status` per visualizzare lo stato del training. 

![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine dell'esercitazione, rimuovere Visual Studio e l'applicazione console se non sono più necessari. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Integrare un bot con Language Understanding](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website