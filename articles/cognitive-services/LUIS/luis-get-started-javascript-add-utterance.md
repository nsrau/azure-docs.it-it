---
title: Guida introduttiva di JavaScript - modificare il modello ed eseguire il training dell'app LUIS
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si apprende come chiamare un'app LUIS con JavaScript.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 38e25b0634b53f4fcc0507091e78ab49b29c8d38
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033397"
---
# <a name="quickstart-change-model-using-javascript"></a>Guida introduttiva: Modificare il modello con JavaScript

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>File JSON delle espressioni di esempio

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>Creare il codice della guida introduttiva

Creare `add-utterances.html` e aggiungere il codice seguente:

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>Eseguire il codice

1. Aprire il file in un browser.

2. Aggiungere l'ID di creazione LUIS o l'ID dell'applicazione LUIS.

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
Dopo aver completato la procedura illustrata, rimuovere tutti i file creati in questa guida introduttiva. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Integrare un bot con Language Understanding](luis-csharp-tutorial-build-bot-framework-sample.md)
