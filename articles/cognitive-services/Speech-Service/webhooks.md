---
title: Webhook - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: I Webhook vengono richiamate HTTP ideale per ottimizzare la soluzione quando si lavora con prolungata dei processi, ad esempio importazioni, adattamento, i test di accuratezza o trascrizioni di lunga esecuzione file in esecuzione.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: fbe6fe25b5ff0cd5148e3bba22dec4648399510d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072300"
---
# <a name="webhooks-for-speech-services"></a>Webhook per i servizi di riconoscimento vocale

I Webhook sono simili a callback HTTP che consentono all'applicazione di accettare i dati dai servizi di riconoscimento vocale quando diventa disponibile. Uso di webhook, è possibile ottimizzare l'uso dell'API REST, eliminando la necessità di eseguire continuamente il polling di una risposta. Nelle prossime sezioni, si apprenderà come usare i webhook con i servizi di riconoscimento vocale.

## <a name="supported-operations"></a>Operazioni supportate

I servizi di riconoscimento vocale supportare i webhook per tutte le operazioni a esecuzione prolungata. Ognuna delle operazioni elencate di seguito può attivare un callback HTTP dopo il completamento. 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Successivamente, è possibile creare un webhook.

## <a name="create-a-webhook"></a>Creare un webhook

È possibile creare un webhook per una trascrizione offline. Lo scenario: un utente dispone di un file audio a esecuzione prolungata che desiderano trascrivere in modo asincrono con l'API di trascrizione di Batch. 

I Webhook possono essere creati da una richiesta POST a https://\<regione\>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks.

I parametri di configurazione per la richiesta vengono forniti in formato JSON:

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
Tutte le richieste POST all'API di trascrizione Batch richiedono un `name`. Il `description` e `properties` parametri sono facoltativi.

Il `Active` proprietà viene utilizzata per passare la richiamata nell'URL di attivare e disattivare senza dover eliminare e ricreare la registrazione del webhook. Se è sufficiente chiamare nuovamente una volta dopo che il processo è completo, quindi eliminare il webhook e switch di `Active` proprietà su false.

Il tipo di evento `TranscriptionCompletion` viene fornito nella matrice di eventi. Verrà eseguita una richiamata all'endpoint quando una trascrizione entra in uno stato terminale (`Succeeded` o `Failed`). Quando si chiama nuovamente all'URL registrato, la richiesta contiene un `X-MicrosoftSpeechServices-Event` intestazione contenente uno dei tipi di evento registrato. Non vi è una richiesta per ogni tipo di evento registrato. 

Vi è un tipo di evento che è possibile sottoscrivere. Si tratta di `Ping` tipo di evento. Viene inviata una richiesta con questo tipo per l'URL al termine della creazione di un webhook quando si usa l'URL di ping (vedere sotto).  

Nella configurazione, il `url` proprietà è obbligatoria. Vengono inviate le richieste POST all'URL. Il `secret` viene usato per creare un hash SHA256 del payload, con il master secret come chiave HMAC. L'hash sia impostato come il `X-MicrosoftSpeechServices-Signature` intestazione quando si chiama nuovamente all'URL registrato. Questa intestazione è con codificata Base64.

Questo esempio viene illustrato come convalidare un payload tramite C#:

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
In questo frammento di codice il `secret` è decodificata e convalidato. Si noterà anche che il tipo di evento del webhook è stato spostato. È attualmente disponibile un evento per ogni trascrizione completato. Il codice ripete cinque volte per ogni evento (con un ritardo secondo uno) prima di rinunciare.

### <a name="other-webhook-operations"></a>Altre operazioni di webhook

Per ottenere registrati tutti i webhook: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Per ottenere un webhook specifico: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Per rimuovere un webhook specifico: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> Nell'esempio precedente, l'area è 'westus'. Questo deve essere sostituito dall'area in cui è stata creata la risorsa di servizi di riconoscimento vocale nel portale di Azure.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping corpo: vuoto

Invia una richiesta POST all'URL registrato. La richiesta contiene un `X-MicrosoftSpeechServices-Event` intestazione con un ping di valore. Se il webhook è stato registrato con un segreto, conterrà un `X-MicrosoftSpeechServices-Signature` intestazione con un hash SHA256 del payload con il master secret come chiave HMAC. L'hash è con codificata Base64. 

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test corpo: vuoto

Invia una richiesta POST all'URL registrato se un'entità per il tipo di evento sottoscritto (trascrizione) è presente nel sistema e si trova nello stato appropriato. Il payload verrà generato dall'ultima entità che sarebbe stato richiamato il webhook. Se non è presente alcuna entità, il POST risponderà con 204. Se è possibile eseguire una richiesta di test, risponderà con 200. Il corpo della richiesta è della stessa forma come la richiesta di recupero per un'entità specifica (ad esempio trascrizione) ha sottoscritto l'hook web. La richiesta avrà il `X-MicrosoftSpeechServices-Event` e `X-MicrosoftSpeechServices-Signature` intestazioni come descritto in precedenza.

### <a name="run-a-test"></a>Eseguire un test

Un rapido test può essere eseguito tramite il sito Web https://bin.webhookrelay.com. Da qui, è possibile ottenere chiamata nuovamente gli URL da passare come parametro per la richiesta HTTP POST per la creazione di un webhook, descritto in precedenza nel documento.

Fare clic su 'Crea Bucket' e seguire sullo schermo le istruzioni per ottenere un hook. Usare quindi le informazioni fornite in questa pagina per registrare l'hook nel servizio di riconoscimento vocale. Il payload di un messaggio di inoltro: nella risposta per il completamento di una trascrizione – appare come segue:

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
Il messaggio contiene l'URL di registrazione e i modelli usati per trascrivere che la registrazione.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
