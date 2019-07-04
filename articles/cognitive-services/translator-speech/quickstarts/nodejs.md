---
title: 'Guida introduttiva: API Traduzione vocale Node.js'
titlesuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice per iniziare a usare rapidamente l'API Traduzione vocale.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c16b4ca0375376829c7617b488d16151363dc816
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442142"
---
# <a name="quickstart-translator-speech-api-with-nodejs"></a>Guida introduttiva: API Traduzione vocale con Node.js
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Questo articolo illustra come usare l'API Traduzione vocale per tradurre il testo parlato in un file con estensione wav.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo codice è necessario [Node.js 6](https://nodejs.org/en/download/).

Per Node.js è necessario installare il [pacchetto Websocket](https://www.npmjs.com/package/websocket).

È inoltre necessario un file con estensione wav denominato "speak.wav" nella stessa cartella del file eseguibile che si compila dal codice seguente. Il file con estensione wav deve essere in formato PCM standard, a 16 bit, 16 kHz, mono. È possibile ottenere questo file .wav dall'[API sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

È necessario disporre di un [account delle API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con l'**API Traduzione vocale Microsoft**. È infine necessaria una chiave di sottoscrizione a pagamento configurata nel [dashboard di Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Traduzione vocale

Il codice seguente traduce il parlato da una lingua a un'altra.

1. Creare un nuovo progetto Node.js nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Text to Speech API. See:
https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {

    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio file is finished.
At 32 bytes per millisecond, this is 100 seconds of silence. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });

    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);

        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });

        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**Risposta della traduzione vocale**

Se il risultato è positivo, viene creato un file denominato "speak2.wav" contenente la traduzione del testo parlato in "speak.wav".

[Torna all'inizio](#HOLTop)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione su Traduzione vocale](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Vedere anche

[Panoramica di Traduzione vocale](../overview.md)
[Informazioni di riferimento sull'API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
