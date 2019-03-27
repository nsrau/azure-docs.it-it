---
title: "Esempio: Usare l'endpoint di stima per testare le immagini a livello di codice con un classificatore - Visione personalizzata"
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare l'API per testare a livello di codice le immagini con il classificatore del Servizio visione artificiale personalizzato.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472727"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Usare il modello con l'API Prediction

Dopo avere eseguito il training del modello, è possibile testare le immagini a livello di codice inviandole all'API Prediction.

> [!NOTE]
> Questo documento illustra l'uso di C# per inviare un'immagine all'API Prediction. Per altre informazioni ed esempi relativi all'uso dell'API, vedere le [informazioni di riferimento per l'API Prediction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Pubblicare l'iterazione sottoposto a training

Dalla [pagina Web di Visione personalizzata](https://customvision.ai) selezionare il progetto e quindi selezionare la scheda __Performance__ (Prestazioni).

Per inviare le immagini per le API di stima, è necessario pubblicare l'iterazione per la stima, che può essere eseguita selezionando __pubblica__ e specificando un nome per l'iterazione pubblicato. In questo modo il modello sia accessibile per l'API di stima delle tue risorse di Azure di visione artificiale personalizzato. 

![Viene visualizzata nella scheda prestazioni, con un rettangolo rosso che racchiudono il pulsante pubblica.](./media/use-prediction-api/unpublished-iteration.png)

Al termine il modello è stato pubblicato correttamente, si noterà un'etichetta "Pubblicato" vengono visualizzate accanto all'iterazione nella barra laterale a sinistra, nonché il nome dell'iterazione pubblicato nella descrizione dell'iterazione.

![Viene visualizzata nella scheda prestazioni, con un rettangolo rosso che circonda l'etichetta pubblicato e il nome dell'iterazione pubblicato.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Ottenere l'URL e la chiave di stima

Dopo aver pubblicato il modello, è possibile recuperare le informazioni sull'uso dell'API di stima, selezionando __stima URL__. Verrà aperta una finestra di dialogo simile a quello illustrato di seguito con le informazioni per l'uso dell'API di stima, tra cui il __URL di stima__ e __stima-Key__.

![La scheda prestazioni viene visualizzata con un rettangolo rosso che racchiudono il pulsante di URL di stima.](./media/use-prediction-api/published-iteration-prediction-url.png)

![La scheda prestazioni viene visualizzata con un rettangolo rosso che racchiudono il valore dell'URL di stima per l'uso di un file di immagine e il valore della chiave di stima.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> I __stima-Key__ sono disponibili anche nel [portale di Azure](https://portal.azure.com) pagina per la risorsa di Azure di visione artificiale personalizzato associato al progetto, sotto __chiavi__. 

Nella finestra di dialogo, copiare le informazioni seguenti per l'uso dell'applicazione:

* __URL di stima__ per l'uso di un __file di immagine__.
* __Chiave di stima__ valore.

## <a name="create-the-application"></a>Creazione dell'applicazione

1. In Visual Studio creare una nuova applicazione console C#.

1. Usare il codice seguente come corpo del file __Program.cs__.

    > [!IMPORTANT]
    > Modificare le informazioni seguenti:
    >
    > * Impostare lo __spazio dei nomi__ sul nome del progetto.
    > * Impostare il __stima-Key__ valore recuperato in precedenza nella riga che inizia con `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Impostare il __URL di stima__ valore recuperato in precedenza nella riga che inizia con `string url =`.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>Usare l'applicazione

Quando si esegue l'applicazione, sarà necessario immettere il percorso del file di immagine nella console. L'immagine viene inviato all'API di stima e i risultati di stima vengono restituiti come documento JSON. Il codice JSON seguente è riportato un esempio della risposta.

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

[Esportare il modello per l'uso con i dispositivi mobili](export-your-model.md)

[Introduzione a .NET SDK](csharp-tutorial.md)

[Introduzione a Python SDK](python-tutorial.md)

[Introduzione a Java SDK](java-tutorial.md)

[Introduzione a SDK di nodo](node-tutorial.md)

[Introduzione a Go SDK](go-tutorial.md)
