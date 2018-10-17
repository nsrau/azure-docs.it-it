---
title: "Esempio: Usare l'endpoint di stima per testare le immagini a livello di codice con un classificatore - Servizio visione artificiale personalizzato"
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare l'API per testare a livello di codice le immagini con il classificatore del Servizio visione artificiale personalizzato.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: sample
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 3a81f3cef6aaeb5c98022d9fc93f4d84f3f58a6e
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363650"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Usare l'endpoint di stima per testare le immagini a livello di codice con un classificatore del Servizio visione artificiale personalizzato

Dopo avere eseguito il training del modello, è possibile testare le immagini a livello di codice inviandole all'API Prediction. 

> [!NOTE]
> Questo documento illustra l'uso di C# per inviare un'immagine all'API Prediction. Per altre informazioni ed esempi relativi all'uso dell'API, vedere le [informazioni di riferimento per l'API Prediction](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Ottenere l'URL e la chiave di stima

Dalla [pagina Web di Visione personalizzata](https://customvision.ai) selezionare il progetto e quindi selezionare la scheda __Performance__ (Prestazioni). Per visualizzare informazioni sull'uso dell'API Prediction, incluso il valore __Prediction-key__ selezionare __Prediction URL__. Per i progetti collegati a una risorsa di Azure il valore __Prediction-key__ è disponibile anche nella pagina del [Portale di Azure](https://portal.azure.com) per la risorsa di Azure associata in __Chiavi__. Copiare le informazioni seguenti da usare nell'applicazione:

* __URL__ per l'uso di un __file di immagine__.
* Valore __Prediction-Key__.

> [!TIP]
> Se si hanno più iterazioni, è possibile controllare l'iterazione usata impostandola come predefinita. Selezionare l'iterazione nella sezione __Iterations__ (Iterazioni), quindi selezionare __Make default__ (Predefinito) nella parte superiore della pagina.

![La scheda Performance (Prestazioni) viene visualizzata con un rettangolo rosso intorno a Prediction URL.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Creazione dell'applicazione

1. In Visual Studio creare una nuova applicazione console C#.

2. Usare il codice seguente come corpo del file __Program.cs__.

    > [!IMPORTANT]
    > Modificare le informazioni seguenti:
    >
    > * Impostare lo __spazio dei nomi__ sul nome del progetto.
    > * Impostare il valore __Prediction-Key__ ricevuto in precedenza nella riga che inizia con `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Impostare il valore __URL__ ricevuto in precedenza nella riga che inizia con `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

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
        }
    }
    ```

## <a name="use-the-application"></a>Usare l'applicazione

Quando si esegue l'applicazione, è possibile immettere il percorso di un file di immagine. L'immagine viene inviata all'API e i risultati vengono restituiti come documento JSON. Il documento JSON seguente è un esempio della risposta

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

[Esportare il modello per l'uso con i dispositivi mobili](export-your-model.md)
