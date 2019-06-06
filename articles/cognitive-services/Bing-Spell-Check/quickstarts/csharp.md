---
title: "Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e C#"
titlesuffix: Azure Cognitive Services
description: Introduzione all'uso dell'API REST Controllo ortografico Bing per controllare l'ortografia e la grammatica.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: e7a1f2572296015aac2d05b36b9b659c85586ff9
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390254"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e C#

Usare questa guida introduttiva per effettuare la prima chiamata all'API REST Controllo ortografico Bing. Questa semplice applicazione C# invia una richiesta all'API e restituisce un elenco di correzioni suggerite. L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://www.visualstudio.com/downloads/).
* Per installare `Newtonsoft.Json` come pacchetto NuGet in Visual Studio:
    1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul file della soluzione.
    1. Scegliere **Gestisci pacchetti NuGet per la soluzione**.
    1. Cercare `Newtonsoft.Json` e installare il pacchetto.
* Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. Creare una nuova soluzione console denominata `SpellCheckSample` in Visual Studio. Aggiungere quindi gli spazi dei nomi seguenti nel file di codice principale.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Creare variabili per l'endpoint dell'API, la chiave di sottoscrizione e il testo da sottoporre a controllo ortografico.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Creare una variabile per i parametri di ricerca. Aggiungere il codice di mercato dopo `mkt=`. Il codice di mercato è il paese da cui si effettua la richiesta. Aggiungere anche la modalità di controllo ortografico dopo `&mode=`. La modalità è `proof` (individua la maggior parte degli errori di ortografia/grammatica) o `spell` (individua la maggior parte degli errori di ortografia, ma meno errori di grammatica).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Creare e inviare una richiesta di controllo ortografico

1. Creare una funzione asincrona denominata `SpellCheck()` per inviare una richiesta all'API. Creare un oggetto `HttpClient` e aggiungere la chiave della sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`. Quindi eseguire i passaggi seguenti all'interno della funzione.

    ```csharp
    async static void SpellCheck()
    {
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = new HttpResponseMessage();
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Creare l'URI per la richiesta aggiungendo l'host, il percorso e i parametri.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Creare un elenco con un oggetto `KeyValuePair` contenente il testo e usarlo per creare un oggetto `FormUrlEncodedContent`. Impostare le informazioni dell'intestazione e usare `PostAsync()` per inviare la richiesta.

    ```csharp
    List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
    values.Add(new KeyValuePair<string, string>("text", text));
    
    using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
    {
        content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
        response = await client.PostAsync(uri, content);
    }
    ```

## <a name="get-and-print-the-api-response"></a>Ottenere e stampare la risposta dell'API

### <a name="get-the-client-id-header"></a>Ottenere l'intestazione dell'ID client

Se la risposta contiene un'intestazione `X-MSEdge-ClientID`, ottenere il valore e stamparlo.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Ottenere la risposta

Ottenere la risposta dall'API. Deserializzare l'oggetto JSON e stamparlo nella console.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Chiamare la funzione di controllo ortografico

Nella funzione Main del progetto chiamare `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>Risposta JSON di esempio

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../tutorials/spellcheck.md)

- [Informazioni sull'API Controllo ortografico Bing](../overview.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
