---
title: Risolvere i problemi di Speech SDK - Servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Questo articolo fornisce informazioni che consentono di risolvere i problemi riscontrati quando si usa Speech SDK.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: a3741cb351b11b8cfd7c5d38713bb71232cf010e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515444"
---
# <a name="troubleshoot-the-speech-sdk"></a>Risolvere i problemi di Speech SDK

Questo articolo fornisce informazioni che consentono di risolvere i problemi riscontrati quando si usa Speech SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Errore: l'aggiornamento di WebSocket non è riuscito a causa di un errore di autenticazione (403)

È possibile che l'endpoint non sia quello corretto per l'area o il servizio. Controllare l'URI per verificare che sia corretto.

Inoltre potrebbe essere presente un problema con la chiave di sottoscrizione o il token di autorizzazione. Per altre informazioni, vedere la sezione seguente.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Errore: HTTP 403 - accesso negato o HTTP 401 - non autorizzato

Questo errore è causato spesso da problemi di autenticazione. Le richieste di connessione senza un'intestazione `Ocp-Apim-Subscription-Key` o `Authorization` valida vengono rifiutate con lo stato 403 o 401.

* Se si usa una chiave di sottoscrizione per l'autenticazione, potrebbe essere visualizzato l'errore perché:

    - La chiave di sottoscrizione manca o non è valida
    - È stata superata la quota di utilizzo della sottoscrizione

* Se si usa un token di autorizzazione per l'autenticazione, potrebbe essere visualizzato l'errore perché:

    - Il token di autorizzazione non è valido
    - Il token di autorizzazione è scaduto

### <a name="validate-your-subscription-key"></a>Convalidare la chiave di sottoscrizione

È possibile verificare di avere una chiave di sottoscrizione valida eseguendo uno dei comandi riportati di seguito.

> [!NOTE]
> Sostituire `YOUR_SUBSCRIPTION_KEY` e `YOUR_REGION` con la propria chiave di sottoscrizione e area associata.

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

Se si immette una chiave di sottoscrizione valida, il comando restituisce un token di autorizzazione, in caso contrario viene restituito un errore.

### <a name="validate-an-authorization-token"></a>Convalidare un token di autorizzazione

Se si usa un token di autorizzazione per l'autenticazione, eseguire uno dei comandi seguenti per verificare che il token di autorizzazione sia ancora valido. I token sono validi per 10 minuti.

> [!NOTE]
> Sostituire `YOUR_AUDIO_FILE` con il percorso del file audio preregistrato. Sostituire `YOUR_ACCESS_TOKEN` con il token di autorizzazione restituito al passaggio precedente. Sostituire `YOUR_REGION` con l'area corretta.

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

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Se si immette un token di autorizzazione valido, il comando restituisce la trascrizione del file audio, in caso contrario viene restituito un errore.

---

## <a name="error-http-400-bad-request"></a>Errore: HTTP 400 - Richiesta non valida

Questo errore si verifica in genere quando il corpo della richiesta contiene dati audio non validi. È supportato solo il formato WAV. Controllare anche le intestazioni della richiesta per verificare che siano specificati valori appropriati per `Content-Type` e `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Errore: HTTP 408 - Timeout richiesta

L'errore molto probabilmente è dovuto al fatto che i dati audio non vengono inviati al servizio. Questo errore potrebbe anche essere causato da problemi di rete.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" nella risposta è "InitialSilenceTimeout"

Questo problema è causato generalmente da dati audio. È possibile che venga visualizzato questo errore perché:

* È presente un lungo intervallo di silenzio all'inizio del file audio. In questo caso il servizio interrompe il riconoscimento dopo alcuni secondi e restituisce `InitialSilenceTimeout`.

* L'audio usa un formato codec non supportato e quindi i dati audio vengono trattati come silenzio.

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare le note sulla versione](releasenotes.md)
