---
title: 'Guida introduttiva: Eseguire la sintesi vocale, Node.js - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si apprenderà come eseguire la sintesi vocale di testo usando Node.js e l'API REST Sintesi vocale. Il testo di esempio incluso in questo articolo è strutturato come SSML (Speech Synthesis Markup Language). Questo formato consente di scegliere la voce e la lingua della risposta di sintesi vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a7713576565ca2632d7d91857040ece4d02c411b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621948"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Guida introduttiva: Eseguire la sintesi vocale con Node.js

In questa guida introduttiva si apprenderà come eseguire la sintesi vocale di testo usando Node.js e l'API REST Sintesi vocale. In questo articolo il corpo della richiesta è strutturato nel formato [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md), che consente di scegliere la voce e la lingua della risposta.

Questa Guida introduttiva richiede un [account di servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa di servizi di riconoscimento vocale. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](get-started.md) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Node 8.12.x o versione successiva](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per i servizi Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Creare un progetto e richiedere le dipendenze

Creare un nuovo progetto Node.js con l'IDE o l'editor preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `tts.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `npm install request request-promise xmlbuilder readline-sync`.

## <a name="get-an-access-token"></a>Ottenere un token di accesso

L'API REST Sintesi vocale necessita di un token di accesso per l'autenticazione. Per ottenere un token di accesso, è necessario uno scambio. Questa funzione lo scambio di chiave di sottoscrizione di servizi di riconoscimento vocale per un token di accesso tramite il `issueToken` endpoint.

In questo esempio si presuppone che la sottoscrizione di servizi di riconoscimento vocale è nell'area Stati Uniti occidentali. Se si usa un'area diversa, aggiornare il valore per `uri`. Per un elenco completo, vedere [Aree](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiare questo codice nel progetto:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Per altre informazioni sull'autenticazione, vedere [Eseguire l'autenticazione con un token di autenticazione](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Nella sezione successiva si creerà la funzione per chiamare l'API di sintesi vocale e salvare la risposta vocale sintetizzata.

## <a name="make-a-request-and-save-the-response"></a>Eseguire una richiesta e salvare la risposta

In questa sezione si creerà la richiesta all'API di sintesi vocale e si salverà la risposta vocale. Questo esempio presuppone che si usi l'endpoint Stati Uniti occidentali. Se la risorsa è registrata in un'area diversa, assicurarsi di aggiornare il valore di `uri`. Per altre informazioni, vedere [aree di servizi di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

È quindi necessario aggiungere le intestazioni obbligatorie per la richiesta. Assicurarsi di aggiornare `User-Agent` con il nome della risorsa, che si trova nel portale di Azure, e impostare `X-Microsoft-OutputFormat` sul formato di output audio preferito. Per un elenco completo dei formati di output, vedere [Output audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Creare quindi il corpo della richiesta nel formato SSML (Speech Synthesis Markup Language). Questo esempio definisce la struttura e usa l'input `text` creato in precedenza.

>[!NOTE]
> Questo esempio usa il carattere voce `JessaRUS`. Per un elenco completo di voci e lingue fornite da Microsoft, vedere [Supporto per le lingue](language-support.md).
> Se si vuole creare una voce unica, riconoscibile per il proprio marchio, vedere [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md).

Si creerà infine una richiesta da inviare al servizio. Se la richiesta ha esito positivo e viene restituito il codice di stato 200, la risposta di sintesi vocale viene scritta come `TTSOutput.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è quasi terminata. L'ultimo passaggio consiste nel creare una funzione asincrona. Questa funzione leggerà la chiave di sottoscrizione dalla variabile di ambiente, messaggio di richiesta per il testo, ottenere un token, attendere che la richiesta di completamento, quindi convertire la sintesi vocale e salvare l'audio come un wav.

Se si ha familiarità con le variabili di ambiente o si preferisce testare con la chiave di sottoscrizione hardcoded sotto forma di stringa, sostituire `process.env.SPEECH_SERVICE_KEY` con la chiave di sottoscrizione sotto forma di stringa.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di sintesi vocale di esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla directory del progetto ed eseguire:

```console
node tts.js
```

Quando richiesto, digitare un testo qualsiasi di cui eseguire la sintesi vocale. Se l'operazione ha esito positivo, il file della sintesi vocale viene salvato nella cartella del progetto. Riprodurlo con il lettore multimediale preferito.

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Node.js su GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Vedere anche 

* [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md)
* [Esempi di campioni vocali per la creazione di una voce personalizzata](record-custom-voice-samples.md)
