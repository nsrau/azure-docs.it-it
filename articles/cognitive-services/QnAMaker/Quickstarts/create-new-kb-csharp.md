---
title: Guida introduttiva alla creazione di una nuova knowledge base in C# per l'API QnA Maker Microsoft (v4) - Servizi cognitivi di Azure | Microsoft Docs
description: Creare una knowledge base in C# in cui includere le domande frequenti o i manuali del prodotto, in modo da poter iniziare a usare QnA Maker.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: 16d17a335bb84212497010b3eeb930582fa86dc4
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "43771276"
---
# <a name="create-a-new-knowledge-base-in-c"></a>Creare una nuova knowledge base in C#

Questa guida introduttiva illustra come creare a livello di codice una knowledge base di esempio per QnA Maker, che verrà visualizzata nel dashboard di Azure relativo all'account delle API Servizi cognitivi.

Di seguito sono indicati due URL di domande frequenti di esempio (identificati da "urls" nella stringa kb). QnA Maker estrae automaticamente domande e risposte da contenuto semistrutturato, come le domande frequenti, in base a quanto illustrato più in dettaglio in questo documento sulle [origini dati](../Concepts/data-sources-supported.md). In questa guida introduttiva è anche possibile usare URL di domande frequenti personalizzati.

## <a name="prerequisites"></a>Prerequisiti

Se l'ambiente di sviluppo integrato preferito è Visual Studio, per eseguire questo esempio di codice in Windows è necessario [Visual Studio 2017](https://www.visualstudio.com/downloads/). È possibile usare la versione gratuita Community Edition.

È necessario avere un [account delle API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con **QnA Maker** selezionato come risorsa. È infine necessaria una chiave di sottoscrizione a pagamento del nuovo account configurata nel [dashboard di Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices). Per recuperare la chiave, selezionare **Chiavi** in **GESTIONE RISORSE** nel dashboard. Una delle due chiavi sarà utilizzabile per questa guida introduttiva.

![Chiave del servizio nel dashboard di Azure](../media/sub-key.png)

## <a name="create-knowledge-base"></a>Creare la knowledge base

Il codice seguente crea una nuova knowledge base usando il metodo [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff).

1. Creare una nuova applicazione console .NET Framework C# nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore `key` con la propria chiave di sottoscrizione valida.
4. Eseguire il programma.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        // Represents the various elements used to create HTTP request URIs
        // for QnA Maker operations.
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/create";

        // NOTE: Replace this value with a valid QnA Maker subscription key.
        static string key = "YOUR SUBSCRIPTION KEY HERE";

        /// <summary>
        /// Defines the data source used to create the knowledge base.
        /// The data source includes a QnA pair, with metadata, 
        /// the URL for the QnA Maker FAQ article, and 
        /// the URL for the Azure Bot Service FAQ article.
        /// </summary>
        static string kb = @"
{
  'name': 'QnA Maker FAQ',
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your knowledge base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my knowledge base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ],
  'urls': [
    'https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs',
    'https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq'
  ],
  'files': []
}
";

        /// <summary>
        /// Represents the HTTP response returned by an HTTP request.
        /// </summary>
        public struct Response
        {
            public HttpResponseHeaders headers;
            public string response;

            public Response(HttpResponseHeaders headers, string response)
            {
                this.headers = headers;
                this.response = response;
            }
        }

        /// <summary>
        /// Formats and indents JSON for display.
        /// </summary>
        /// <param name="s">The JSON to format and indent.</param>
        /// <returns>A string containing formatted and indented JSON.</returns>
        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        /// <summary>
        /// Asynchronously sends a POST HTTP request.
        /// </summary>
        /// <param name="uri">The URI of the HTTP request.</param>
        /// <param name="body">The body of the HTTP request.</param>
        /// <returns>A <see cref="System.Threading.Tasks.Task{TResult}(QnAMaker.Program.Response)"/> 
        /// object that represents the HTTP response."</returns>
        async static Task<Response> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        /// <summary>
        /// Asynchronously sends a GET HTTP request.
        /// </summary>
        /// <param name="uri">The URI of the HTTP request.</param>
        /// <returns>A <see cref="System.Threading.Tasks.Task{TResult}(QnAMaker.Program.Response)"/> 
        /// object that represents the HTTP response."</returns>
        async static Task<Response> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        /// <summary>
        /// Creates a knowledge base.
        /// </summary>
        /// <param name="kb">The data source for the knowledge base.</param>
        /// <returns>A <see cref="System.Threading.Tasks.Task{TResult}(QnAMaker.Program.Response)"/> 
        /// object that represents the HTTP response."</returns>
        /// <remarks>The method constructs the URI to create a knowledge base in QnA Maker, and then
        /// asynchronously invokes the <see cref="QnAMaker.Program.Post(string, string)"/> method
        /// to send the HTTP request.</remarks>
        async static Task<Response> PostCreateKB(string kb)
        {
            // Builds the HTTP request URI.
            string uri = host + service + method;
            
            // Writes the HTTP request URI to the console, for display purposes.
            Console.WriteLine("Calling " + uri + ".");

            // Asynchronously invokes the Post(string, string) method, using the
            // HTTP request URI and the specified data source.
            return await Post(uri, kb);
        }

        /// <summary>
        /// Gets the status of the specified QnA Maker operation.
        /// </summary>
        /// <param name="operation">The QnA Maker operation to check.</param>
        /// <returns>A <see cref="System.Threading.Tasks.Task{TResult}(QnAMaker.Program.Response)"/> 
        /// object that represents the HTTP response."</returns>
        /// <remarks>The method constructs the URI to get the status of a QnA Maker operation, and
        /// then asynchronously invokes the <see cref="QnAMaker.Program.Get(string)"/> method
        /// to send the HTTP request.</remarks>
        async static Task<Response> GetStatus(string operation)
        {
            // Builds the HTTP request URI.
            string uri = host + service + operation;

            // Writes the HTTP request URI to the console, for display purposes.
            Console.WriteLine("Calling " + uri + ".");

            // Asynchronously invokes the Get(string) method, using the
            // HTTP request URI.
            return await Get(uri);
        }

        /// <summary>
        /// Creates a knowledge base, periodically checking status 
        /// until the knowledge base is created.
        /// </summary>
        async static void CreateKB()
        {
            try
            {
                // Starts the QnA Maker operation to create the knowledge base.
                var response = await PostCreateKB(kb);

                // Retrieves the operation ID, so the operation's status can be
                // checked periodically.
                var operation = response.headers.GetValues("Location").First();

                // Displays the JSON in the HTTP response returned by the 
                // PostCreateKB(string) method.
                Console.WriteLine(PrettyPrint(response.response));

                // Iteratively gets the state of the operation creating the
                // knowledge base. Once the operation state is set to something other
                // than "Running" or "NotStarted", the loop ends.
                var done = false;
                while (true != done)
                {
                    // Gets the status of the operation.
                    response = await GetStatus(operation);

                    // Displays the JSON in the HTTP response returned by the
                    // GetStatus(string) method.
                    Console.WriteLine(PrettyPrint(response.response));

                    // Deserialize the JSON into key-value pairs, to retrieve the
                    // state of the operation.
                    var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

                    // Gets and checks the state of the operation.
                    String state = fields["operationState"];
                    if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
                    {
                        // QnA Maker is still creating the knowledge base. The thread is 
                        // paused for a number of seconds equal to the Retry-After header value,
                        // and then the loop continues.
                        var wait = response.headers.GetValues("Retry-After").First();
                        Console.WriteLine("Waiting " + wait + " seconds...");
                        Thread.Sleep(Int32.Parse(wait) * 1000);
                    }
                    else
                    {
                        // QnA Maker has completed creating the knowledge base. 
                        done = true;
                    }
                }
            }
            catch
            {
                // An error occurred while creating the knowledge base. Ensure that
                // you included your QnA Maker subscription key where directed in the sample.
                Console.WriteLine("An error occurred while creating the knowledge base.");
            }
            finally
            {
                Console.WriteLine("Press any key to continue.");
            }

        }

        static void Main(string[] args)
        {
            // Invoke the CreateKB() method to create a knowledge base, periodically 
            // checking the status of the QnA Maker operation until the 
            // knowledge base is created.
            CreateKB();

            // The console waits for a key to be pressed before closing.
            Console.ReadLine();
        }
    }
}
  
```

## <a name="understand-what-qna-maker-returns"></a>Comprendere i risultati restituiti da QnA Maker

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente. I risultati possono variare leggermente. Se la chiamata finale restituisce uno stato "Succeeded", significa che la knowledge base è stata creata. Per risolvere eventuali problemi, vedere la pagina [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (Ottenere i dettagli dell'operazione) relativa all'API QnA Maker.

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-06-25T10:30:15Z",
  "lastActionTimestamp": "2018-06-25T10:30:15Z",
  "userId": "0d85ec291c204197a70cfec51725cd22",
  "operationId": "d9d40918-01bd-49f4-88b4-129fbc434c94"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-06-25T10:30:15Z",
  "lastActionTimestamp": "2018-06-25T10:30:15Z",
  "userId": "0d85ec291c184197a70cfeb51025cd22",
  "operationId": "d9d40918-01bd-49f4-88b4-129fbc434c94"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-06-25T10:30:15Z",
  "lastActionTimestamp": "2018-06-25T10:30:15Z",
  "userId": "0d85ec221c284197a70gfeb51725cd22",
  "operationId": "d9d40918-01bd-49f4-88b4-129fbc434c94"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-06-25T10:30:15Z",
  "lastActionTimestamp": "2018-06-25T10:30:51Z",
  "resourceLocation": "/knowledgebases/1d9eb2a1-de2a-4709-91b2-f6ea8afb6fb9",
  "userId": "0d85ec294c284197a70cfeb51775cd22",
  "operationId": "d9d40918-01bd-49f4-88b4-129fbc434c94"
}
Press any key to continue.
```

Dopo aver creato la knowledge base, è possibile visualizzarla nella pagina [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Knowledge base personali) del portale di QnA Maker. Selezionare il nome della knowledge base, ad esempio QnA Maker FAQ, per visualizzare le informazioni.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
