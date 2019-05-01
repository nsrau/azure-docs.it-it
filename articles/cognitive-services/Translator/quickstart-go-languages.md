---
title: 'Avvio rapido: Ottenere le lingue supportate, Go: API Traduzione testuale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si ottiene un elenco di lingue supportate per la traduzione, la traslitterazione e la ricerca nei dizionari insieme a esempi usando l'API Traduzione testuale con Go.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 93cc20c303d6c8ba0592651064790d83919b2948
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916647"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-go"></a>Avvio rapido: Usare l'API Traduzione testuale per ottenere un elenco delle lingue supportate usando Go

In questa guida di avvio rapido si apprenderà come effettuare una richiesta GET che restituisce un elenco delle lingue supportate usando Go e l'API REST Traduzione testuale.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Go](https://golang.org/doc/install)

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Go usando l'IDE o l'editor preferito oppure creare una nuova cartella sul desktop. Copiare quindi questo frammento di codice nel progetto o nella cartella all'interno di un file denominato `get-languages.go`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)
```

## <a name="create-the-main-function"></a>Creare la funzione main

Creare la funzione main per l'applicazione. Come si può notare, si tratta di una singola riga di codice. Il motivo è che per ottenere e stampare l'elenco di lingue supportate per Traduzione testuale viene creata una singola funzione.

Copiare questo codice nel progetto:

```go
func main() {
    /*
     * This calls our getLanguages function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    getLanguages()
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>Creare una funzione per ottenere un elenco delle lingue supportate

È possibile creare una funzione per ottenere un elenco delle lingue supportate.

```go
func getLanguages() {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Successivamente, è possibile creare l'URL. Per creare l'URL vengono usati i metodi `Parse()` e `Query()`.

Copiare questo codice nella funzione `getLanguages`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/languages")
q := u.Query()
q.Add("api-version", "3.0")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Per altre informazioni su endpoint, route e parametri della richiesta, consultare [API Traduzione testuale 3.0: Lingue](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

## <a name="build-the-request"></a>Compilare la richiesta

Ora che il corpo della richiesta è stato codificato in formato JSON, è possibile compilare la richiesta POST e chiamare l'API Traduzione testuale.

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>Gestire e stampare la risposta

Aggiungere questo codice alla funzione `getLanguages` per decodificare la risposta JSON, quindi formattare e stampare il risultato.

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
go run get-languages.go
```

Se si vuole confrontare il proprio codice con quello già disponibile, l'esempio completo è pubblicato su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Risposta di esempio

Trovare l'abbreviazione del paese in questo [elenco di lingue](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
{
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  },
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare i pacchetti Go per le API Servizi cognitivi da [Azure SDK per Go](https://github.com/Azure/azure-sdk-for-go) su GitHub.

> [!div class="nextstepaction"]
> [Esaminare i pacchetti Go su GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Vedere anche 

Informazioni su come usare l'API Traduzione testuale per:

* [Tradurre un testo](quickstart-go-translate.md)
* [Traslitterare testo](quickstart-go-transliterate.md)
* [Identificare la lingua da un input](quickstart-go-detect.md)
* [Ottenere traduzioni alternative](quickstart-go-dictionary.md)
* [Determinare la lunghezza delle frasi da un input](quickstart-go-sentences.md)
