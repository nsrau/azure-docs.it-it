---
title: Ottenere il modello con una chiamata REST in Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: ec61abca19579426818e227687e08e66b73969cb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505852"
---
## <a name="prerequisites"></a>Prerequisiti

* Chiave di avvio.
* Importare l'app [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) dal repository GitHub cognitive-services-language-understanding.
* L'ID applicazione di LUIS per l'app TravelAgent importata. L'ID applicazione viene visualizzato nel dashboard delle applicazioni.
* L'ID della versione all'interno dell'applicazione che riceve le espressioni. L'ID predefinito è "0.1".
* Linguaggio di programmazione [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>File JSON delle espressioni di esempio

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Cambiare il modello a livello di codice

Usare Go per aggiungere all'applicazione un'[API](https://aka.ms/luis-apim-v3-authoring) entità basata su Machine Learning. 

1. Creare un file denominato `predict.go`. Aggiungere il codice seguente:
    
    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )
    
    // main function
    func main() {
    
        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"
    
        // NOTE: change to your starter key
        var authoringKey = "YOUR-KEY"
    
        // NOTE: change to your starter key's endpoint, for example, westus.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"  
    
        var version = "0.1"
    
        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `
    
        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)
    
        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)
    
        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }
    
    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){
    
        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)
    
        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){
    
        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){
    
        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)
    
        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){
    
        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){
    
        client := &http.Client{}
    
        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)
    
        fmt.Println("body")
        fmt.Println(body)
    
        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }    
    ```

1. Sostituire i valori seguenti:

    * `YOUR-KEY` con la chiave di avvio
    * `YOUR-ENDPOINT` con l'endpoint, ad esempio `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` con l'ID dell'app

1. Da un prompt dei comandi nella stessa directory in cui è stato creato il file immettere il comando seguente per compilare il file Go:

    ```console
    go build model.go
    ```  

1. Eseguire l'applicazione Go alla riga di comando immettendo il testo seguente nel prompt dei comandi: 

    ```console
    go run model.go
    ```

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, eliminare il file dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per un'app](../luis-concept-best-practices.md)