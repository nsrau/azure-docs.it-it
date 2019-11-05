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
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506978"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Trascrizione di conversazioni asincrone (anteprima)

In questo articolo viene illustrata la trascrizione asincrona delle conversazioni usando l'API **RemoteConversationTranscriptionClient** . Se è stata configurata la trascrizione della conversazione per eseguire la trascrizione asincrona e si ha una `conversationId`, è possibile ottenere la trascrizione associata a tale `conversationId` usando l'API **RemoteConversationTranscriptionClient** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Confronto asincrono e in tempo reale + asincrono

Con la trascrizione asincrona è possibile trasmettere l'audio della conversazione, ma non è necessaria una trascrizione restituita in tempo reale. Al contrario, dopo l'invio dell'audio, usare il `conversationId` di `ConversationTranscriber` per eseguire una query per lo stato della trascrizione asincrona. Quando la trascrizione asincrona è pronta, si otterrà un `RemoteConversationTranscriptionResult`.

Con il tempo reale più asincrono, si ottiene la trascrizione in tempo reale, ma si ottiene anche la trascrizione eseguendo una query con il `conversationId` (simile allo scenario asincrono).

Per completare la trascrizione asincrona sono necessari due passaggi. Il primo passaggio consiste nel caricare l'audio, scegliendo solo asincrono o in tempo reale più asincrono. Il secondo passaggio consiste nell'ottenere i risultati della trascrizione.

## <a name="upload-the-audio"></a>Caricare l'audio

Prima di poter eseguire la trascrizione asincrona, è necessario inviare l'audio alla trascrizione delle conversazioni usando Microsoft cognitive Speech client SDK (versione 1.8.0 o successiva).

Questo esempio di codice Mostra come creare il trascrittore di conversazioni per la modalità solo asincrona. Per trasmettere audio al trascrittore, è necessario aggiungere il codice di streaming audio derivato dalle [conversazioni di trascrizione in tempo reale con l'SDK di riconoscimento vocale](how-to-use-conversation-transcription-service.md). Per informazioni sulle piattaforme supportate e sulle API dei linguaggi, vedere la sezione **limitazioni** di questo argomento.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
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

Per il codice illustrato di seguito, è necessario **remoteconversation-client-SDK versione 1.0.0**, supportato solo per Java (1,8 o versioni successive) in Windows, Linux e Android (livello API 26 o versione successiva).

### <a name="obtaining-the-client-sdk"></a>Acquisizione dell'SDK client

È possibile ottenere **remoteconversation-client-SDK** modificando il file POM. XML come indicato di seguito.

- Alla fine del file, prima del tag di chiusura `</project>`, creare un elemento `repositories` con un riferimento al repository maven per l'SDK di riconoscimento vocale:

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- Aggiungere anche un elemento `dependencies`, con remoteconversation-client-SDK 1.0.0 come dipendenza:

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- Salva le modifiche

### <a name="sample-transcription-code"></a>Codice di trascrizione di esempio

Dopo aver creato la `conversationId`, creare un oggetto operazione remota **RemoteConversationTranscriptionOperation** nel client per eseguire una query sullo stato della trascrizione asincrona. **RemoteConversationTranscriptionOperation** viene esteso dal [Poller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java). Al termine del polling, ottenere **RemoteConversationTranscriptionResult** sottoscrivendo il Poller ed eseguendo una query sull'oggetto. In questo codice viene semplicemente stampato il contenuto dei risultati nell'output di sistema.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)
