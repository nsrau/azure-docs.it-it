---
title: 'Avvio rapido: Eseguire la sintesi vocale, Node.js - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si apprenderà come eseguire la sintesi vocale di testo usando Node.js e l'API REST Sintesi vocale. Il testo di esempio incluso in questo articolo è strutturato come SSML (Speech Synthesis Markup Language). Questo formato consente di scegliere la voce e la lingua della risposta di sintesi vocale.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: e2319262b669cd5a03fa3e50bf3e534e7974c72d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550624"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Avvio rapido: Eseguire la sintesi vocale con Node.js

In questa guida introduttiva si apprenderà come eseguire la sintesi vocale di testo usando Node.js e l'API REST Sintesi vocale. In questo articolo il corpo della richiesta è strutturato nel formato [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md), che consente di scegliere la voce e la lingua della risposta.

Per questo avvio rapido è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa del servizio Voce. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](get-started.md) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Node 8.12.x o versione successiva](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Creare un progetto e richiedere le dipendenze

Creare un nuovo progetto Node.js con l'IDE o l'editor preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `tts.js`.

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');

```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `npm install request readline-sync`.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Impostare la chiave di sottoscrizione e creare un prompt per la sintesi vocale

Nelle sezioni successive si creeranno le funzioni per gestire l'autorizzazione, chiamare l'API Sintesi vocale e convalidare la risposta. Si inizierà aggiungendo una chiave di sottoscrizione e creando una richiesta di input di testo.

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>Ottenere un token di accesso

L'API REST Sintesi vocale necessita di un token di accesso per l'autenticazione. Per ottenere un token di accesso, è necessario uno scambio. In questo esempio viene scambiata la chiave di sottoscrizione del servizio Voce con un token di accesso usando l'endpoint `issueToken`.

Questa funzione accetta due argomenti, la chiave di sottoscrizione dei Servizi di riconoscimento vocale e una funzione di callback. Dopo che la funzione ha ottenuto un token di accesso, passa il valore alla funzione di callback. Nella sezione successiva si creerà la funzione per chiamare l'API di sintesi vocale e salvare la risposta vocale sintetizzata.

L'esempio presuppone che la sottoscrizione del servizio Voce sia nell'area Stati Uniti occidentali. Se si usa un'area diversa, aggiornare il valore per `uri`. Per un elenco completo, vedere [Aree](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiare questo codice nel progetto:

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> Per altre informazioni sull'autenticazione, vedere [Eseguire l'autenticazione con un token di autenticazione](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Eseguire una richiesta e salvare la risposta

In questa sezione si creerà la richiesta all'API di sintesi vocale e si salverà la risposta vocale. Questo esempio presuppone che si usi l'endpoint Stati Uniti occidentali. Se la risorsa è registrata in un'area diversa, assicurarsi di aggiornare il valore di `uri`. Per altre informazioni, vedere [Aree del servizio Voce](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

È quindi necessario aggiungere le intestazioni obbligatorie per la richiesta. Assicurarsi di aggiornare `User-Agent` con il nome della risorsa, che si trova nel portale di Azure, e impostare `X-Microsoft-OutputFormat` sul formato di output audio preferito. Per un elenco completo dei formati di output, vedere [Output audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Creare quindi il corpo della richiesta nel formato SSML (Speech Synthesis Markup Language). Questo esempio definisce la struttura e usa l'input `text` creato in precedenza.

>[!NOTE]
> Questo esempio usa il carattere voce `JessaRUS`. Per un elenco completo di voci e lingue fornite da Microsoft, vedere [Supporto per le lingue](language-support.md).
> Se si vuole creare una voce unica, riconoscibile per il proprio marchio, vedere [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md).

Si creerà infine una richiesta da inviare al servizio. Se la richiesta ha esito positivo e viene restituito il codice di stato 200, la risposta di sintesi vocale viene scritta come `sample.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
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
        body: '<speak version=\'1.0\' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang=\'en-US\'>\n<voice  name=\'Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)\'>' + text + '</voice> </speak>'
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è quasi terminata. L'ultimo passaggio consiste nel chiamare la funzione `textToSpeech`.

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
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
> [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>Vedere anche 

* [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md)
* [Esempi di campioni vocali per la creazione di una voce personalizzata](record-custom-voice-samples.md)
