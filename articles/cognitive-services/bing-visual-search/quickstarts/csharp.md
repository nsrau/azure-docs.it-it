---
title: "Avvio rapido: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e C#"
titleSuffix: Azure Cognitive Services
description: Informazioni su come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 7961fb05f7ca9c6e6b61330e7dff53f2d5a41001
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535315"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Avvio rapido: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e C#

Usare questo argomento di avvio rapido per eseguire la prima chiamata all'API Ricerca visiva Bing e visualizzare i risultati della ricerca. Questa semplice applicazione C# carica un'immagine nell'API e visualizza le informazioni restituite.

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Il framework [Json.NET](https://www.newtonsoft.com/json), disponibile come pacchetto NuGet.
* Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. Creare una nuova soluzione console denominata `BingSearchApisQuickStart` in Visual Studio. Aggiungere quindi gli spazi dei nomi seguenti nel file di codice principale.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Aggiungere le variabili per la chiave di sottoscrizione, l'endpoint e il percorso dell'immagine da caricare.

    ```csharp
        const string accessKey = "<yoursubscriptionkeygoeshere>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<pathtoimagegoeshere>";
    ```


1. Creare un metodo denominato `GetImageFileName()` per ottenere il percorso per l'immagine
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

2. Creare un metodo per ottenere i caratteri binari dell'immagine.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Compilare i dati del modulo

Quando si carica un'immagine locale, i dati del modulo inviati all'API devono essere formattati correttamente. Devono includere l'intestazione Content-Disposition, il parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Per formattare i dati del modulo, aggiungere stringhe limite per la formattazione corretta dei dati del modulo POST, che determinano i caratteri di inizio, fine e nuova riga per i dati.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Le variabili seguenti saranno usate per aggiungere parametri ai dati del modulo. 

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Creare una funzione denominata `BuildFormDataStart()` per creare la parte iniziale dei dati del modulo necessari usando le stringhe limite e il percorso dell'immagine.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Creare una funzione denominata `BuildFormDataEnd()` per creare la parte finale dei dati del modulo necessari usando le stringhe limite.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Chiamare l'API Ricerca visiva Bing

1. Creare una funzione per chiamare l'endpoint Ricerca visiva Bing e restituire la risposta json. La funzione deve accettare le parti di inizio e fine per i dati del modulo, una matrice di byte che contiene i dati dell'immagine e un valore contentType.

2. Usare `WebRequest` per archiviare l'URI, il valore contentType e le intestazioni.  

3. Usare `request.GetRequestStream()` per scrivere i dati del modulo e dell'immagine. Ottenere quindi la risposta. La funzione dovrebbe essere simile al codice seguente:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Creare il metodo main

1. Nel metodo main dell'applicazione, ottenere il nome file e il file binario dell'immagine. 

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Impostare il corpo POST formattando il limite corrispondente. Quindi chiamare `startFormData()` e `endFormData` per creare i dati del modulo. 

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Creare il valore ContentType formattando `CONTENT_TYPE_HEADER_PARAMS` e il limite di dati del modulo.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Ottenere la risposta dell'API chiamando `BingImageSearch()`. Stampare la risposta.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Uso di HttpClient

Se si usa HttpClient, è possibile usare MultipartFormDataContent per compilare i dati di formato. Usare solo le sezioni di codice seguente per sostituire gli stessi metodi denominati nell'esempio precedente.

Sostituire il metodo Main con il codice seguente:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Sostituire il metodo BingImageSearch con il codice seguente:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](../tutorial-bing-visual-search-single-page-app.md)
