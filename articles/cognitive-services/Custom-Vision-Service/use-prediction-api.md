---
title: Usare l'endpoint di stima per testare le immagini a livello di codice con un classificatore - Visione personalizzata
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API per testare a livello di codice le immagini con il classificatore del Servizio visione artificiale personalizzato.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169941"
---
# <a name="use-your-model-with-the-prediction-api"></a>Usare il modello con l'API di stimaUse your model with the prediction API

Dopo aver eseguito il training del modello, è possibile testare le immagini a livello di codice inviandole all'endpoint dell'API di stima.

> [!NOTE]
> Questo documento illustra l'uso di C# per inviare un'immagine all'API Prediction. Per altre informazioni ed esempi, vedere le informazioni di [riferimento sull'API di stima](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Pubblicare l'iterazione con training

Dalla [pagina Web di Visione personalizzata](https://customvision.ai) selezionare il progetto e quindi selezionare la scheda __Performance__ (Prestazioni).

Per inviare immagini all'API di stima, è innanzitutto necessario pubblicare l'iterazione per la stima, operazione che può essere eseguita selezionando __Pubblica__ e specificando un nome per l'iterazione pubblicata. In questo modo il modello sarà accessibile all'API di stima della risorsa di Azure con visione personalizzata.

![Viene visualizzata la scheda Delle prestazioni, con un rettangolo rosso che circonda il pulsante Pubblica.](./media/use-prediction-api/unpublished-iteration.png)

Una volta che il modello è stato pubblicato correttamente, vedrai un'etichetta "Pubblicato" accanto all'iterazione nella barra laterale sinistra e il suo nome verrà visualizzato nella descrizione dell'iterazione.

![Viene visualizzata la scheda Prestazioni, con un rettangolo rosso che circonda l'etichetta Published e il nome dell'iterazione pubblicata.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Ottenere l'URL e la chiave di stima

Dopo aver pubblicato il modello, è possibile recuperare le informazioni richieste selezionando __URL stima__. Verrà aperta una finestra di dialogo con informazioni per l'utilizzo dell'API di stima, inclusi l'URL di stima e la __chiave di__ __stima__ .

![La scheda delle prestazioni viene visualizzata con un rettangolo rosso che circonda il pulsante URL stima.](./media/use-prediction-api/published-iteration-prediction-url.png)

![La scheda delle prestazioni viene visualizzata con un rettangolo rosso che circonda il valore URL stima per l'utilizzo di un file di immagine e il valore di chiave di stima.](./media/use-prediction-api/prediction-api-info.png)


In questa guida verrà utilizzata un'immagine locale, quindi copiare l'URL in **Se si dispone** di un file di immagine in un percorso temporaneo. Copiare anche il valore __della chiave di stima__ corrispondente.

## <a name="create-the-application"></a>Creazione dell'applicazione

1. In Visual Studio, creare una nuova applicazione console di C.

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
   * Impostare `namespace` il campo sul nome del progetto.
   * Sostituire il `<Your prediction key>` segnaposto con il valore della chiave recuperato in precedenza.
   * Sostituire il `<Your prediction URL>` segnaposto con l'URL recuperato in precedenza.

## <a name="run-the-application"></a>Eseguire l'applicazione

Quando si esegue l'applicazione, viene richiesto di immettere un percorso a un file di immagine nella console. L'immagine viene quindi inviata all'API di stima e i risultati della stima vengono restituiti come stringa in formato JSON. Di seguito è riportata una risposta di esempio.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come inviare immagini al classificatore/rilevatore di immagini personalizzato e ricevere una risposta a livello di codice con l'SDK di C. Successivamente, scopri come completare gli scenari end-to-end con C 'net' o iniziare a usare un SDK in una lingua diversa.

* [Guida introduttiva: .NET SDK](csharp-tutorial.md)
* [Guida introduttiva: Python SDK](python-tutorial.md)
* [Guida introduttiva: Java SDK](java-tutorial.md)
* [Guida introduttiva: SDK del nodo](node-tutorial.md)
* [Guida introduttiva: Go SDK](go-tutorial.md)
