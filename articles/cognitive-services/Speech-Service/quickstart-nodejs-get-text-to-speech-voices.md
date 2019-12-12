---
title: 'Guida introduttiva: Elencare le voci per la sintesi vocale, Node.js - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questa guida di avvio rapido si apprenderà come ottenere l'elenco completo delle voci standard e neurali per un'area o un endpoint usando Node.js. L'elenco viene restituito in formato JSON e la disponibilità delle voci varia in base all'area.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 7a929794ffaea4f863ffaef7227e58c7ccf901f0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976571"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Guida introduttiva: Ottenere l'elenco delle voci per la sintesi vocale usando Node.js

In questa guida di avvio rapido si apprenderà come ottenere l'elenco completo delle voci standard e neurali per un'area o un endpoint usando Node.js. L'elenco viene restituito in formato JSON e la disponibilità delle voci varia in base all'area. Per un elenco delle aree supportate, vedere [Aree](regions.md).

Per questo argomento di avvio rapido, è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa del servizio Voce. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](get-started.md) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Node 8.12.x o versione successiva](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Creare un progetto e richiedere le dipendenze

Creare un nuovo progetto Node.js con l'IDE o l'editor preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `npm install request request-promise`.

## <a name="get-an-access-token"></a>Ottenere un token di accesso

L'API REST Sintesi vocale necessita di un token di accesso per l'autenticazione. Per ottenere un token di accesso, è necessario uno scambio. Questa funzione scambia la chiave di sottoscrizione del servizio Voce con un token di accesso usando l'endpoint `issueToken`.

L'esempio presuppone che la sottoscrizione del servizio Voce si trovi nell'area Stati Uniti occidentali. Se si usa un'area diversa, aggiornare il valore per `uri`. Per un elenco completo, vedere [Aree](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

Nella prossima sezione si creerà la funzione per ottenere l'elenco di voci e salvare l'output JSON in un file.

## <a name="make-a-request-and-save-the-response"></a>Eseguire una richiesta e salvare la risposta

In questa sezione si creerà la richiesta e si salverà l'elenco delle voci restituite. Questo esempio presuppone che si usi l'endpoint Stati Uniti occidentali. Se la risorsa è registrata in un'area diversa, assicurarsi di aggiornare il valore di `uri`. Per altre informazioni, vedere [Aree del servizio Voce](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Successivamente, aggiungere le intestazioni obbligatorie per la richiesta. Si creerà infine una richiesta da inviare al servizio. Se la richiesta ha esito positivo e viene restituito il codice di stato 200, la risposta viene scritta su file.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è quasi terminata. L'ultimo passaggio consiste nel creare una funzione asincrona. Questa funzione legge la chiave della sottoscrizione da una variabile di ambiente, ottiene un token, attende il completamento della richiesta e quindi scrive la risposta JSON in un file.

Se non si ha familiarità con le variabili di ambiente o si preferisce eseguire test con la chiave della sottoscrizione hardcoded come stringa, sostituire `process.env.SPEECH_SERVICE_KEY` con la chiave della sottoscrizione come stringa.

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
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla directory del progetto ed eseguire:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Node.js su GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Vedere anche

* [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md)
* [Esempi di campioni vocali per la creazione di una voce personalizzata](record-custom-voice-samples.md)
