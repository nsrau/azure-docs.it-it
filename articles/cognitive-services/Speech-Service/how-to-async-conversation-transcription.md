---
title: Trascrizione di conversazioni asincrone (anteprima) - Servizio di riconoscimento vocaleAsynchronous Conversation Transcription (Preview) - Speech service
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la trascrizione di conversazioni asincrona usando il servizio di riconoscimento vocale. Disponibile solo per Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: c1f0110c83eb42aaedbd36736946ae3faff58699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366604"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Trascrizione di conversazioni asincrone (anteprima)Asynchronous Conversation Transcription (Preview)

In questo articolo, la trascrizione di conversazioni asincrona viene illustrata tramite l'API **RemoteConversationTranscriptionClient.** Se la trascrizione di conversazione è stata configurata per eseguire `conversationId`la trascrizione asincrona e si `conversationId` dispone di un oggetto , è possibile ottenere la trascrizione associata utilizzando l'API **RemoteConversationTranscriptionClient.**

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asincrono e in tempo reale - asincrono

Con la trascrizione asincrona, si esegue lo streaming dell'audio della conversazione, ma non è necessario che venga restituita una trascrizione in tempo reale. Al contrario, dopo l'invio `Conversation` dell'audio, utilizzare il di per eseguire una `conversationId` query per lo stato della trascrizione asincrona. Quando la trascrizione asincrona è pronta, si `RemoteConversationTranscriptionResult`otterrà un file .

Con in tempo reale più asincrono, si ottiene la trascrizione in tempo reale, ma anche `conversationId` ottenere la trascrizione eseguendo una query con il (simile allo scenario asincrono).

Per eseguire la trascrizione asincrona sono necessari due passaggi. Il primo passaggio consiste nel caricare l'audio, scegliendo solo asincrono o in tempo reale più asincrono. Il secondo passaggio consiste nell'ottenere i risultati della trascrizione.

## <a name="upload-the-audio"></a>Caricare l'audio

Prima di poter eseguire la trascrizione asincrona, è necessario inviare l'audio al servizio di trascrizione conversazioni utilizzando Microsoft Cognitive Speech client SDK (versione 1.8.0 o successiva).

Questo codice di esempio mostra come creare un transcriber di conversazione per la modalità solo asincrona. Per trasmettere l'audio al trascrittore, è necessario aggiungere il codice di streaming audio derivato dalle [conversazioni Transcribe in tempo reale con Speech SDK](how-to-use-conversation-transcription-service.md). Fare riferimento alla sezione **Limitazioni** dell'argomento per visualizzare le API relative a piattaforme e lingue supportate.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Se si desidera in tempo reale _più_ asincrono, commentare e rimuovere il commento le righe di codice appropriate come segue:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Ottenere i risultati della trascrizioneGet transcripton results

Questo passaggio consente di ottenere i risultati della trascrizione asincrona, ma presuppone che qualsiasi elaborazione in tempo reale necessaria venga eseguita altrove. Per ulteriori informazioni, consultate [Transcribe conversations in real time con Speech SDK.](how-to-use-conversation-transcription-service.md)

Per il codice illustrato di seguito, è necessaria la **versione di conversazione remota 1.8.0**, supportata solo per Java (1.8.0 o versione successiva) in Windows, Linux e Android (solo livello API 26 o superiore).

### <a name="obtaining-the-client-sdk"></a>Ottenere l'SDK del client

È possibile ottenere la **conversazione remota** modificando il file pom.xml come segue.

1. Alla fine del file, prima `</project>`del tag `repositories` di chiusura , creare un elemento con un riferimento al repository Maven per Speech SDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Aggiungere anche `dependencies` un elemento, con remoteconversation-client-sdk 1.8.0 come dipendenza:Also add a element, with the remoteconversation-client-sdk 1.8.0 as a dependency:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Salvare le modifiche

### <a name="sample-transcription-code"></a>Codice di trascrizione di esempio

Dopo aver `conversationId`creato un client di trascrizione della conversazione remota **RemoteConversationTranscriptionClient** nell'applicazione client per eseguire una query sullo stato della trascrizione asincrona. Utilizzare il metodo **getTranscriptionOperation** in **RemoteConversationTranscriptionClient** per ottenere un oggetto [PollerFlux.Use getTranscriptionOperation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) method in RemoteConversationTranscriptionClient to get a PollerFlux object. L'oggetto PollerFlux dirà informazioni sullo stato dell'operazione remota **RemoteConversationTranscriptionOperation** e sul risultato finale **RemoteConversationTranscriptionResult**. Al termine dell'operazione, ottenere **RemoteConversationTranscriptionResult** chiamando **getFinalResult** su un [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). In questo codice stampiamo semplicemente il contenuto del risultato nell'output del sistema.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)
