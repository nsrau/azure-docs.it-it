---
title: Avvio rapido per la libreria client Go di Suggerimenti automatici Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: c0129ff25f1df492ab6eba9f49add18d5321a3e8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88246426"
---
Introduzione alla libreria client di Suggerimenti automatici Bing per Go. Seguire questa procedura per installare la libreria e provare gli esempi di attività di base.

Usare la libreria client di Suggerimenti automatici Bing per Go per ottenere suggerimenti per la ricerca in base a stringhe di query parziali.

[Documentazione di riferimento](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Codice di esempio](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha già una sottoscrizione di Azure, è possibile [crearne una gratuitamente](https://azure.microsoft.com/free/cognitive-services).
* L'ultima versione di [Go](https://golang.org/dl/).

Iniziare a usare la libreria client di Suggerimenti automatici Bing creando una risorsa di Azure. Scegliere di seguito il tipo di risorsa più adatto alle proprie esigenze:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Creare variabili di ambiente

>[!NOTE]
> Gli endpoint per le risorse create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains).

Usando la chiave e l'endpoint della risorsa creata, creare due variabili di ambiente per l'autenticazione:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: la chiave della risorsa per l'autenticazione delle richieste.
* `AUTOSUGGEST_ENDPOINT`: l'endpoint della risorsa per l'invio delle richieste API. L'aspetto dovrà essere come questo: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`

Usare le istruzioni per il sistema operativo in uso.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Dopo aver aggiunto la variabile di ambiente, riavviare la finestra della console.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

### <a name="macos"></a>[macOS](#tab/unix)

Modificare `.bash_profile` e aggiungere la variabile di ambiente:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.
***

## <a name="create-a-new-go-project"></a>Creare un nuovo progetto Go

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a un'area di lavoro per il progetto Go. L'area di lavoro conterrà tre cartelle:

* **src**: questa directory contiene il codice sorgente e i pacchetti. Tutti i pacchetti installati con il comando `go get` saranno inclusi qui.
* **pkg**: questa directory contiene gli oggetti pacchetto Go compilati. Tutti i file hanno estensione `.a`.
* **bin**: questa directory contiene i file eseguibili binari creati durante l'esecuzione di `go install`.

> [!TIP]
> Acquisire familiarità con la struttura di un'[area di lavoro Go](https://golang.org/doc/code.html#Workspaces). Questa guida include informazioni per l'impostazione di `$GOPATH` e `$GOROOT`.

Creare un'area di lavoro denominata `my-app` e le sottodirectory necessarie per `src`, `pkg` e `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Installare la libreria client per Go

Ora installare la libreria client per Go:

```bash
$ go get -u <library-location-or-url>
```

oppure, se si usa DEP, all'interno del repository eseguire:

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Creare l'applicazione Go

Successivamente, creare un file denominato `src/sample-app.go`:

```bash
$ cd src
$ touch sample-app.go
```

Aprire `sample-app.go`, aggiungere il nome del pacchetto e importare le librerie seguenti:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Creare una funzione denominata `main`. Quindi creare le variabili di ambiente per la chiave e l'endpoint di Suggerimenti automatici Bing:

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Esempi di codice

Questi esempi di codice illustrano come completare le attività di base usando la libreria client di Suggerimenti automatici Bing per Go:

* [Autenticare il client](#authenticate-the-client)
* [Inviare una richiesta all'API](#send-an-api-request)

### <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo argomento di avvio rapido presuppone che sia stata [creata una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave di Suggerimenti automatici Bing denominata `BING_AUTOSUGGEST_SUBSCRIPTION_KEY` e una per l'endpoint denominato `BING_AUTOSUGGEST_ENDPOINT`.

Nella funzione `main()` creare un'istanza di un client con l'endpoint e la chiave.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>Inviare una richiesta all'API

Nello stesso metodo usare il metodo [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) del client per inviare una query a Bing. Scorrere quindi la risposta di [Suggerimenti](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) e stampare il primo suggerimento.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione Go immettendo il comando `go run [arguments]` dalla directory dell'applicazione.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Eliminare un gruppo di risorse nel portale di Azure](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Eliminare un gruppo di risorse nell'interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione per Suggerimenti automatici Bing](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Vedere anche

- [Informazioni su Suggerimenti automatici Bing](../../get-suggested-search-terms.md)
- [Riferimento sull'API Suggerimenti automatici Bing versione 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
