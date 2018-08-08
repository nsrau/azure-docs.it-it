---
title: Risoluzione dei problemi di Speech SDK di Servizi cognitivi
description: Risoluzione dei problemi per Speech SDK di Servizi cognitivi
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284123"
---
# <a name="troubleshooting-speech-services-sdk"></a>Risoluzione dei problemi relativi a Speech Services SDK

Questo articolo fornisce informazioni che consentono di risolvere i problemi riscontrati quando si usa Speech SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Errore `WebSocket Upgrade failed with an authentication error (403).`

È possibile che l'endpoint non sia quello corretto per l'area o il servizio. Controllare l'URI per verificare che sia corretto. Vedere anche la sezione successiva, perché potrebbe anche trattarsi di un problema relativo alla chiave di sottoscrizione o al token di autorizzazione.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Errore `HTTP 403 Forbidden` o errore `HTTP 401 Unauthorized`

Questo errore è causato spesso da problemi di autenticazione. Le richieste di connessione senza un'intestazione `Ocp-Apim-Subscription-Key` o `Authorization` valida vengono rifiutate con lo stato 401 o 403.

* Se per l'autenticazione si usa una chiave di sottoscrizione, la causa può essere una delle seguenti:

    - chiave di sottoscrizione mancante o non valida
    - è stata superata la quota di utilizzo della sottoscrizione

* Se per l'autenticazione si usa un token di autorizzazione, la causa può essere una delle seguenti:

    - token di autorizzazione non valido
    - token di autorizzazione scaduto

### <a name="validate-your-subscription-key"></a>Convalidare la chiave di sottoscrizione

È possibile verificare di avere una chiave di sottoscrizione valida eseguendo uno dei comandi riportati di seguito.

> [!NOTE]
> Sostituire `YOUR_SUBSCRIPTION_KEY` e `YOUR_REGION` rispettivamente con la propria chiave di sottoscrizione e area associata.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

### <a name="validate-an-authorization-token"></a>Convalidare un token di autorizzazione

Se si usa un token di autorizzazione per l'autenticazione, eseguire uno dei comandi seguenti per verificare che il token di autorizzazione sia ancora valido. I token sono validi per 10 minuti.

> [!NOTE]
> Sostituire `YOUR_AUDIO_FILE` con il percorso al file audio pre-registrato, `YOUR_ACCESS_TOKEN` con il token di autorizzazione restituito nel passaggio precedente e `YOUR_REGION` con l'area corretta.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'
    
    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>Errore `HTTP 400 Bad Request`

Questo errore si verifica in genere quando il corpo della richiesta contiene dati audio non validi. È supportato solo il formato `WAV`. Controllare anche le intestazioni della richiesta per verificare che siano specificati `Content-Type` e `Content-Length` appropriati.

## <a name="error-http-408-request-timeout"></a>Errore `HTTP 408 Request Timeout`

L'errore è molto probabilmente dovuto al fatto che i dati audio non vengono inviati al servizio. Questo errore potrebbe anche essere causato da problemi di rete.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>L'elemento `RecognitionStatus` nella risposta è `InitialSilenceTimeout`

I dati audio sono in genere la causa del problema. Ad esempio: 

* È presente un lungo intervallo di silenzio all'inizio del file audio. Il servizio interrompe il riconoscimento dopo alcuni secondi e restituisce `InitialSilenceTimeout`.
* L'audio usa un formato codec non supportato e quindi i dati audio vengono trattati come silenzio.

## <a name="next-steps"></a>Passaggi successivi

* [Note sulla versione](releasenotes.md)

