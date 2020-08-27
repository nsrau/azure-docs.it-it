---
title: Usare l'endpoint di stima per testare le immagini a livello di codice con un classificatore - Visione personalizzata
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API per testare a livello di codice le immagini con il classificatore del Servizio visione artificiale personalizzato.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934753"
---
# <a name="use-your-model-with-the-prediction-api"></a>Usare il modello con l'API di stima

Dopo aver eseguito il training del modello, è possibile testare le immagini a livello di programmazione inviando le immagini all'endpoint dell'API di stima.

> [!NOTE]
> Questo documento illustra l'uso di C# per inviare un'immagine all'API Prediction. Per ulteriori informazioni ed esempi, vedere le informazioni di [riferimento sull'API di stima](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Pubblicare l'iterazione con training

Dalla [pagina Web di Visione personalizzata](https://customvision.ai) selezionare il progetto e quindi selezionare la scheda __Performance__ (Prestazioni).

Per inviare immagini all'API di stima, sarà prima di tutto necessario pubblicare l'iterazione per la stima, operazione che può essere eseguita selezionando __pubblica__ e specificando un nome per l'iterazione pubblicata. In questo modo il modello verrà reso accessibile all'API di stima della risorsa Visione personalizzata Azure.

![Viene visualizzata la scheda prestazioni con un rettangolo rosso che circonda il pulsante pubblica.](./media/use-prediction-api/unpublished-iteration.png)

Una volta che il modello è stato pubblicato correttamente, verrà visualizzata un'etichetta "pubblicata" accanto all'iterazione nella barra laterale a sinistra e il relativo nome verrà visualizzato nella descrizione dell'iterazione.

![Viene visualizzata la scheda prestazioni con un rettangolo rosso che circonda l'etichetta pubblicata e il nome dell'iterazione pubblicata.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Ottenere l'URL e la chiave di stima

Una volta pubblicato il modello, è possibile recuperare le informazioni richieste selezionando __URL stima__. Verrà visualizzata una finestra di dialogo con le informazioni per l'utilizzo dell'API di stima, inclusi l' __URL__ di stima e la __chiave di stima__.

![La scheda prestazioni viene visualizzata con un rettangolo rosso che circonda il pulsante URL di stima.](./media/use-prediction-api/published-iteration-prediction-url.png)

![La scheda prestazioni viene visualizzata con un rettangolo rosso che circonda il valore dell'URL di stima per l'utilizzo di un file di immagine e il valore della chiave di stima.](./media/use-prediction-api/prediction-api-info.png)


In questa guida verrà usata un'immagine locale, quindi copiare l'URL in **se si dispone** di un file di immagine in un percorso temporaneo. Copiare anche il valore della __chiave di stima__ corrispondente.

## <a name="create-the-application"></a>Creare l'applicazione

1. In Visual Studio creare una nuova applicazione console C#.

1. Usare il codice seguente come corpo del file __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Modificare le informazioni seguenti:
   * Impostare il `namespace` campo sul nome del progetto.
   * Sostituire il segnaposto `<Your prediction key>` con il valore della chiave recuperato in precedenza.
   * Sostituire il segnaposto `<Your prediction URL>` con l'URL recuperato in precedenza.

## <a name="run-the-application"></a>Eseguire l'applicazione

Quando si esegue l'applicazione, viene richiesto di immettere un percorso di un file di immagine nella console di. L'immagine viene quindi inviata all'API di stima e i risultati della stima vengono restituiti come stringa in formato JSON. Di seguito è riportato un esempio di risposta.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come inviare immagini al classificatore/rilevatore di immagini personalizzate e ricevere una risposta a livello di codice con C# SDK. Successivamente, Scopri come completare gli scenari end-to-end con C# o iniziare a usare un SDK di linguaggio diverso.

* [Guida introduttiva: SDK Visione personalizzata](quickstarts/image-classification.md)
