---
title: Usare l'endpoint di stima per testare le immagini a livello di codice con un classificatore - Visione personalizzata
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare l'API per testare a livello di codice le immagini con il classificatore del Servizio visione artificiale personalizzato.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046071"
---
# <a name="use-your-model-with-the-prediction-api"></a>Usare il modello con l'API Prediction

Dopo che è stato il training del modello, è possibile testare le immagini a livello di codice per inviarle all'endpoint dell'API di stima.

> [!NOTE]
> Questo documento illustra l'uso di C# per inviare un'immagine all'API Prediction. Per altre informazioni ed esempi, vedere la [riferimento all'API di stima](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Pubblicare l'iterazione sottoposto a training

Dalla [pagina Web di Visione personalizzata](https://customvision.ai) selezionare il progetto e quindi selezionare la scheda __Performance__ (Prestazioni).

Per inviare le immagini per le API di stima, è necessario pubblicare l'iterazione per la stima, che può essere eseguita selezionando __pubblica__ e specificando un nome per l'iterazione pubblicato. Il modello in questo modo accessibile per l'API di stima delle tue risorse di Azure di visione artificiale personalizzato.

![Viene visualizzata nella scheda prestazioni, con un rettangolo rosso che racchiudono il pulsante pubblica.](./media/use-prediction-api/unpublished-iteration.png)

Una volta il modello è stato pubblicato correttamente, si noterà un'etichetta "Pubblicato" vengono visualizzate accanto all'iterazione nella barra laterale a sinistra e il relativo nome verrà visualizzato nella descrizione dell'iterazione.

![Viene visualizzata nella scheda prestazioni, con un rettangolo rosso che circonda l'etichetta pubblicato e il nome dell'iterazione pubblicato.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Ottenere l'URL e la chiave di stima

Dopo aver pubblicato il modello, è possibile recuperare le informazioni necessarie, selezionare __stima URL__. Verrà aperta una finestra di dialogo con le informazioni per l'uso dell'API di stima, tra cui il __URL di stima__ e __stima-Key__.

![La scheda prestazioni viene visualizzata con un rettangolo rosso che racchiudono il pulsante di URL di stima.](./media/use-prediction-api/published-iteration-prediction-url.png)

![La scheda prestazioni viene visualizzata con un rettangolo rosso che racchiudono il valore dell'URL di stima per l'uso di un file di immagine e il valore della chiave di stima.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> il __stima-Key__ sono disponibili anche nel [portale di Azure](https://portal.azure.com) pagina per la risorsa di Azure di visione artificiale personalizzato associati al progetto, sotto il __chiavi__ pannello.

In questa Guida, verranno usare un'immagine locale, quindi copiare l'URL sotto **se si dispone di un file di immagine** in un percorso temporaneo. Copiare la corrispondente __chiave di stima__ anche valore.

## <a name="create-the-application"></a>Creazione dell'applicazione

1. In Visual Studio, creare un nuovo oggetto C# applicazione console.

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
   * Impostare il `namespace` campo per il nome del progetto.
   * Sostituire il segnaposto `<Your prediction key>` con il valore della chiave è stato recuperato in precedenza.
   * Sostituire il segnaposto `<Your prediction URL>` con l'URL recuperato in precedenza.

## <a name="run-the-application"></a>Eseguire l'applicazione

Quando si esegue l'applicazione, viene chiesto di immettere un percorso a un file di immagine nella console di. L'immagine viene quindi inviato all'API di stima, e i risultati della stima vengono restituiti come una stringa in formato JSON. Di seguito è riportato un esempio di risposta.

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

In questa Guida, si è appreso come inviare immagini personalizzati/rilevatore di classificazione di immagini e ricevano una risposta a livello di codice con il C# SDK. Successivamente, per informazioni su come completare scenari end-to-end con C#, oppure iniziare a usare un'altra lingua SDK.

* [Guida introduttiva: .NET SDK](csharp-tutorial.md)
* [Guida introduttiva: Python SDK](python-tutorial.md)
* [Guida introduttiva: Java SDK](java-tutorial.md)
* [Guida introduttiva: Node SDK](node-tutorial.md)
* [Guida introduttiva: Go SDK](go-tutorial.md)
