---
title: "Guida introduttiva: Come aggiungere espressioni a un'app LUIS con JavaScript - Servizi cognitivi di Azure | Microsoft Docs"
description: In questa guida introduttiva si apprende come chiamare un'app LUIS con JavaScript.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 0920a194d3e9c93883b88b7131f7e81dc8fb3302
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159691"
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
