---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 64a934196bb964561f36b9d95a2467b149847225
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906660"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Creare un progetto .NET Core

Aprire un nuovo prompt dei comandi (o una nuova sessione del terminale) ed eseguire i comandi seguenti:

```console
dotnet new console -o translate-sample
cd translate-sample
```

Il primo comando esegue due operazioni. Consente di creare una nuova applicazione console .NET e di creare una directory denominata `translate-sample`. Il secondo comando consente di passare alla directory del progetto.

In seguito sarà necessario installare Json.NET. Dalla directory del progetto eseguire:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selezionare la versione del linguaggio C#

Questa guida introduttiva richiede C# 7.1 o versione successiva. Esistono alcuni modi per modificare la versione C# per il progetto. In questa Guida, vi mostreremo come regolare il file `translate-sample.csproj`. Per tutte le opzioni disponibili, come ad esempio cambiare lingua in Visual Studio, vedere [Selezionare la versione del linguaggio C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Aprire il progetto, quindi aprire `translate-sample.csproj`. Assicurarsi che `LangVersion` sia impostato su 7.1 o versione successiva. Se non esiste un gruppo di proprietà per la versione del linguaggio, aggiungere le righe seguenti:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Aggiungere gli spazi dei nomi obbligatori al progetto

Il comando `dotnet new console`, eseguito in precedenza, ha consentito di creare un progetto, incluso il file `Program.cs`. Si tratta del file in cui verrà inserito il codice dell'applicazione. Aprire il file `Program.cs` e sostituire le istruzioni using seguenti. Queste istruzioni assicurano l'accesso a tutti i tipi necessari per compilare ed eseguire l'app di esempio.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Creare le classi per la risposta JSON

Successivamente, si creerà un insieme di classi usate per la deserializzazione della risposta JSON restituita dall'API Traduzione testuale.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TranslationResult
{
    public DetectedLanguage DetectedLanguage { get; set; }
    public TextResult SourceText { get; set; }
    public Translation[] Translations { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}

public class TextResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}

public class Translation
{
    public string Text { get; set; }
    public TextResult Transliteration { get; set; }
    public string To { get; set; }
    public Alignment Alignment { get; set; }
    public SentenceLength SentLen { get; set; }
}

public class Alignment
{
    public string Proj { get; set; }
}

public class SentenceLength
{
    public int[] SrcSentLen { get; set; }
    public int[] TransSentLen { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Ottenere informazioni sulla sottoscrizione dalle variabili di ambiente

Aggiungere le righe seguenti alla classe `Program`. Queste righe leggono la chiave e l'endpoint della sottoscrizione dalle variabili di ambiente e generano un errore se si verificano problemi.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-translate-text"></a>Creare una funzione per tradurre il testo

Nella classe `Program` creare una funzione asincrona denominata `TranslateTextRequest()`. Questa funzione accetta quattro argomenti: `subscriptionKey`, `host`, `route` e `inputText`.

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator Text API
static public async Task TranslateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serializzare la richiesta di traduzione

In seguito, è necessario creare e serializzare l'oggetto JSON che include il testo da tradurre. Tenere presente che è possibile passare più di un oggetto nel `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
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
* Stampare la risposta usando le classi create in precedenza

Aggiungere questo codice a `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
TranslationResult[] deserializedOutput = JsonConvert.DeserializeObject<TranslationResult[]>(result);
// Iterate over the deserialized results.
foreach (TranslationResult o in deserializedOutput)
{
    // Print the detected input language and confidence score.
    Console.WriteLine("Detected input language: {0}\nConfidence score: {1}\n", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    // Iterate over the results and print each translation.
    foreach (Translation t in o.Translations)
    {
        Console.WriteLine("Translated to {0}: {1}", t.To, t.Text);
    }
}
```

Se si usa una sottoscrizione multiservizio di Servizi cognitivi, è necessario includere anche `Ocp-Apim-Subscription-Region` nei parametri della richiesta. [Informazioni sull'autenticazione con la sottoscrizione multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

L'ultimo passaggio consiste nel chiamare `TranslateTextRequest()` nella funzione `Main`. In questo esempio, si sta eseguendo la traduzione verso il tedesco (`de`), l'italiano (`it`), il giapponese (`ja`) e il tailandese (`th`). Individuare `static void Main(string[] args)` e sostituirlo con il codice seguente:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate
    string route = "/translate?api-version=3.0&to=de&to=it&to=ja&to=th";
    // Prompts you for text to translate. If you'd prefer, you can
    // provide a string as textToTranslate.
    Console.Write("Type the phrase you'd like to translate? ");
    string textToTranslate = Console.ReadLine();
    await TranslateTextRequest(subscriptionKey, endpoint, route, textToTranslate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Si noterà che in `Main` si sta dichiarando `subscriptionKey`, `endpoint` e `route`. Inoltre, si richiede l'input all'utente con `Console.Readline()` e si assegna il valore a `textToTranslate`.

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla directory del progetto ed eseguire:

```console
dotnet run
```

## <a name="sample-response"></a>Risposta di esempio

Dopo aver eseguito l'esempio, nel terminale si dovrebbe visualizzare quanto segue:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Questo messaggio viene compilato dal file JSON non elaborato, che avrà un aspetto simile al seguente:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      },
      {
        "text": "ハローワールド！",
        "to": "ja"
      },
      {
        "text": "หวัดดีชาวโลก!",
        "to": "th"
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le informazioni di riferimento sulle API per conoscere quali operazioni è possibile eseguire con l'API Traduzione testuale.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
