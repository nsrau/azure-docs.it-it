---
title: 'Avvio rapido: Rilevare la lingua del testo, C# - API Traduzione testuale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si apprenderà come rilevare la lingua del testo fornito con .NET Core e l'API REST Traduzione testuale.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: erhopf
ms.openlocfilehash: de5088a012a7f5fd210a5aa66805fe865cc09bbe
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889260"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-c"></a>Avvio rapido: Usare l'API Traduzione testuale per rilevare la lingua del testo con C#

In questa guida introduttiva si apprenderà come rilevare la lingua del testo fornito con .NET Core e l'API REST Traduzione testuale.

Per questa guida introduttiva è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa Traduzione testuale. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Pacchetto NuGet di Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per Traduzione testuale

## <a name="create-a-net-core-project"></a>Creare un progetto .NET Core

Aprire un nuovo prompt dei comandi (o una nuova sessione del terminale) ed eseguire i comandi seguenti:

```console
dotnet new console -o detect-sample
cd detect-sample
```

Il primo comando esegue due operazioni. Consente di creare una nuova applicazione console .NET e di creare una directory denominata `detect-sample`. Il secondo comando consente di passare alla directory del progetto.

In seguito sarà necessario installare Json.NET. Dalla directory del progetto eseguire:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Aggiungere gli spazi dei nomi obbligatori al progetto

Il comando `dotnet new console`, eseguito in precedenza, ha consentito di creare un progetto, incluso il file `Program.cs`. Si tratta del file in cui verrà inserito il codice dell'applicazione. Aprire il file `Program.cs` e sostituire le istruzioni using seguenti. Queste istruzioni assicurano l'accesso a tutti i tipi necessari per compilare ed eseguire l'app di esempio.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-detect-the-source-texts-language"></a>Creare una funzione per rilevare la lingua del testo di origine

Nella classe `Program` creare una funzione denominata `Detect`. Questa classe incapsula il codice usato per chiamare la risorsa Detect e stampa i risultati nella console.

```csharp
static void Detect()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Impostare la chiave di sottoscrizione, il nome host e il percorso

Aggiungere le righe seguenti alla funzione `Detect`.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/detect?api-version=3.0";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

In seguito, è necessario creare e serializzare l'oggetto JSON che include il testo che verrà sottoposto al rilevamento della lingua.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Salve mondo!" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Creare un'istanza del client ed effettuare una richiesta

Queste righe consentono di creare un'istanza di `HttpClient` e `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Costruire una richiesta e stampare la risposta

All'interno di `HttpRequestMessage` vengono eseguite le operazioni seguenti:

* Dichiarare il metodo HTTP
* Costruire l'URI della richiesta
* Inserire il corpo della richiesta (oggetto JSON serializzato)
* Aggiungere le intestazioni obbligatorie
* Effettuare una richiesta asincrona
* Stampare la risposta

Aggiungere questo codice a `HttpRequestMessage`:

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

L'ultimo passaggio consiste nel chiamare `Detect()` nella funzione `Main`. Individuare `static void Main(string[] args)` e aggiungere le righe seguenti:

```csharp
Detect();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di esempio. Dalla riga di comando (o nella sessione del terminale) passare alla directory del progetto ed eseguire:

```console
dotnet run
```

## <a name="sample-response"></a>Risposta di esempio

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Esaminare il codice di esempio per questa guida introduttiva e per altre, incluse quelle relative alla traduzione e alla traslitterazione, e anche altri progetti di esempio di Traduzione testuale su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Vedere anche 

* [Tradurre un testo](quickstart-csharp-translate.md)
* [Traslitterare testo](quickstart-csharp-transliterate.md)
* [Ottenere traduzioni alternative](quickstart-csharp-dictionary.md)
* [Ottenere un elenco di lingue supportate](quickstart-csharp-languages.md)
* [Determinare la lunghezza delle frasi da un input](quickstart-csharp-sentences.md)
