---
title: Trascrivere conversazioni multipartecipante con l'SDK vocale-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la trascrizione delle conversazioni con l'SDK di riconoscimento vocale. Disponibile per C++, C#e Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 5ad912b1cee5495e18a5eb2da4d981eadd74dd7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066422"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Trascrizione di conversazioni multipartecipante con l'SDK di riconoscimento vocale

L'API **ConversationTranscriber** dell'SDK vocale consente di trascrivere riunioni/conversazioni con la possibilità di aggiungere, rimuovere e identificare i partecipanti tramite lo streaming di audio ai servizi di riconoscimento vocale `PullStream` usando `PushStream`o.

## <a name="limitations"></a>Limitazioni

* Il trascrittore delle conversazioni è C++supportato C#per, e Java in Windows, Linux e Android.
* Il DevKit ROOBO è l'ambiente hardware supportato per la creazione di trascrizioni di conversazioni, in quanto fornisce una matrice circolare multimicrofono che può essere usata in modo efficiente per l'identificazione del parlante. [Per altre informazioni, vedere Speech Devices SDK](speech-devices-sdk.md).
* Il supporto dell'SDK vocale per la trascrizione delle conversazioni è limitato ai flussi di modalità pull e push audio con 8 canali di audio PCM a 16 bit a 16 bit. Attualmente, solo questi kit sono supportati per l'acquisizione audio a 8 canali:
   * [ROOBO smart audio circolare 7-mic DK](https://ddk.roobo.com/)
   * [Azure Kinect dk](https://azure.microsoft.com/en-in/services/kinect-dk/).
* La trascrizione delle conversazioni è attualmente disponibile nei linguaggi "en-US" e "zh-CN" nelle aree seguenti: centralus e eastasia.

## <a name="prerequisites"></a>Prerequisiti

* [Informazioni su come usare la sintesi vocale con l'SDK di riconoscimento vocale.](quickstart-csharp-dotnet-windows.md)
* [Ricevi la tua sottoscrizione di valutazione vocale.](https://azure.microsoft.com/try/cognitive-services/)
* È necessario l'SDK vocale versione 1.5.1 o successiva.

## <a name="create-voice-signatures-for-participants"></a>Creare firme vocali per i partecipanti

Il primo passaggio consiste nel creare firme vocali per i partecipanti alla conversazione. La creazione di firme vocali è necessaria per l'identificazione efficiente del relatore.

### <a name="requirements-for-input-wave-file"></a>Requisiti per il file Wave di input

* Il file Wave audio di input per la creazione di firme vocali deve essere in campioni a 16 bit, frequenza di campionamento di 16 kHz e formato a canale singolo (mono).
* La lunghezza consigliata per ogni campione audio è compresa tra 30 secondi e due minuti.

Nell'esempio seguente vengono illustrati due modi diversi per creare la firma vocale [usando l'API REST](https://aka.ms/cts/signaturegenservice) da C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Trascrizione di conversazioni

Per `ConversationTranscriber` trascrivere conversazioni con più partecipanti, creare l'oggetto associato `AudioConfig` all'oggetto creato per la sessione di conversazione e il flusso audio usando `PullAudioInputStream` o `PushAudioInputStream`.

Si supponga di disporre di una classe ConversationTranscriber denominata `MyConversationTranscriber`. Il codice potrebbe essere simile al seguente:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopTranscription.TrySetResult(0);
                    }
                };

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document.
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)
