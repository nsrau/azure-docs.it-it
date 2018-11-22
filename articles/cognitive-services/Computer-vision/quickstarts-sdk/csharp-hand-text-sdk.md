---
title: 'Guida introduttiva: estrarre testo - SDK, C# - visione artificiale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si estrae testo da un'immagine usando la libreria client Windows C# di Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/27/2018
ms.author: pafarley
ms.openlocfilehash: c1b25db440023618d3d633f9a2b2ebcd5c0a6293
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852487"
---
# <a name="quickstart-extract-text-using-the-computer-vision-sdk-and-c"></a>Guida introduttiva: estrarre testo usando SDK e C# di Visione artificiale

In questa guida introduttiva si estrae testo scritto a mano o stampato da un'immagine usando la libreria client Windows di Visione artificiale.

## <a name="prerequisites"></a>Prerequisiti

* Per usare Visione artificiale, è necessario avere una chiave di sottoscrizione. A tale scopo, vedere [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Come ottenere chiavi di sottoscrizione).
* Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).
* Il pacchetto NuGet della libreria client [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Non è necessario scaricare il pacchetto. Le istruzioni di installazione sono disponibili più avanti.

## <a name="recognizetextasync-method"></a>Metodo RecognizeTextAsync

> [!TIP]
> Ottenere la versione più recente del codice come soluzione di Visual Studio da [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

I metodi `RecognizeTextAsync` e `RecognizeTextInStreamAsync` eseguono il wrapping dell'[API Recognize Text](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) rispettivamente per le immagini locali e remote. Il metodo `GetTextOperationResultAsync` esegue il wrapping dell'[API Get Recognize Text Operation Results](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201).  È possibile usare questi metodi per rilevare il testo in un'immagine ed estrarre i caratteri riconosciuti in un flusso utilizzabile da computer.

Per eseguire l'esempio, seguire questa procedura:

1. Creare una nuova app console Visual C# in Visual Studio.
1. Installare il pacchetto NuGet della libreria client di Visione artificiale.
    1. Nel menu fare clic su **Strumenti**, selezionare **Gestione pacchetti NuGet** e quindi **Gestisci pacchetti NuGet per la soluzione**.
    1. Fare clic sulla scheda **Sfoglia** e nella casella di **ricerca** digitare "Microsoft.Azure.CognitiveServices.Vision.ComputerVision".
    1. Selezionare la voce **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** quando viene visualizzata, quindi fare clic sulla casella di controllo accanto al nome del progetto e infine su **Installa**.
1. Sostituire `Program.cs` con il codice seguente.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Modificare `computerVision.Endpoint` in modo che corrisponda all'area di Azure associata alle chiavi di sottoscrizione, se necessario.
1. Facoltativamente, impostare `textRecognitionMode` su `TextRecognitionMode.Printed`.
1. Sostituire `<LocalImage>` con il percorso e il nome del file di un'immagine locale.
1. Facoltativamente, impostare un'immagine diversa per `remoteImageUrl`.
1. Eseguire il programma.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ExtractText
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // For printed text, change to TextRecognitionMode.Printed
        private const TextRecognitionMode textRecognitionMode =
            TextRecognitionMode.Handwritten;

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
            "Cursive_Writing_on_Notebook_paper.jpg/" +
            "800px-Cursive_Writing_on_Notebook_paper.jpg";

        private const int numberOfCharsInOperationId = 36;

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the "westus"
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...");
            var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
            var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Recognize text from a remote image
        private static async Task ExtractRemoteTextAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            // Start the async process to recognize the text
            RecognizeTextHeaders textHeaders =
                await computerVision.RecognizeTextAsync(
                    imageUrl, textRecognitionMode);

            await GetTextAsync(computerVision, textHeaders.OperationLocation);
        }

        // Recognize text from a local image
        private static async Task ExtractLocalTextAsync(
            ComputerVisionClient computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                // Start the async process to recognize the text
                RecognizeTextInStreamHeaders textHeaders =
                    await computerVision.RecognizeTextInStreamAsync(
                        imageStream, textRecognitionMode);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }
        }

        // Retrieve the recognized text
        private static async Task GetTextAsync(
            ComputerVisionClient computerVision, string operationLocation)
        {
            // Retrieve the URI where the recognized text will be
            // stored from the Operation-Location header
            string operationId = operationLocation.Substring(
                operationLocation.Length - numberOfCharsInOperationId);

            Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
            TextOperationResult result =
                await computerVision.GetTextOperationResultAsync(operationId);

            // Wait for the operation to complete
            int i = 0;
            int maxRetries = 10;
            while ((result.Status == TextOperationStatusCodes.Running ||
                    result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
            {
                Console.WriteLine(
                    "Server status: {0}, waiting {1} seconds...", result.Status, i);
                await Task.Delay(1000);

                result = await computerVision.GetTextOperationResultAsync(operationId);
            }

            // Display the results
            Console.WriteLine();
            var lines = result.RecognitionResult.Lines;
            foreach (Line line in lines)
            {
                Console.WriteLine(line.Text);
            }
            Console.WriteLine();
        }
    }
}
```

## <a name="recognizetextasync-response"></a>Risposta di RecognizeTextAsync

Una risposta con esito positivo visualizza le righe del testo riconosciuto per ogni immagine.

Per un esempio di output JSON non elaborato, vedere [Guida introduttiva: estrarre testo scritto a mano - REST, C#](../QuickStarts/CSharp-hand-text.md#examine-the-response).

```cmd
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare le API Visione artificiale usate per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato.

> [!div class="nextstepaction"]
> [Esaminare le API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
