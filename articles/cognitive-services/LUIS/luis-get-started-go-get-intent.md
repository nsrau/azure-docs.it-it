---
title: 'Guida introduttiva: Ottenere la finalità, Go - LUIS'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido su Go si userà un'app LUIS disponibile pubblicamente per determinare la finalità di un utente da un testo discorsivo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 1fc36b8e1240751ce85d61c761cd069dcdad207e
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307551"
---
# <a name="quickstart-get-intent-using-go"></a>Avvio rapido: Ottenere la finalità tramite Go

In questa guida introduttiva si passano le espressioni a un endpoint LUIS per ottenere la restituzione di finalità ed entità.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prerequisiti

* Linguaggio di programmazione [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Ottenere la finalità tramite browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Ottenere la finalità a livello di codice

È possibile usare Go per accedere agli stessi risultati visualizzati nella finestra del browser nel passaggio precedente. 

1. Creare un file denominato `endpoint.go`. Aggiungere il codice seguente:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. Da un prompt dei comandi nella stessa directory in cui è stato creato il file immettere `go build endpoint.go` per compilare il file di Go. Il prompt dei comandi non restituisce informazioni relative all'avvenuta compilazione.

3. Eseguire l'applicazione Go alla riga di comando immettendo il testo seguente nel prompt dei comandi: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Sostituire `<add-your-key>` con il valore della chiave.  
    
    Al prompt dei comandi viene visualizzata la risposta seguente: 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse
Chiudere il file di Go e rimuoverlo dal file system. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Aggiungere espressioni](luis-get-started-go-add-utterance.md)
