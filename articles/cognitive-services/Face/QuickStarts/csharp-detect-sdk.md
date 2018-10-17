---
title: "Guida introduttiva: Rilevare i visi in un'immagine - SDK, C#"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si rileveranno i visi in un'immagine usando la libreria client C# Viso per Windows in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: nolachar
ms.openlocfilehash: a9a7efd89f8e7462812064615d07acf12acbc3a3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364114"
---
# <a name="quickstart-detect-faces-in-an-image-using-c35---face"></a>Guida introduttiva: Rilevare i visi in un'immagine con C&#35; - Viso

In questa guida introduttiva si rileveranno i visi in un'immagine usando la libreria client C# Viso per Windows.

Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face).

## <a name="prerequisites"></a>Prerequisiti

* Per eseguire l'esempio è necessaria una sottoscrizione. È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
* Qualsiasi edizione di [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Pacchetto NuGet della libreria client [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Non è necessario scaricare il pacchetto. Le istruzioni di installazione sono disponibili più avanti.

## <a name="detectwithurlasync-method"></a>Metodo DetectWithUrlAsync

I metodi `DetectWithUrlAsync` e `DetectWithStreamAsync` eseguono il wrapping dell'[API Viso - Rilevamento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rispettivamente per le immagini locali e remote. È possibile usare questi metodi per rilevare i visi in un'immagine e restituirne gli attributi, tra cui:

* ID viso: ID univoco usato in diversi scenari di API Viso.
* Rettangolo del viso: riquadro i cui lati indicano la posizione del viso nell'immagine.
* Punti di riferimento: matrice con 27 punti di riferimento che indicano le posizioni importanti dei componenti del viso.
* Altri attributi del viso quali età, sesso, intensità del sorriso, orientamento della testa e peli del viso.

Per eseguire l'esempio, seguire questa procedura:

1. Creare una nuova app console Visual C# in Visual Studio.
1. Installare il pacchetto NuGet della libreria client Viso.
    1. Nel menu in alto fare clic su **Strumenti**, selezionare **Gestione pacchetti NuGet** e quindi **Gestisci pacchetti NuGet per la soluzione**.
    1. Fare clic sulla scheda **Sfoglia** e quindi selezionare **Includi versione preliminare**.
    1. Nella casella **Ricerca** digitare "Microsoft.Azure.CognitiveServices.Vision.Face".
    1. Selezionare la voce **Microsoft.Azure.CognitiveServices.Vision.Face** quando viene visualizzata, fare clic sulla casella di controllo accanto al nome del progetto e quindi su **Installa**.
1. Sostituire *Program.cs* con il codice seguente.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Modificare `faceEndpoint` in modo che corrisponda all'area di Azure associata alle chiavi di sottoscrizione, se necessario.
1. Facoltativamente, sostituire <`LocalImage>` con il percorso e il nome del file di un'immagine locale. Se non impostato, questo parametro verrà ignorato.
1. Facoltativamente, impostare un'immagine diversa per `remoteImageUrl`.
1. Eseguire il programma.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>Risposta DetectWithUrlAsync

In caso di risposta positiva, vengono visualizzati il sesso e l'età di ogni viso nell'immagine.

Per un esempio di output JSON non elaborato, vedere [Guide introduttive per le API: Rilevare i visi in un'immagine con C#](CSharp.md).

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare un'applicazione Windows WPF che usi il servizio Viso per rilevare i visi in un'immagine. L'applicazione disegna una cornice intorno a ogni viso e visualizza una descrizione del viso nella barra di stato.

> [!div class="nextstepaction"]
> [Esercitazione: Creare un'app WPF per rilevare e incorniciare i visi in un'immagine](../Tutorials/FaceAPIinCSharpTutorial.md)
