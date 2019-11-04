---
title: Trascrizione di conversazioni in tempo reale (anteprima)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la trascrizione delle conversazioni in tempo reale con l'SDK di riconoscimento vocale. Disponibile per C++, C#e Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 4ee597f96d28b43b9c69d1515634aadde2f0ab7c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464442"
---
# <a name="real-time-conversation-transcription-preview"></a>Trascrizione di conversazioni in tempo reale (anteprima)

L'API **ConversationTranscriber** dell'SDK vocale consente di trascrivere riunioni e altre conversazioni con la possibilità di aggiungere, rimuovere e identificare più partecipanti tramite lo streaming di audio ai servizi vocali usando `PullStream` o `PushStream`. Questo argomento richiede informazioni su come usare la sintesi vocale con l'SDK di riconoscimento vocale (versione 1.8.0 o successiva). Per ulteriori informazioni, vedere la pagina relativa ai [servizi di riconoscimento vocale](overview.md).

## <a name="limitations"></a>Limitazioni

- L'API ConversationTranscriber è supportata per C++, C#e Java in Windows, Linux e Android.
- Attualmente disponibile nei linguaggi "en-US" e "zh-CN" nelle aree seguenti: _centralus_ e _eastasia_.
- Richiede una matrice multimicrofono circolare 7-mic con un flusso di riferimento per la riproduzione. La matrice di microfoni deve soddisfare le [specifiche](https://aka.ms/sdsdk-microphone).
- [Speech Devices SDK](speech-devices-sdk.md) fornisce dispositivi appropriati e un'app di esempio che illustra la trascrizione delle conversazioni.

## <a name="optional-sample-code-resources"></a>Risorse di codice di esempio facoltative

Speech Device SDK fornisce codice di esempio in Java per l'acquisizione audio in tempo reale con 8 canali.

- [Codice di esempio del dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Codice di esempio di Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Prerequisiti

Sottoscrizione di servizi vocali. Se non si dispone [di una sottoscrizione di valutazione vocale, è possibile ottenerne una](https://azure.microsoft.com/try/cognitive-services/) .

## <a name="create-voice-signatures"></a>Creare firme vocali

Il primo passaggio consiste nel creare firme vocali per i partecipanti della conversazione per l'identificazione efficiente del relatore.

### <a name="audio-input-requirements"></a>Requisiti di input audio

- Il file Wave audio di input per la creazione di firme vocali deve essere in campioni a 16 bit, frequenza di campionamento di 16 kHz e formato a canale singolo (mono).
- La lunghezza consigliata per ogni campione audio è compresa tra 30 secondi e due minuti.

### <a name="sample-code"></a>Codice di esempio

Nell'esempio seguente vengono illustrati due modi diversi per creare la firma vocale [usando l'API REST](https://aka.ms/cts/signaturegenservice) in C#. Si noti che è necessario sostituire le informazioni reali per "YourSubscriptionKey", il nome del file Wave per "speakerVoice. wav" e la propria area per `{region}` e "YourServiceRegion" (_centralus_ o _eastasia_).

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
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

## <a name="transcribe-conversations"></a>Trascrizione di conversazioni

Nell'esempio di codice seguente viene illustrato come trascrivere le conversazioni in tempo reale per tre relatori. Si presuppone che siano già state create firme vocali per ogni altoparlante, come illustrato in precedenza. Sostituire le informazioni reali per "YourSubscriptionKey" e "YourServiceRegion" durante la creazione dell'oggetto SpeechConfig.

Di seguito sono riportati alcuni esempi di codice:

- Creazione di un oggetto `Conversation` dall'oggetto `SpeechConfig` utilizzando un identificatore della riunione generato utilizzando `Guid.NewGuid()`
- Creazione di un oggetto `ConversationTranscriber` e aggiunta della conversazione con `JoinConversationAsync()` per avviare la trascrizione
- Registrazione degli eventi di interesse
- Aggiunta o rimozione di partecipanti alla conversazione utilizzando l'oggetto Conversation
- Streaming dell'audio

La trascrizione e l'identificatore del relatore vengono restituiti negli eventi registrati.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = new Conversation(config, meetingId))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
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

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trascrizione di conversazioni asincrone](how-to-async-conversation-transcription.md)
