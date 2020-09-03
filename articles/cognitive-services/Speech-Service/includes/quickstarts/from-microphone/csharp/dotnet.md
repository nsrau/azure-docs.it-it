---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 63fc699cfd2f02a322f71324519b2f0b5b22234b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926433"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa Voce di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

## <a name="open-your-project-in-visual-studio"></a>Aprire il progetto in Visual Studio

Il primo passaggio consiste nel verificare che il progetto sia aperto in Visual Studio.

1. Avviare **Visual Studio 2019**.
2. Caricare il progetto e aprire *Program.cs*.

## <a name="source-code"></a>Codice sorgente

Sostituire il contenuto del file *Program.cs* con il codice C# seguente.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace Speech.Recognition
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();

            Console.WriteLine("Please press any key to continue...");
            Console.ReadLine();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config =
                SpeechConfig.FromSubscription(
                    "YourSubscriptionKey",
                    "YourServiceRegion");

            using var recognizer = new SpeechRecognizer(config);
            
            var result = await recognizer.RecognizeOnceAsync();
            switch (result.Reason)
            {
                case ResultReason.RecognizedSpeech:
                    Console.WriteLine($"We recognized: {result.Text}");
                    break;
                case ResultReason.NoMatch:
                    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    break;
                case ResultReason.Canceled:
                    var cancellation = CancellationDetails.FromResult(result);
                    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    
                    if (cancellation.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                    }
                    break;
            }
        }
    }
}
```

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Spiegazione del codice

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Compilare ed eseguire l'app

A questo punto è possibile ricompilare l'app e testare la funzionalità di riconoscimento vocale con il servizio Voce.

1. **Compilare il codice**: dalla barra dei menu di Visual Studio scegliere **Compila** > **Compila soluzione**.
2. **Avviare l'app**: dalla barra dei menu scegliere **Debug** > **Avvia debug** o premere <kbd>F5</kbd>.
3. **Avviare il riconoscimento**: verrà richiesto di pronunciare una frase in inglese. La voce viene inviata al servizio Voce, trascritta come testo e visualizzata nella console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
