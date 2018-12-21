---
title: "Avvio rapido: Generare un'anteprima - REST, Go - Visione artificiale"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva, si genererà l'anteprima di un'immagine usando l'API Visione artificiale con Go.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: a1a1daebaba7b5592a94bc90811a9ac272ec7971
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969550"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-go-in-computer-vision"></a>Avvio rapido: Generare un'anteprima con l'API REST e Go in Visione artificiale

In questa guida introduttiva si genererà l'anteprima di un'immagine usando l'API REST di Visione artificiale. Il metodo [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) consente di generare l'anteprima di un'immagine. Si specificano l'altezza e la larghezza, che possono essere diverse rispetto alle proporzioni dell'immagine di input. Visione artificiale usa il ritaglio intelligente per identificare l'area di interesse in modo intelligente e generare le coordinate di ritaglio in base a tale area.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver installato [Go](https://golang.org/dl/).
- È necessario avere una chiave di sottoscrizione per Visione artificiale. Per ottenere una chiave di sottoscrizione, vedere la sezione [Come ottenere chiavi di sottoscrizione](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor di testo.
1. Apportare le modifiche seguenti al codice, dove necessario:
    1. Sostituire il valore di `subscriptionKey` con la chiave di sottoscrizione.
    1. Se necessario, sostituire il valore di `uriBase` con l'URL endpoint per il metodo [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) dall'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
    1. Facoltativamente, sostituire il valore di `imageUrl` con l'URL di un'altra immagine per la quale si vuole generare un'anteprima.
1. Salvare il codice in un file con estensione `.go`. Ad esempio: `get-thumbnail.go`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt, eseguire il comando `go build` per compilare il pacchetto dal file. Ad esempio: `go build get-thumbnail.go`.
1. Al prompt, eseguire il pacchetto compilato. Ad esempio: `get-thumbnail`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace <Subscription Key> with your valid subscription key.
    const subscriptionKey = "<Subscription Key>"

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo contiene i dati binari dell'immagine di anteprima. Se invece la richiesta ha esito negativo, la risposta contiene un codice di errore e un messaggio per determinarne la causa.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, eliminare il pacchetto compilato e il file da cui è stato compilato e quindi chiudere la finestra del prompt dei comandi e l'editor di testo.

## <a name="next-steps"></a>Passaggi successivi

Esaminare l'API Visione artificiale usata per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esplorare l'API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
