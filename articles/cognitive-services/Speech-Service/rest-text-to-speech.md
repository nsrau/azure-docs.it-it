---
title: Sintesi vocale riferimento all'API REST () - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API REST di sintesi vocale. In questo articolo vengono illustrate le opzioni di autorizzazione, le opzioni di query, come strutturare una richiesta e ricevere una risposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d67d8462c177d19dfa3cebbd0b4b000fbe3f41b8
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57894952"
---
# <a name="text-to-speech-rest-api"></a>API REST di sintesi vocale

I servizi voce consentono di convertire la sintesi vocale usando un'API REST. Ogni endpoint accessibile è associato a un'area. L'applicazione richiede una chiave di sottoscrizione per l'endpoint che si intende usare. L'API REST Sintesi vocale supporta voci neurali e standard, ognuna delle quali supporta a sua volta una lingua e una lingua regionale specifiche, identificate dalle impostazioni locali.

* Per un elenco completo delle voci, vedere [Supporto per le lingue](language-support.md#text-to-speech).
* Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#text-to-speech).

> [!IMPORTANT]
> I costi variano per voci standard, personalizzate e neurali. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Prima di usare questa API, comprendere:

* L'API REST di sintesi vocale richiede un'intestazione dell'autorizzazione. Ciò significa che è necessario completare uno scambio di token per accedere al servizio. Per altre informazioni, vedere [Autenticazione](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di riconoscimento vocale.

| Intestazione | DESCRIZIONE | Obbligatoria / Facoltativa |
|--------|-------------|---------------------|
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per altre informazioni, vedere [Autenticazione](#authentication). | Obbligatorio |
| `Content-Type` | Specifica il tipo di contenuto per il testo specificato. Valore accettato: `application/ssml+xml`. | Obbligatorio |
| `X-Microsoft-OutputFormat` | Specifica il formato di output audio. Per un elenco completo dei valori accettati, vedere [output audio](#audio-outputs). | Obbligatorio |
| `User-Agent` | Il nome applicazione. Il valore fornito deve essere minore di 255 caratteri. | Obbligatorio |

## <a name="audio-outputs"></a>Output audio

Questo è un elenco dei formati audio supportati che vengono inviati in ogni richiesta come intestazione `X-Microsoft-OutputFormat`. Ognuno incorpora una velocità in bit e il tipo di codifica. I servizi di riconoscimento vocale supporta 24 KHz, KHz 16, e gli output audio 8 KHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Se la voce selezionata e il formato di output hanno velocità in bit diverse, il campionamento audio viene ripetuto secondo necessità. Tuttavia, le voci a 24 kHz non supportano i formati di output `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren`.

## <a name="request-body"></a>Corpo della richiesta

Il corpo di ogni richiesta `POST` viene inviato come [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML consente di scegliere la voce e la lingua della sintesi vocale restituita dal servizio di sintesi vocale. Per un elenco completo delle voci supportate, consultare [Supporto per le lingue](language-support.md#text-to-speech).

> [!NOTE]
> Se si usa una voce personalizzata, il corpo della richiesta può essere inviato come testo normale (ASCII o UTF-8).

## <a name="sample-request"></a>Richiesta di esempio

Questa richiesta HTTP utilizza SSML per specificare la lingua e la voce. Il corpo non può superare i 1000 caratteri.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Vedere le guide introduttive per esempi specifici del linguaggio:

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

## <a name="http-status-codes"></a>Codici di stato HTTP

Il codice di stato HTTP di ogni risposta indica esito positivo o errori comuni.

| Stato codice HTTP | DESCRIZIONE | Possibile motivo |
|------------------|-------------|-----------------|
| 200 | OK | La richiesta ha avuto esito positivo; il corpo della risposta è un file audio. |
| 400 | Bad Request | Un parametro obbligatorio è mancante, vuoto o Null. In alternativa, il valore passato a un parametro obbligatorio o facoltativo non è valido. Un problema comune è la lunghezza eccessiva dell'intestazione. |
| 401 | Non autorizzata | La richiesta non è autorizzata. Assicurarsi che la chiave di sottoscrizione o il token sia valido e nell'area corretta. |
| 413 | Entità della richiesta troppo grande | La lunghezza dell'input SSML è maggiore di 1024 caratteri. |
| 429 | Troppe richieste | È stata superata la quota o la frequenza di richieste consentite per la sottoscrizione. |
| 502 | Gateway non valido | Problema di rete o lato server. Può anche indicare intestazioni non valide. |

Se lo stato HTTP è `200 OK`, il corpo della risposta contiene un file audio nel formato richiesto. Questo file può essere riprodotto, poiché è trasferito, salvato in un buffer o salvato in un file.

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
