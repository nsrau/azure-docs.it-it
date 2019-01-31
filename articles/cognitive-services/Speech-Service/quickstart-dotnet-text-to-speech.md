---
title: 'Avvio rapido: Eseguire la sintesi vocale, .NET Core - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva, si apprenderà come eseguire la conversione di sintesi vocale con l'API REST di sintesi vocale. Il testo di esempio incluso in questa guida è strutturato come SSML (Speech Synthesis Markup Language). Questo formato consente di scegliere la voce e la lingua della risposta di sintesi vocale.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 9e0b71acc4faa97bf1aa82e56a3784c349663dc0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207106"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Avvio rapido: Eseguire la sintesi vocale con .NET Core

In questa guida introduttiva, si apprenderà come eseguire la conversione di sintesi vocale usando l'API REST di sintesi vocale .NET Core. Il testo di esempio incluso in questa guida è strutturato come [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md), che consente di scegliere la voce e la lingua della risposta.

Per questo avvio rapido è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa del servizio Voce. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [ASP.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per il servizio Voce

## <a name="create-a-net-core-project"></a>Creare un progetto .NET Core

Aprire un nuovo prompt dei comandi (o una nuova sessione del terminale) ed eseguire i comandi seguenti:

```console
dotnet new console -o tts-sample
cd tts-sample
```

Il primo comando esegue due operazioni. Consente di creare una nuova applicazione console .NET e di creare una directory denominata `tts-sample`. Il secondo comando consente di passare alla directory del progetto.

## <a name="select-the-c-language-version"></a>Selezionare la versione del linguaggio C#

Questa guida introduttiva richiede C# 7.1 o versione successiva. Esistono alcuni modi per modificare la versione C# per il progetto. In questa Guida, vi mostreremo come regolare il file `tts-sample.csproj`. Per tutte le opzioni disponibili, come ad esempio cambiare lingua in Visual Studio, vedere [Selezionare la versione del linguaggio C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Aprire il progetto, quindi aprire `tts-sample.csproj`. Assicurarsi che `LangVersion` sia impostato su 7.1 o versione successiva. Se non esiste un gruppo di proprietà per la versione del linguaggio, aggiungere le righe seguenti:

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Assicurarsi di salvare le modifiche.

## <a name="add-required-namespaces-to-your-project"></a>Aggiungere gli spazi dei nomi obbligatori al progetto

Il comando `dotnet new console`, eseguito in precedenza, ha consentito di creare un progetto, incluso il file `Program.cs`. Si tratta del file in cui verrà inserito il codice dell'applicazione. Aprire il file `Program.cs` e sostituire le istruzioni using seguenti. Queste istruzioni assicurano l'accesso a tutti i tipi necessari per compilare ed eseguire l'app di esempio.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Creare una classe per lo scambio di token

L'API REST di sintesi vocale necessita di un token di accesso per l'autenticazione. Per ottenere un token di accesso, è necessario uno scambio. In questo esempio viene scambiata la chiave di sottoscrizione del servizio Voce con un token di accesso usando l'endpoint `issueToken`.

L'esempio presuppone che la sottoscrizione del servizio Voce sia nell'area Stati Uniti occidentali. Se si usa un'area diversa, aggiornare il valore per `FetchTokenUri`. Per un elenco completo, vedere [Aree](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Per altre informazioni sull'autenticazione, vedere [Eseguire l'autenticazione con un token di autenticazione](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Ottenere un token di accesso e impostare l'URL dell'host

Individuare `static void Main(string[] args)` e sostituirlo con `static async Task Main(string[] args)`.

Copiare quindi il codice seguente nel metodo principale. Esegue alcune operazioni, ma soprattutto, prende il testo come un input e richiama la funzione `Authentication` per scambiare la chiave di sottoscrizione per un token di accesso. Se si verificano problemi, l'errore è stampato nella console.

Assicurarsi di aggiungere la chiave di sottoscrizione prima di eseguire l'app.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Impostare quindi l'host e la route per la sintesi vocale:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Compilare la richiesta SSML

Il testo viene inviato come corpo di una richiesta `POST`. Con SSML, è possibile specificare la voce e la lingua. In questa guida introduttiva si userà SSML con la lingua impostata su `en-US` e la voce impostata su `ZiraRUS`. È possibile costruire l'elemento SSML per la richiesta:

```csharp
string body = @"<speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> In questo esempio viene utilizzato il carattere voce `ZiraRUS`. Per un elenco completo di voci e lingue fornite da Microsoft, vedere [Supporto per le lingue](language-support.md). Se si è interessati alla creazione di una voce unica, riconoscibile per il proprio marchio, vedere [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Creare un'istanza del client, effettuare una richiesta e salvare l'audio sintetizzato in un file

Vi sono molte attività in questo esempio di codice. È opportuno esaminare rapidamente ciò che accade:

* Viene creata un'istanza per la richiesta e il client.
* Il metodo HTTP è impostato come `POST`.
* Le intestazioni obbligatorie vengono aggiunte alla richiesta.
* La richiesta viene inviata e viene controllato il codice di stato.
* La risposta viene letta in modo asincrono e scritta in un file denominato sample.wav.

Copiare questo codice nel progetto. Assicurarsi di sostituire il valore dell'intestazione `User-Agent` con il nome della risorsa dal portale di Azure.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di sintesi vocale. Dalla riga di comando (o nella sessione del terminale) passare alla directory del progetto ed eseguire:

```console
dotnet run
```

Se l'operazione riesce, il file di riconoscimento vocale viene salvato nella cartella di progetto. Riprodurlo con il lettore multimediale preferito.

## <a name="clean-up-resources"></a>Pulire le risorse

Se la chiave di sottoscrizione è stata impostata come hardcoded nel programma, assicurarsi di rimuoverla al termine di questa guida introduttiva.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi .NET su GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Vedere anche 

* [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md)
* [Esempi di campioni vocali per la creazione di una voce personalizzata](record-custom-voice-samples.md)
