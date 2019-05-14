---
title: Trascrivi conversazioni più partecipanti con Speech SDK - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la trascrizione di conversazione con Speech SDK. Disponibile per C++, C#e Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: 80ec606fee30c239d47bca94188d3b9cbb7c82d5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604406"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Trascrivi conversazioni più partecipanti con Speech SDK

il SDK di riconoscimento vocale **ConversationTranscriber** API consente di trascrivere riunioni/le conversazioni con la possibilità di aggiungere, rimuovere e identificare i partecipanti tramite lo streaming di audio per l'uso di servizi di riconoscimento vocale `PullStream` o`PushStream`.

## <a name="limitations"></a>Limitazioni

* Transcriber conversazione è supportata per C++, C#e Java su Windows, Linux e Android.
* Il DevKit ROOBO è l'ambiente hardware supportato per la creazione trascrizioni di conversazione che fornisce matrice multi-microfono circolare che può essere utilizzato in modo efficiente per l'identificazione voce. [Per altre informazioni, vedere vocale Devices SDK](speech-devices-sdk.md).
* Supporto SDK di riconoscimento vocale per la trascrizione di conversazione è limitato a utilizzare audio pull e push flussi, la modalità con otto canali audio PCM kHz 16 di 16 bit.
* La trascrizione di conversazione è attualmente disponibile nelle lingue "en-US" e "zh-CN" nelle aree seguenti: centralus e Asia orientale.

## <a name="prerequisites"></a>Prerequisiti

* [Informazioni su come utilizzare riconoscimento vocale con Speech SDK.](quickstart-csharp-dotnet-windows.md)
* [Ottenere la sottoscrizione di valutazione di riconoscimento vocale.](https://azure.microsoft.com/try/cognitive-services/)
* Speech SDK versione 1.5.1 o versione successiva è obbligatorio.

## <a name="create-voice-signatures-for-participants"></a>Creare le firme vocali per partecipanti

Il primo passaggio consiste nel creare le firme vocali per i partecipanti alla conversazione. Creazione di firme vocale è obbligatoria per identificazione voce efficiente.

### <a name="requirements-for-input-wave-file"></a>Requisiti per i file audio di input

* Il file di input audio wave per la creazione di firme vocale saranno in esempi di 16 bit, frequenza di campionamento kHz 16 e un formato di singolo canale (Mono).
* La lunghezza consigliata per ogni campione audio è compreso tra 30 secondi e due minuti.

L'esempio seguente illustra due modi diversi per creare una firma vocale [con l'API REST]. (https://aka.ms/cts/signaturegenservice) da C#:

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

## <a name="transcribing-conversations"></a>La trascrizione delle conversazioni

Per trascrivere conversazioni con più partecipanti, creare il `ConversationTranscriber` oggetto che è associato il `AudioConfig` oggetto creato per la sessione di conversazione e flusso audio tramite `PullAudioInputStream` o `PushAudioInputStream`.

Si supponga di avere una classe ConversationTranscriber denominata `MyConversationTranscriber`. Il codice potrebbe essere simile al seguente:

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
