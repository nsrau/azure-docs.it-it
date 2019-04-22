---
title: 'Guida introduttiva: Elenco voci sintesi vocale, Node. js - servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa Guida introduttiva si apprenderà come ottenere l'elenco completo delle voci neurale e standard per un'area/endpoint usando Node. js. Viene restituito l'elenco in formato JSON e disponibilità vocale varia in base all'area.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 62187ddbe587a81038f8424b079e3c0c313d1ae2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58887098"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Guida introduttiva: Ottiene l'elenco di voci di sintesi vocale con Node. js

In questa Guida introduttiva si apprenderà come ottenere l'elenco completo delle voci neurale e standard per un'area/endpoint usando Node. js. Viene restituito l'elenco in formato JSON e disponibilità vocale varia in base all'area. Per un elenco delle aree supportate, vedere [aree](regions.md).

Questa Guida introduttiva richiede un [account di servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa di servizi di riconoscimento vocale. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](get-started.md) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Node 8.12.x o versione successiva](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per i servizi Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Creare un progetto e richiedere le dipendenze

Creare un nuovo progetto Node.js con l'IDE o l'editor preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languagesto a file
const fs = require('fs');
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `npm install request request-promise`.

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

Nella sezione successiva, si creerà la funzione per ottenere l'elenco di voci e salvare l'output JSON al file.

## <a name="make-a-request-and-save-the-response"></a>Eseguire una richiesta e salvare la risposta

In questo caso si intende la richiesta di compilazione e salvare l'elenco di voci restituite. Questo esempio presuppone che si usi l'endpoint Stati Uniti occidentali. Se la risorsa è registrata in un'area diversa, assicurarsi di aggiornare il valore di `uri`. Per altre informazioni, vedere [aree di servizi di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Successivamente, aggiungere le intestazioni obbligatorie per la richiesta. Si creerà infine una richiesta da inviare al servizio. Se la richiesta ha esito positivo e viene restituito un codice di 200 stato, la risposta viene scritto in un file.

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

La procedura è quasi terminata. L'ultimo passaggio consiste nel creare una funzione asincrona. Questa funzione verrà leggere la chiave di sottoscrizione da una variabile di ambiente, ottenere un token, attendere il completamento della richiesta, quindi scrivere la risposta JSON al file.

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
