---
title: Risoluzione dei problemi di Riconoscimento vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Come risolvere i problemi quando si usa Riconoscimento vocale Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: bcb8b421eeb0db215aa21b93a7792e0e978a4f48
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215470"
---
# <a name="troubleshooting-bing-speech"></a>Risoluzione dei problemi di Riconoscimento vocale Bing

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Errore `HTTP 403 Forbidden`

Quando si usa l'API Riconoscimento vocale, viene restituito un errore `HTTP 403 Forbidden`.

### <a name="cause"></a>Causa

Questo errore è causato spesso da problemi di autenticazione. Le richieste di connessione senza intestazione `Ocp-Apim-Subscription-Key` o `Authorization` valida vengono rifiutate dal servizio con una risposta `HTTP 403 Forbidden`.

Se per l'autenticazione si usa la chiave di sottoscrizione, il motivo può essere uno dei seguenti:

- chiave di sottoscrizione mancante o non valida
- superamento della quota d'uso della chiave di sottoscrizione
- campo `Ocp-Apim-Subscription-Key` non impostato nell'intestazione della richiesta al momento della chiamata all'API REST

Se per l'autenticazione si usa il token di autorizzazione, l'errore può essere causato dai motivi seguenti:

- intestazione `Authorization` mancante nella richiesta quando si usa REST
- token di autorizzazione specificato nell'intestazione dell'autorizzazione non valido
- token di autorizzazione scaduto (la scadenza del token di accesso è di 10 minuti)

Per altre informazioni sull'autenticazione, vedere la pagina [Autenticazione](How-to/how-to-authentication.md).

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

#### <a name="verify-that-your-subscription-key-is-valid"></a>Verificare che la chiave di sottoscrizione sia valida

Per la verifica è possibile eseguire questo comando. Sostituire *YOUR_SUBSCRIPTION_KEY* con la propria chiave di sottoscrizione. Se la chiave di sottoscrizione è valida, nella risposta viene visualizzato il token di autorizzazione come token JSON Web (JWT). In caso contrario, nella risposta viene visualizzato un errore.

> [!NOTE]
> Sostituire `YOUR_SUBSCRIPTION_KEY` con la propria chiave di sottoscrizione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

L'esempio usa curl in Linux con bash. Se curl non è disponibile nella piattaforma in uso, è necessario installarlo. L'esempio funziona anche in Cygwin su Windows, Git Bash, zsh e altre shell.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Verificare di usare la stessa chiave di sottoscrizione nell'applicazione o nella richiesta REST usata in precedenza.

#### <a name="verify-the-authorization-token"></a>Verificare il token di autorizzazione

Questo passaggio è necessario solo se per l'autenticazione si usa il token di autorizzazione. Eseguire questo comando per verificare che il token di autorizzazione sia ancora valido. Il comando invia una richiesta POST al servizio da cui prevede di ricevere un messaggio di risposta. Se si continua a ricevere l'errore `403 Forbidden` HTTP, controllare che il token di accesso sia impostato in modo corretto e che non sia scaduto.

> [!IMPORTANT]
> Il token ha una scadenza di 10 minuti.
> [!NOTE]
> Sostituire `YOUR_AUDIO_FILE` con il percorso al file audio pre-registrato e `YOUR_ACCESS_TOKEN` con il token di autorizzazione restituito nel passaggio precedente.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Errore `HTTP 400 Bad Request`

Questo errore viene in genere restituito se il corpo della richiesta contiene dati audio non validi. Attualmente sono supportati solo file WAV.

## <a name="error-http-408-request-timeout"></a>Errore `HTTP 408 Request Timeout`

L'errore viene restituito dopo il timeout se al servizio non viene inviato alcun dato audio. Per l'API REST, è consigliabile inserire i dati audio nel corpo della richiesta.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>L'elemento `RecognitionStatus` nella risposta è `InitialSilenceTimeout`

I dati audio sono in genere la causa del problema. Ad esempio,

- L'audio ha un lungo tempo di silenzio all'inizio. Il servizio interrompe il riconoscimento dopo un certo numero di secondi e restituisce `InitialSilenceTimeout`.
- L'audio usa un formato codec non supportato e pertanto i dati audio vengono trattati come silenzio.
