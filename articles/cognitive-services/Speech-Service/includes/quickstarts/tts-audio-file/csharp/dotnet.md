---
title: 'Guida introduttiva: Eseguire la sintesi vocale in un file audio, C# (.NET) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 0b34c65f6b7003cbdd4d99b684fe2b5a3249c410
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818042"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Aprire il progetto in Visual Studio

Il primo passaggio consiste nel verificare che il progetto sia aperto in Visual Studio.

1. Avviare Visual Studio 2019.
2. Caricare il progetto e aprire `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere codice che funga da scheletro del progetto. Si noti che è stato creato un metodo asincrono denominato `SynthesisToAudioFileAsync()`.

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Prima di inizializzare un oggetto `SpeechSynthesizer`, è necessario creare una configurazione che usi la chiave e l'area di sottoscrizione. Inserire questo codice nel metodo `SynthesisToAudioFileAsync()`.

````C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Creare una configurazione audio

A questo punto, è necessario creare un oggetto ````AudioConfig```` che punti al file audio. Questo oggetto viene creato all'interno di un'istruzione using per garantire il corretto rilascio di risorse non gestite. Inserire questo codice nel metodo `SynthesisToAudioFileAsync()`, immediatamente sotto la configurazione di Voce.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Inizializzare SpeechSynthesizer

A questo punto, creare l'oggetto `SpeechSynthesizer` usando gli oggetti `SpeechConfig` e `AudioConfig` creati in precedenza. Anche questo oggetto viene creato all'interno di un'istruzione using per garantire il corretto rilascio di risorse non gestite. Inserire questo codice nel metodo `SynthesisToAudioFileAsync()`, all'interno dell'istruzione using che esegue il wrapping dell'oggetto ````AudioConfig````.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Sintetizzare il testo usando SpeakTextAsync

Dall'oggetto `SpeechSynthesizer` chiamare il metodo `SpeakTextAsync()`. Questo metodo invia il testo al servizio Voce, che lo converte in audio. Se `config.VoiceName` non è specificato in modo esplicito, `SpeechSynthesizer` userà la voce predefinita.

All'interno dell'istruzione using aggiungere questo codice:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Verificare la presenza di errori

Quando il servizio Voce restituisce il risultato della sintesi, è necessario verificare che il testo sia stato sintetizzato correttamente.

All'interno dell'istruzione using, sotto `SpeakTextAsync()`, aggiungere il codice seguente:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Controllare il codice

A questo punto, il codice dovrà avere questo aspetto:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

A questo punto è possibile compilare l'app e testare la sintesi vocale con il servizio Voce.

1. **Compilare il codice**: sulla barra dei menu di Visual Studio scegliere **Compila** > **Compila soluzione**.
2. **Avviare l'app**: sulla barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.
3. **Avviare la sintesi**: il testo viene convertito in voce e salvato nei dati audio specificati.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Vedere anche

- [Creare una voce personalizzata](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrare campioni vocali personalizzati](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
