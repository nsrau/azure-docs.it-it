---
title: Webhook-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: I webhook sono richiamati HTTP ideali per l'ottimizzazione della soluzione quando si gestiscono processi a esecuzione prolungata come importazione, adattamento, test di accuratezza o trascrizioni di file a esecuzione prolungata.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558801"
---
# <a name="webhooks-for-speech-services"></a>Webhook per servizi vocali

I webhook sono come callback HTTP che consentono all'applicazione di accettare i dati dai servizi di riconoscimento vocale quando diventano disponibili. Usando i webhook, è possibile ottimizzare l'uso delle API REST eliminando la necessità di eseguire continuamente il polling di una risposta. Nelle prossime sezioni verrà illustrato come usare i webhook con i servizi di riconoscimento vocale.

## <a name="supported-operations"></a>Operazioni supportate

I servizi di riconoscimento vocale supportano i webhook per tutte le operazioni a esecuzione prolungata. Ognuna delle operazioni elencate di seguito può attivare un callback HTTP al termine del completamento.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Verrà ora creato un webhook.

## <a name="create-a-webhook"></a>Creare un webhook

Viene ora creato un webhook per una trascrizione offline. Scenario: un utente dispone di un file audio con esecuzione prolungata che si vuole trascrivere in modo asincrono con l'API di trascrizione batch.

I webhook possono essere creati effettuando una richiesta post a https://\<Region\>. Cris.ai/API/speechtotext/V2.1/Transcriptions/hooks.

I parametri di configurazione per la richiesta vengono forniti come JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Tutte le richieste POST all'API di trascrizione batch `name`richiedono. I `description` parametri `properties` e sono facoltativi.

La `Active` proprietà viene usata per attivare e disattivare la chiamata all'URL senza dover eliminare e ricreare la registrazione del webhook. Se è necessario chiamare di nuovo una volta al termine del processo, eliminare il webhook e impostare la `Active` proprietà su false.

Il tipo `TranscriptionCompletion` di evento viene fornito nella matrice di eventi. Verrà richiamato all'endpoint quando una trascrizione entra in uno stato terminale (`Succeeded` o `Failed`). Quando si richiama l'URL registrato, la richiesta conterrà un' `X-MicrosoftSpeechServices-Event` intestazione contenente uno dei tipi di evento registrati. È presente una richiesta per ogni tipo di evento registrato.

Esiste un tipo di evento che non è possibile sottoscrivere. Si tratta del `Ping` tipo di evento. Una richiesta con questo tipo viene inviata all'URL al termine della creazione di un webhook quando si usa l'URL ping (vedere di seguito).  

Nella configurazione, la `url` proprietà è obbligatoria. Le richieste POST vengono inviate a questo URL. `secret` Viene utilizzato per creare un hash SHA256 del payload, con il segreto come chiave HMAC. L'hash viene impostato come `X-MicrosoftSpeechServices-Signature` intestazione quando si richiama l'URL registrato. Questa intestazione è con codifica Base64.

Questo esempio illustra come convalidare un payload usando C#:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
In questo frammento di codice `secret` , la viene decodificata e convalidata. Si noterà anche che il tipo di evento webhook è stato cambiato. Attualmente esiste un evento per ogni trascrizione completata. Il codice esegue un nuovo tentativo cinque volte per ogni evento (con un ritardo di un secondo) prima di rinunciare.

### <a name="other-webhook-operations"></a>Altre operazioni di Webhook

Per ottenere tutti i webhook registrati: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Per ottenere un webhook specifico: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Per rimuovere un webhook specifico: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> Nell'esempio precedente, l'area è "westus". Questa operazione deve essere sostituita dall'area in cui è stata creata la risorsa di servizi vocali nel portale di Azure.

Corpo https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping post: vuoto

Invia una richiesta POST all'URL registrato. La richiesta contiene un' `X-MicrosoftSpeechServices-Event` intestazione con un valore ping. Se il webhook è stato registrato con un segreto, conterrà un' `X-MicrosoftSpeechServices-Signature` intestazione con un hash SHA256 del payload con il segreto come chiave HMAC. L'hash è codificato in base 64.

Corpo https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test post: vuoto

Invia una richiesta POST all'URL registrato se un'entità per il tipo di evento sottoscritto (trascrizione) è presente nel sistema ed è nello stato appropriato. Il payload verrà generato dall'ultima entità che avrebbe richiamato l'hook Web. Se non è presente alcuna entità, il POST risponderà con 204. Se è possibile effettuare una richiesta di test, risponderà con 200. Il corpo della richiesta ha la stessa forma della richiesta GET per un'entità specifica sottoscritta dall'hook Web (per la trascrizione dell'istanza). La richiesta avrà le `X-MicrosoftSpeechServices-Event` intestazioni e `X-MicrosoftSpeechServices-Signature` come descritto in precedenza.

### <a name="run-a-test"></a>Eseguire un test

È possibile eseguire un test rapido usando il sito https://bin.webhookrelay.com Web. Da qui è possibile ottenere gli URL di chiamata per passare come parametro al POST HTTP per la creazione di un webhook descritto in precedenza nel documento.

Fare clic su "Crea bucket" e seguire le istruzioni visualizzate per ottenere un hook. Usare quindi le informazioni fornite in questa pagina per registrare l'hook con il servizio di riconoscimento vocale. Il payload di un messaggio di inoltro, in risposta al completamento di una trascrizione, è simile al seguente:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
Il messaggio contiene l'URL di registrazione e i modelli usati per trascrivere la registrazione.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
