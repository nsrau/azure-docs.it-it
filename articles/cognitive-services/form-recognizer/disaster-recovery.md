---
title: Linee guida sul ripristino di emergenza per il sistema di riconoscimento di Azure form
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API Copia modello per eseguire il backup delle risorse di riconoscimento del modulo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 2e5b32421a04e09bd32d2bba21ff4faf920d84dd
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221845"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Eseguire il backup e il ripristino dei modelli di riconoscimento moduli

Quando si crea una risorsa di riconoscimento del modulo nella portale di Azure, è necessario specificare un'area. Da quel punto in poi, la risorsa e tutte le relative operazioni vengono mantenute associate a questa particolare area del server di Azure. È raro, ma non impossibile, incontrare un problema di rete che raggiunge un'intera area. Se la soluzione deve essere sempre disponibile, è necessario progettarla per eseguire il failover in un'altra area oppure suddividere il carico di lavoro tra due o più aree. Entrambi gli approcci richiedono almeno due risorse di riconoscimento form in aree diverse e la possibilità di sincronizzare i [modelli personalizzati](./quickstarts/curl-train-extract.md) tra le aree.

L'API di copia Abilita questo scenario consentendo di copiare modelli personalizzati da un account di riconoscimento form o in altri, che possono esistere in qualsiasi area geografica supportata. Questa guida illustra come usare l'API REST di copia con cURL. Per eseguire le richieste, è anche possibile usare un servizio di richiesta HTTP, ad esempio il poster.

## <a name="business-scenarios"></a>Scenari aziendali

Se l'app o l'azienda dipende dall'uso di un modello personalizzato di riconoscimento moduli, è consigliabile copiare il modello in un altro account di riconoscimento form in un'altra area. Se si verifica un'interruzione a livello di area, è possibile accedere al modello nell'area in cui è stato copiato.

##  <a name="prerequisites"></a>Prerequisiti

1. Risorse di Azure per il riconoscimento di due moduli in diverse aree di Azure. Se non sono disponibili, passare alla portale di Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" creare una nuova risorsa di riconoscimento del modulo " target="_blank"> creare una nuova risorsa di riconoscimento del modulo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
1. La chiave della sottoscrizione, l'URL dell'endpoint e l'ID sottoscrizione della risorsa di riconoscimento del modulo. È possibile trovare questi valori nella scheda **Panoramica** della risorsa nel portale di Azure.


## <a name="copy-api-overview"></a>Panoramica dell'API di copia

Il processo per la copia di un modello personalizzato è costituito dai passaggi seguenti:

1. Viene prima di tutto eseguita una richiesta di autorizzazione di copia alla risorsa di destinazione &mdash; , ovvero la risorsa che riceverà il modello copiato. Si ottiene l'URL del modello di destinazione appena creato, che riceverà i dati copiati.
1. Successivamente si invia la richiesta di copia alla risorsa di origine &mdash; che contiene il modello da copiare. Verrà restituito un URL su cui è possibile eseguire una query per tenere traccia dell'avanzamento dell'operazione.
1. Si useranno le credenziali della risorsa di origine per eseguire una query sull'URL di stato fino a quando l'operazione ha esito positivo. È anche possibile eseguire una query sul nuovo ID modello nella risorsa di destinazione per ottenere lo stato del nuovo modello.

## <a name="generate-copy-authorization-request"></a>Genera richiesta di autorizzazione copia

La richiesta HTTP seguente ottiene l'autorizzazione di copia dalla risorsa di destinazione. È necessario immettere l'endpoint e la chiave della risorsa di destinazione come intestazioni.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Si otterrà una `201\Created` risposta con un `modelId` valore nel corpo. Questa stringa è l'ID del modello appena creato (vuoto). `accessToken`È necessario che l'API copi i dati in questa risorsa e che il `expirationDateTimeTicks` valore sia la scadenza del token. Salvare tutti e tre questi valori in una posizione sicura.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Avvia operazione di copia

La richiesta HTTP seguente avvia l'operazione di copia sulla risorsa di origine. È necessario immettere l'endpoint e la chiave della risorsa di origine come intestazioni. Si noti che l'URL della richiesta contiene l'ID del modello di origine che si vuole copiare.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Il corpo della richiesta deve avere il formato seguente. È necessario immettere l'ID risorsa e il nome dell'area della risorsa di destinazione. Sono necessari anche l'ID modello, il token di accesso e il valore di scadenza copiati dal passaggio precedente.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
}
```

Si otterrà una `202\Accepted` risposta con un'intestazione Operation-location. Questo valore è l'URL che verrà usato per tenere traccia dello stato di avanzamento dell'operazione. Copiarlo in un percorso temporaneo per il passaggio successivo.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

## <a name="track-copy-progress"></a>Traccia stato copia

Tenere traccia dello stato di avanzamento eseguendo una query sull'endpoint della risorsa di origine per **ottenere copia modello** .

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

La risposta varia a seconda dello stato dell'operazione. Cercare il `"status"` campo nel corpo JSON. Se si automatizza questa chiamata API in uno script, è consigliabile eseguire una query sull'operazione una volta al secondo.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="optional-track-the-target-model-id"></a>Opzionale Tenere traccia dell'ID modello di destinazione 

È anche possibile usare l'API per **ottenere un modello personalizzato** per tenere traccia dello stato dell'operazione eseguendo una query sul modello di destinazione. Chiamare questa API usando l'ID del modello di destinazione copiato nel primo passaggio.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Nel corpo della risposta verranno visualizzate le informazioni sul modello. Controllare il `"status"` campo per lo stato del modello.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>codice di esempio cURL

I frammenti di codice seguenti usano cURL per eseguire le chiamate API descritte nei passaggi precedenti. Sarà comunque necessario inserire gli ID del modello e le informazioni sulla sottoscrizione specifiche per le proprie risorse.

### <a name="generate-copy-authorization-request"></a>Genera richiesta di autorizzazione copia

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Avvia operazione di copia

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Traccia stato copia

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0-preview/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come usare l'API di copia per eseguire il backup dei modelli personalizzati in una risorsa di riconoscimento del modulo secondario. Esplorare quindi la documentazione di riferimento per le API per vedere le altre operazioni che è possibile eseguire con il riconoscimento del modulo.
* [Documentazione di riferimento delle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)