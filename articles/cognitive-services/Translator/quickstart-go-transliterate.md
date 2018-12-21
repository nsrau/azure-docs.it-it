---
title: 'Avvio rapido: Convertire uno script di testo, Go - API Traduzione testuale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si converte il testo in una lingua da un sistema di scrittura a un altro usando l'API Traduzione testuale con Go.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: erhopf
ms.openlocfilehash: 6b86d94e53b1ecb7a0d0d7b1f325a425f05c9e4f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993300"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-go"></a>Avvio rapido: Usare l'API Traduzione testuale per traslitterare un testo con Go

In questa guida introduttiva si converte il testo in una lingua da un sistema di scrittura a un altro usando l'API Traduzione testuale.

Per questa guida introduttiva è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa Traduzione testuale. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Go](https://golang.org/doc/install)
* Una chiave di sottoscrizione di Azure per Traduzione testuale

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Go tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `transliterate-text.go`.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Creare la funzione main

Questo esempio proverà a leggere la chiave di sottoscrizione di Traduzione testuale dalla variabile di ambiente `TRANSLATOR_TEXT_KEY`. Se non si ha familiarità con le variabili di ambiente, è possibile impostare `subscriptionKey` come stringa e l'istruzione condizionale come commento.

Copiare questo codice nel progetto:

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our transliterate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    transliterate(subscriptionKey)
}
```

## <a name="create-a-function-to-transliterate-text"></a>Creare una funzione per traslitterare il testo

Creare una funzione per traslitterare il testo. Questa funzione accetta un singolo argomento, la chiave di sottoscrizione del servizio Traduzione testuale.

```go
func transliterate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Creare quindi l'URL. Per creare l'URL vengono usati i metodi `Parse()` e `Query()`. È possibile notare che vengono aggiunti parametri con il metodo `Add()`. In questo esempio viene traslitterato un testo dal giapponese all'alfabeto latino.

Copiare questo codice nella funzione `transliterate`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0")
q := u.Query()
q.Add("language", "ja")
q.Add("fromScript", "jpan")
q.Add("toScript", "latn")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Per altre informazioni su endpoint, route e parametri della richiesta, vedere [API Traduzione testuale 3.0: Transliterate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

## <a name="create-a-struct-for-your-request-body"></a>Creare uno struct per il corpo della richiesta

Creare quindi una struttura anonima per il corpo della richiesta e codificarla in formato JSON con `json.Marshal()`. Aggiungere questo codice alla funzione `transliterate`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "こんにちは"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Compilare la richiesta

Ora che il corpo della richiesta è stato codificato in formato JSON, è possibile creare la richiesta POST e chiamare l'API Traduzione testuale.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>Gestire e stampare la risposta

Aggiungere questo codice alla funzione `transliterate` per decodificare la risposta JSON e quindi formattare e stampare il risultato.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è completata. È stato realizzato un semplice programma che chiamerà l'API Traduzione testuale e restituirà una risposta JSON. A questo punto, è possibile eseguire il programma:

```console
go run transliterate-text.go
```

Se si vuole confrontare il proprio codice con quello già disponibile, l'esempio completo è pubblicato su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Risposta di esempio

```json
[
  {
    "script": "latn",
    "text": "konnnichiha"
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare i pacchetti Go per le API Servizi cognitivi da [Azure SDK per Go](https://github.com/Azure/azure-sdk-for-go) su GitHub.

> [!div class="nextstepaction"]
> [Esaminare i pacchetti Go su GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Vedere anche 

Informazioni su come usare l'API Traduzione testuale per:

* [Tradurre un testo](quickstart-go-translate.md)
* [Identificare la lingua da un input](quickstart-go-detect.md)
* [Ottenere traduzioni alternative](quickstart-go-dictionary.md)
* [Ottenere un elenco di lingue supportate](quickstart-go-languages.md)
* [Determinare la lunghezza delle frasi da un input](quickstart-go-sentences.md)
