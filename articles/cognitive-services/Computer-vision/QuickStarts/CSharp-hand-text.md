---
title: API Visione artificiale - Guida introduttiva all'estrazione di testo scritto a mano con C# | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: In questa guida introduttiva si estrae testo scritto a mano da un'immagine usando Visione artificiale con C# in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 22836f3406f85a68322c7a8bae2a15cd897294e2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772379"
---
# <a name="quickstart-extract-handwritten-text---rest-c35"></a>Guida introduttiva: Estrarre testo scritto a mano - REST, C&#35;

In questa guida introduttiva si estrae testo scritto a mano da un'immagine usando Visione artificiale.

## <a name="prerequisites"></a>Prerequisiti

Per usare Visione artificiale, è necessario avere una chiave di sottoscrizione. A tale scopo, vedere [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Come ottenere chiavi di sottoscrizione).

## <a name="recognize-text-request"></a>Richiesta di riconoscimento del testo

Con i metodi [Recognize Text](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) e [Get Recognize Text Operation Result](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201), è possibile rilevare il testo scritto a mano in un'immagine ed estrarre i caratteri riconosciuti in un flusso utilizzabile da computer.

Per eseguire l'esempio, seguire questa procedura:

1. Creare una nuova app console Visual C# in Visual Studio.
1. Questo codice usa il pacchetto NuGet Newtonsoft.Json.
    1. Nel menu fare clic su **Strumenti**, selezionare **Gestione pacchetti NuGet** e quindi **Gestisci pacchetti NuGet per la soluzione**.
    1. Fare clic sulla scheda **Sfoglia** e nella casella di **ricerca** digitare "Newtonsoft.Json".
    1. Selezionare la voce **Newtonsoft.Json** quando viene visualizzata, quindi fare clic sulla casella di controllo accanto al nome del progetto e infine su **Installa**.
1. Sostituire `Program.cs` con il codice seguente.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Modificare il valore di `uriBase` impostando l'indirizzo in cui si sono ottenute le chiavi di sottoscrizione, se necessario.
1. Eseguire il programma.
1. Al prompt immettere il percorso di un'immagine locale.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same region in your REST call as you used to
        // get your subscription keys. For example, if you got your
        // subscription keys from westus, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/recognizeText";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Handwriting Recognition:");
            Console.Write(
                "Enter the path to an image with handwritten text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                ReadHandwrittenText(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the handwritten text from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with handwritten text.</param>
        static async Task ReadHandwrittenText(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameter.
                // Note: The request parameter changed for APIv2.
                // For APIv1, it is "handwriting=true".
                string requestParameters = "mode=Handwritten";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Two REST API calls are required to extract handwritten text.
                // One call to submit the image for processing, the other call
                // to retrieve the text found in the image.
                // operationLocation stores the REST API location to call to
                // retrieve the text.
                string operationLocation;

                // Request body.
                // Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // The first REST call starts the async process to analyze the
                    // written text in the image.
                    response = await client.PostAsync(uri, content);
                }

                // The response contains the URI to retrieve the result of the process.
                if (response.IsSuccessStatusCode)
                    operationLocation =
                        response.Headers.GetValues("Operation-Location").FirstOrDefault();
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                    return;
                }

                // The second REST call retrieves the text written in the image.
                //
                // Note: The response may not be immediately available. Handwriting
                // recognition is an async operation that can take a variable amount
                // of time depending on the length of the handwritten text. You may
                // need to wait or retry this operation.
                //
                // This example checks once per second for ten seconds.
                string contentString;
                int i = 0;
                do
                {
                    System.Threading.Thread.Sleep(1000);
                    response = await client.GetAsync(operationLocation);
                    contentString = await response.Content.ReadAsStringAsync();
                    ++i;
                }
                while (i < 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1);

                if (i == 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1)
                {
                    Console.WriteLine("\nTimeout error.\n");
                    return;
                }

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="recognize-text-response"></a>Risposta alla richiesta di riconoscimento del testo

Viene restituita una risposta con esito positivo in formato JSON, ad esempio:

```json
{
   "status": "Succeeded",
   "recognitionResult": {
      "lines": [
         {
            "boundingBox": [
               99,
               195,
               1309,
               45,
               1340,
               292,
               130,
               442
            ],
            "text": "when you write them down",
            "words": [
               {
                  "boundingBox": [
                     152,
                     191,
                     383,
                     154,
                     341,
                     421,
                     110,
                     458
                  ],
                  "text": "when"
               },
               {
                  "boundingBox": [
                     436,
                     145,
                     607,
                     118,
                     565,
                     385,
                     394,
                     412
                  ],
                  "text": "you"
               },
               {
                  "boundingBox": [
                     644,
                     112,
                     873,
                     76,
                     831,
                     343,
                     602,
                     379
                  ],
                  "text": "write"
               },
               {
                  "boundingBox": [
                     895,
                     72,
                     1092,
                     41,
                     1050,
                     308,
                     853,
                     339
                  ],
                  "text": "them"
               },
               {
                  "boundingBox": [
                     1140,
                     33,
                     1400,
                     0,
                     1359,
                     258,
                     1098,
                     300
                  ],
                  "text": "down"
               }
            ]
         },
         {
            "boundingBox": [
               142,
               222,
               1252,
               62,
               1269,
               180,
               159,
               340
            ],
            "text": "You remember things better",
            "words": [
               {
                  "boundingBox": [
                     140,
                     223,
                     267,
                     205,
                     288,
                     324,
                     162,
                     342
                  ],
                  "text": "You"
               },
               {
                  "boundingBox": [
                     314,
                     198,
                     740,
                     137,
                     761,
                     256,
                     335,
                     317
                  ],
                  "text": "remember"
               },
               {
                  "boundingBox": [
                     761,
                     134,
                     1026,
                     95,
                     1047,
                     215,
                     782,
                     253
                  ],
                  "text": "things"
               },
               {
                  "boundingBox": [
                     1046,
                     92,
                     1285,
                     58,
                     1307,
                     177,
                     1068,
                     212
                  ],
                  "text": "better"
               }
            ]
         },
         {
            "boundingBox": [
               155,
               405,
               537,
               338,
               557,
               449,
               175,
               516
            ],
            "text": "by hand",
            "words": [
               {
                  "boundingBox": [
                     146,
                     408,
                     266,
                     387,
                     301,
                     495,
                     181,
                     516
                  ],
                  "text": "by"
               },
               {
                  "boundingBox": [
                     290,
                     383,
                     569,
                     334,
                     604,
                     443,
                     325,
                     491
                  ],
                  "text": "hand"
               }
            ]
         }
      ]
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare un'applicazione di base per Windows che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime ritagliate in modo intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, di un'immagine. Per sperimentare rapidamente le API Visione artificiale, provare la [console di test delle API aperta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esercitazione in C&#35; dell'API Visione artificiale](../Tutorials/CSharpTutorial.md)
