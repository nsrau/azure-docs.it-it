---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 7ab4d7708dd75ff2e07e77f3be4f996068704797
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486786"
---
## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK per JavaScript<span class="docon docon-navigate-external x-hidden-focus"></span></a>. A seconda della piattaforma, usare le istruzioni seguenti:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web browser <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-voice-signatures"></a>Creare firme vocali

Il primo passaggio consiste nel creare firme vocali per i partecipanti della conversazione in modo che possano essere identificati come relatori univoci. Il `.wav` file audio di input per la creazione di firme vocali deve essere a 16 bit, frequenza di campionamento a 16 kHz e formato canale singolo (mono). La lunghezza consigliata per ogni campione audio è compresa tra 30 secondi e due minuti. Il `.wav` file deve essere un esempio di voce di **una persona** , in modo che venga creato un profilo vocale univoco.

L'esempio seguente illustra come creare una firma vocale [usando l'API REST](https://aka.ms/cts/signaturegenservice) in JavaScript. Si noti che è necessario sostituire le informazioni reali per `subscriptionKey` , `region` e il percorso di un file di esempio `.wav` .

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

L'esecuzione di questo script restituisce una stringa di firma vocale nella variabile `voiceSignatureString` . Eseguire la funzione due volte per avere due stringhe da usare come input per le variabili `voiceSignatureStringUser1` e `voiceSignatureStringUser2` sotto.

> [!NOTE]
> È possibile creare firme vocali **solo** usando l'API REST.

## <a name="transcribe-conversations"></a>Trascrizione di conversazioni

Nell'esempio di codice seguente viene illustrato come trascrivere le conversazioni in tempo reale per due relatori. Si presuppone che siano già state create stringhe di firma vocale per ogni altoparlante, come illustrato in precedenza. Sostituire le informazioni reali per `subscriptionKey` , `region` e il percorso dell' `filepath` audio che si vuole trascrivere.

Questo codice di esempio esegue le operazioni seguenti:

* Consente di creare un flusso push da utilizzare per la trascrizione e di scrivervi il `.wav` file di esempio.
* Crea un oggetto `Conversation` utilizzando `createConversationAsync()` .
* Crea un oggetto `ConversationTranscriber` usando il costruttore.
* Aggiunge i partecipanti alla conversazione. Le stringhe `voiceSignatureStringUser1` e `voiceSignatureStringUser2` dovrebbero essere come output dei passaggi precedenti.
* Registra gli eventi e inizia la trascrizione.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```