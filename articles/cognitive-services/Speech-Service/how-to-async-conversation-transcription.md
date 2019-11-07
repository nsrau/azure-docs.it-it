---
title: Trascrizione di conversazioni asincrone (anteprima)-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la trascrizione di conversazioni asincrone usando il servizio di riconoscimento vocale. Disponibile solo per Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 9a2a00a8c76c7b5c02cde623bf93f536b27cf074
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621963"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Trascrizione di conversazioni asincrone (anteprima)

In questo articolo viene illustrata la trascrizione asincrona delle conversazioni usando l'API **RemoteConversationTranscriptionClient** . Se è stata configurata la trascrizione della conversazione per eseguire la trascrizione asincrona e si ha una `conversationId`, è possibile ottenere la trascrizione associata a tale `conversationId` usando l'API **RemoteConversationTranscriptionClient** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Confronto asincrono e in tempo reale + asincrono

Con la trascrizione asincrona è possibile trasmettere l'audio della conversazione, ma non è necessaria una trascrizione restituita in tempo reale. Al contrario, dopo l'invio dell'audio, usare il `conversationId` di `Conversation` per eseguire una query per lo stato della trascrizione asincrona. Quando la trascrizione asincrona è pronta, si otterrà un `RemoteConversationTranscriptionResult`.

Con il tempo reale più asincrono, si ottiene la trascrizione in tempo reale, ma si ottiene anche la trascrizione eseguendo una query con il `conversationId` (simile allo scenario asincrono).

Per completare la trascrizione asincrona sono necessari due passaggi. Il primo passaggio consiste nel caricare l'audio, scegliendo solo asincrono o in tempo reale più asincrono. Il secondo passaggio consiste nell'ottenere i risultati della trascrizione.

## <a name="upload-the-audio"></a>Caricare l'audio

Prima di poter eseguire la trascrizione asincrona, è necessario inviare l'audio al servizio di trascrizione delle conversazioni usando Microsoft cognitive Speech client SDK (versione 1.8.0 o successiva).

Questo esempio di codice Mostra come creare il trascrittore di conversazioni per la modalità solo asincrona. Per trasmettere audio al trascrittore, è necessario aggiungere il codice di streaming audio derivato dalle [conversazioni di trascrizione in tempo reale con l'SDK di riconoscimento vocale](how-to-use-conversation-transcription-service.md). Per informazioni sulle piattaforme supportate e sulle API dei linguaggi, vedere la sezione **limitazioni** di questo argomento.

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
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
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

Se si vuole usare in tempo reale _più_ asincrono, commentare e rimuovere il commento dalle righe di codice appropriate come indicato di seguito:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Ottenere i risultati della trascrizione

Questo passaggio consente di ottenere i risultati della trascrizione asincrona, ma si presuppone che l'elaborazione in tempo reale eventualmente richiesta venga eseguita altrove. Per altre informazioni, vedere [trascrivere conversazioni in tempo reale con l'SDK di riconoscimento vocale](how-to-use-conversation-transcription-service.md).

Per il codice illustrato di seguito, è necessaria la **versione di conversazione remota 1.8.0**, supportata solo per Java (1.8.0 o versione successiva) in Windows, Linux e Android (solo livello API 26 o versione successiva).

### <a name="obtaining-the-client-sdk"></a>Acquisizione dell'SDK client

È possibile ottenere la **conversazione remota** modificando il file POM. XML come indicato di seguito.

1. Alla fine del file, prima del tag di chiusura `</project>`, creare un elemento `repositories` con un riferimento al repository maven per l'SDK di riconoscimento vocale:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Aggiungere anche un elemento `dependencies`, con remoteconversation-client-SDK 1.8.0 come dipendenza:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Salva le modifiche

### <a name="sample-transcription-code"></a>Codice di trascrizione di esempio

Dopo aver creato la `conversationId`, creare un client di trascrizione della conversazione remota **RemoteConversationTranscriptionClient** nell'applicazione client per eseguire una query sullo stato della trascrizione asincrona. Usare il metodo **getTranscriptionOperation** in **RemoteConversationTranscriptionClient** per ottenere un oggetto [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) . L'oggetto PollerFlux includerà informazioni sullo stato dell'operazione remota **RemoteConversationTranscriptionOperation** e il risultato finale **RemoteConversationTranscriptionResult**. Al termine dell'operazione, ottenere **RemoteConversationTranscriptionResult** chiamando **GetFinalResult** su un [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). In questo codice viene semplicemente stampato il contenuto dei risultati nell'output di sistema.

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
