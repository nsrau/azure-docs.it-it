---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: efb314b788f7718bef1dbf1588c87ba133229552
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968232"
---
## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Go](https://golang.org/doc/install)
* Una chiave di sottoscrizione di Azure per Traduzione testuale

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Go usando l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `detect-language.go`.

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
     * want to use env variables. If so, be sure to delete the "os" import.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our detect function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    detect(subscriptionKey)
}
```

## <a name="create-a-function-to-detect-the-text-language"></a>Creare una funzione per rilevare la lingua del testo

Creare una funzione per rilevare la lingua del testo. Questa funzione accetta un singolo argomento, la chiave di sottoscrizione del servizio Traduzione testuale.

```go
func detect(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Successivamente, è possibile creare l'URL. Per creare l'URL vengono usati i metodi `Parse()` e `Query()`.

Copiare questo codice nella funzione `detect`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/detect?api-version=3.0")
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> Per altre informazioni su endpoint, route e parametri della richiesta, vedere [API Traduzione testuale 3.0: Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

## <a name="create-a-struct-for-your-request-body"></a>Creare uno struct per il corpo della richiesta

Creare quindi una struttura anonima per il corpo della richiesta e codificarla in formato JSON con `json.Marshal()`. Aggiungere questo codice alla funzione `detect`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Salve, Mondo!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Compilare la richiesta

Ora che il corpo della richiesta è stato codificato in formato JSON, è possibile compilare la richiesta POST e chiamare l'API Traduzione testuale.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Se si usa una sottoscrizione multiservizio di Servizi cognitivi, è necessario includere anche `Ocp-Apim-Subscription-Region` nei parametri della richiesta. [Informazioni sull'autenticazione con la sottoscrizione multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Gestire e stampare la risposta

Aggiungere questo codice alla funzione `detect` per decodificare la risposta JSON, quindi formattare e stampare il risultato.

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
go run detect-language.go
```

Se si vuole confrontare il proprio codice con quello già disponibile, l'esempio completo è pubblicato su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Risposta di esempio

Trovare l'abbreviazione del paese o dell'area geografica in questo [elenco di lingue](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
  {
    "alternatives": [
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "pt",
        "score": 1
      },
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "en",
        "score": 1
      }
    ],
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "language": "it",
    "score": 1
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare le informazioni di riferimento sulle API per conoscere quali operazioni è possibile eseguire con l'API Traduzione testuale.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
