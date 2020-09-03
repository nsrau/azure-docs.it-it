---
title: "Avvio rapido: Analizzare un'immagine locale - REST, C#"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si analizzerà un'immagine locale usando l'API Visione artificiale con C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e086f0a02849b568c932aab0b01e6168631422e9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928293"
---
# <a name="quickstart-analyze-a-local-image-using-the-computer-vision-rest-api-and-c"></a>Avvio rapido: Analizzare un'immagine locale usando l'API REST Visione artificiale e C#

Questa guida di avvio rapido mostra come analizzare un'immagine archiviata in locale per estrarre caratteristiche visive usando l'API REST di Visione artificiale. Con il metodo [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) è possibile estrarre informazioni sulle caratteristiche visive dal contenuto di un'immagine.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* È necessario avere [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) o versione successiva
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* [Creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominati rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample-application"></a>Creare ed eseguire l'applicazione di esempio

Per creare l'esempio in Visual Studio, seguire questa procedura:

1. Creare una nuova soluzione o un nuovo progetto di Visual Studio in Visual Studio usando il modello di app console di Visual C# (.NET Core Framework).
1. Questo codice usa il pacchetto NuGet Newtonsoft.Json.
    1. Nel menu fare clic su **Strumenti**, selezionare **Gestione pacchetti NuGet** e quindi **Gestisci pacchetti NuGet per la soluzione**.
    1. Fare clic sulla scheda **Sfoglia** e nella **casella di ricerca** digitare "Newtonsoft.Json" (se non è già visualizzato).
    1. Selezionare **Newtonsoft.Json**, quindi fare clic sulla casella di controllo accanto al nome del progetto e selezionare **Installa**.
1. Copiare e incollare il frammento di codice di esempio seguente nel file Program.cs. Modificare il nome dello spazio dei nomi se è diverso da quello creato.
1. Aggiungere un'immagine a scelta alla cartella bin/debug/netcoreappX.X, quindi aggiungere il nome dell'immagine (compresa l'estensione) alla variabile "imageFilePath".
1. Eseguire il programma.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Add your Computer Vision subscription key and endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");

        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");
        
        // the Analyze method endpoint
        static string uriBase = endpoint + "vision/v3.0/analyze";

        // Image you want analyzed (add to your bin/debug/netcoreappX.X folder)
        // For sample images, download one from here (png or jpg):
        // https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/Images
        static string imageFilePath = @"my-sample-image";

        public static void Main()
        {
            // Call the API
            MakeAnalysisRequest(imageFilePath).Wait();

            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the analysis of the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to analyze.</param>
        static async Task MakeAnalysisRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                // The Analyze Image method returns information about the following
                // visual features:
                // Categories:  categorizes image content according to a
                //              taxonomy defined in documentation.
                // Description: describes the image content with a complete
                //              sentence in supported languages.
                // Color:       determines the accent color, dominant color, 
                //              and whether an image is black & white.
                string requestParameters =
                    "visualFeatures=Categories,Description,Color";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Asynchronously get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

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
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>Esaminare i risultati

Nella finestra della console viene restituita una risposta con esito positivo in formato JSON (in base all'immagine usata), simile all'esempio seguente:

```json
{
    "categories": [
        {
            "name": "abstract_",
            "score": 0.00390625
        },
        {
            "name": "others_",
            "score": 0.0234375
        },
        {
            "name": "outdoor_",
            "score": 0.00390625
        }
    ],
    "description": {
        "tags": [
            "road",
            "building",
            "outdoor",
            "street",
            "night",
            "black",
            "city",
            "white",
            "light",
            "sitting",
            "riding",
            "man",
            "side",
            "empty",
            "rain",
            "corner",
            "traffic",
            "lit",
            "hydrant",
            "stop",
            "board",
            "parked",
            "bus",
            "tall"
        ],
        "captions": [
            {
                "text": "a close up of an empty city street at night",
                "confidence": 0.7965622853462756
            }
        ]
    },
    "requestId": "dddf1ac9-7e66-4c47-bdef-222f3fe5aa23",
    "metadata": {
        "width": 3733,
        "height": 1986,
        "format": "Jpeg"
    },
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": [
            "Black",
            "Grey"
        ],
        "accentColor": "666666",
        "isBWImg": true
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare un'applicazione di base per Windows che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime ritagliate in modo intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, di un'immagine.

> [!div class="nextstepaction"]
> [Esercitazione in C# dell'API Visione artificiale](../Tutorials/CSharpTutorial.md)
