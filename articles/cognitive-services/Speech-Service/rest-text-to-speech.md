---
title: Informazioni di riferimento sull'API sintesi vocale (REST)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API REST di sintesi vocale. In questo articolo vengono illustrate le opzioni di autorizzazione, le opzioni di query, come strutturare una richiesta e ricevere una risposta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 77a8321ba8bac0ecaf577bce6c3c05d10508128e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "96020161"
---
# <a name="text-to-speech-rest-api"></a>API REST di sintesi vocale

Il servizio riconoscimento vocale consente di [convertire il testo in sintesi vocale](#convert-text-to-speech) e [ottenere un elenco di voci supportate](#get-a-list-of-voices) per un'area usando un set di API REST. Ogni endpoint disponibile è associato a un'area. È necessaria una chiave di sottoscrizione per l'endpoint/area che si prevede di usare.

L'API REST Sintesi vocale supporta voci neurali e standard, ognuna delle quali supporta a sua volta una lingua e una lingua regionale specifiche, identificate dalle impostazioni locali.

* Per un elenco completo delle voci, vedere [Supporto per le lingue](language-support.md#text-to-speech).
* Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#text-to-speech).

> [!IMPORTANT]
> I costi variano per voci standard, personalizzate e neurali. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Prima di usare questa API, comprendere quanto segue:

* L'API REST di sintesi vocale richiede un'intestazione dell'autorizzazione. Ciò significa che è necessario completare uno scambio di token per accedere al servizio. Per altre informazioni, vedere [Autenticazione](#authentication).

> [!TIP]
> Vedere la [documentazione](../../azure-government/compare-azure-government-global-azure.md) di Azure per enti pubblici per gli endpoint di Fairfax (Government cloud).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Ottenere un elenco di voci

L' `voices/list` endpoint consente di ottenere un elenco completo delle voci per un'area o un endpoint specifico.

### <a name="regions-and-endpoints"></a>Aree ed endpoint

| Region | Endpoint |
|--------|----------|
| Australia orientale | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brasile meridionale | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canada centrale | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Stati Uniti centrali | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Asia orientale | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Stati Uniti orientali | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Stati Uniti orientali 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Francia centrale | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| India centrale | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Giappone orientale | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Corea centrale | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Stati Uniti centro-settentrionali | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa settentrionale | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Stati Uniti centro-meridionali | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Asia sud-orientale | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Regno Unito meridionale | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa occidentale | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Stati Uniti occidentali | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Stati Uniti occidentali 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di sintesi vocale.

| Intestazione | Descrizione | Obbligatoria / Facoltativa |
|--------|-------------|---------------------|
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per altre informazioni, vedere [Autenticazione](#authentication). | Necessario |

### <a name="request-body"></a>Corpo della richiesta

Non è necessario un corpo per `GET` le richieste a questo endpoint.

### <a name="sample-request"></a>Richiesta di esempio

Questa richiesta richiede solo un'intestazione di autorizzazione.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Risposta di esempio

Questa risposta è stata troncata per illustrare la struttura di una risposta.

> [!NOTE]
> La disponibilità vocale varia in base all'area/endpoint.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)",
        "ShortName": "zh-CN-XiaoxiaoNeural",
        "Gender": "Female",
        "Locale": "zh-CN",
        "SampleRateHertz": "24000",
        "VoiceType": "Neural"
    },

    ...
]
```

### <a name="http-status-codes"></a>Codici di stato HTTP

Il codice di stato HTTP di ogni risposta indica esito positivo o errori comuni.

| Codice di stato HTTP | Descrizione | Possibile motivo |
|------------------|-------------|-----------------|
| 200 | OK | La richiesta è stata completata. |
| 400 | Bad Request | Un parametro obbligatorio è mancante, vuoto o Null. In alternativa, il valore passato a un parametro obbligatorio o facoltativo non è valido. Un problema comune è la lunghezza eccessiva dell'intestazione. |
| 401 | Non autorizzata | La richiesta non è autorizzata. Assicurarsi che la chiave di sottoscrizione o il token sia valido e nell'area corretta. |
| 429 | Troppe richieste | È stata superata la quota o la frequenza di richieste consentite per la sottoscrizione. |
| 502 | Gateway non valido    | Problema di rete o lato server. Può anche indicare intestazioni non valide. |


## <a name="convert-text-to-speech"></a>Conversione sintesi vocale

L' `v1` endpoint consente di convertire sintesi vocale mediante [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Aree ed endpoint

Queste aree sono supportate per la trascrizione vocale usando l'API REST. Assicurarsi di selezionare l'endpoint corrispondente all'area della propria sottoscrizione.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di sintesi vocale.

| Intestazione | Descrizione | Obbligatoria / Facoltativa |
|--------|-------------|---------------------|
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per altre informazioni, vedere [Autenticazione](#authentication). | Necessario |
| `Content-Type` | Specifica il tipo di contenuto per il testo specificato. Valore accettato: `application/ssml+xml`. | Necessario |
| `X-Microsoft-OutputFormat` | Specifica il formato di output audio. Per un elenco completo dei valori accettati, vedere [output audio](#audio-outputs). | Necessario |
| `User-Agent` | Nome dell'applicazione. Il valore specificato deve essere inferiore a 255 caratteri. | Necessario |

### <a name="audio-outputs"></a>Output audio

Questo è un elenco dei formati audio supportati che vengono inviati in ogni richiesta come intestazione `X-Microsoft-OutputFormat`. Ognuno incorpora una velocità in bit e il tipo di codifica. Il servizio di riconoscimento vocale supporta gli output audio a 24 kHz, 16 kHz e 8 kHz.

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
```

> [!NOTE]
> Se la voce selezionata e il formato di output hanno velocità in bit diverse, il campionamento audio viene ripetuto secondo necessità. OGG-24kHz-16 bit-mono-Opus può essere decodificato con il [codec Opus](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Corpo della richiesta

Il corpo di ogni richiesta `POST` viene inviato come [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML consente di scegliere la voce e la lingua della sintesi vocale restituita dal servizio di sintesi vocale. Per un elenco completo delle voci supportate, consultare [Supporto per le lingue](language-support.md#text-to-speech).

> [!NOTE]
> Se si usa una voce personalizzata, il corpo della richiesta può essere inviato come testo normale (ASCII o UTF-8).

### <a name="sample-request"></a>Richiesta di esempio

Questa richiesta HTTP utilizza SSML per specificare la lingua e la voce. Se la lunghezza del corpo è lunga e l'audio risultante supera 10 minuti, viene troncato a 10 minuti. In altre parole, la lunghezza audio non può superare i 10 minuti.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Vedere le guide introduttive per esempi specifici della lingua:

* [.NET Core, C #](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](./get-started-text-to-speech.md?pivots=programming-language-python)
* [Node.js](./get-started-text-to-speech.md)

### <a name="http-status-codes"></a>Codici di stato HTTP

Il codice di stato HTTP di ogni risposta indica esito positivo o errori comuni.

| Codice di stato HTTP | Descrizione | Possibile motivo |
|------------------|-------------|-----------------|
| 200 | OK | La richiesta ha avuto esito positivo; il corpo della risposta è un file audio. |
| 400 | Bad Request | Un parametro obbligatorio è mancante, vuoto o Null. In alternativa, il valore passato a un parametro obbligatorio o facoltativo non è valido. Un problema comune è la lunghezza eccessiva dell'intestazione. |
| 401 | Non autorizzata | La richiesta non è autorizzata. Assicurarsi che la chiave di sottoscrizione o il token sia valido e nell'area corretta. |
| 413 | Entità della richiesta troppo grande | La lunghezza dell'input SSML è maggiore di 1024 caratteri. |
| 415 | Tipo di supporto non supportato | È possibile che sia `Content-Type` stato specificato un errore. `Content-Type` deve essere impostato su `application/ssml+xml` . |
| 429 | Troppe richieste | È stata superata la quota o la frequenza di richieste consentite per la sottoscrizione. |
| 502 | Gateway non valido    | Problema di rete o lato server. Può anche indicare intestazioni non valide. |

Se lo stato HTTP è `200 OK`, il corpo della risposta contiene un file audio nel formato richiesto. Questo file può essere riprodotto, poiché è trasferito, salvato in un buffer o salvato in un file.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account Azure gratuito](https://azure.microsoft.com/free/cognitive-services/)
- [Sintesi asincrona per audio a lungo termine](./long-audio-api.md)
- [Introduzione a Voce personalizzata](how-to-custom-voice.md)