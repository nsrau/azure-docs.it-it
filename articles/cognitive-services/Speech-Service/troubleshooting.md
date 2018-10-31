---
title: Risolvere i problemi di Speech Service SDK
titleSuffix: Azure Cognitive Services
description: Risolvere i problemi di Speech Service SDK.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 9f0cea263262d83d9a95012f6cd09fa9acdc0141
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49464572"
---
# <a name="troubleshoot-the-speech-service-sdk"></a>Risolvere i problemi di Speech Service SDK

Le informazioni di questo articolo consentono di risolvere i problemi riscontrati quando si usa Speech Service SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Errore: l'aggiornamento di WebSocket non è riuscito a causa di un errore di autenticazione (403)

È possibile che l'endpoint non sia quello corretto per l'area o il servizio. Controllare l'URI per verificare che sia corretto. 

Inoltre potrebbe essere presente un problema con la chiave di sottoscrizione o il token di autorizzazione. Per altre informazioni, vedere la sezione seguente.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Errore: HTTP 403 operazione non consentita o HTTP 401 utente non autorizzato

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

---

## <a name="error-http-400-bad-request"></a>Errore: HTTP 400 richiesta non valida

Questo errore si verifica in genere quando il corpo della richiesta contiene dati audio non validi. È supportato solo il formato WAV. Controllare anche le intestazioni della richiesta per verificare che siano specificati valori appropriati per `Content-Type` e `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Errore: HTTP 408 timeout richiesta

L'errore molto probabilmente è dovuto al fatto che i dati audio non vengono inviati al servizio. Questo errore potrebbe anche essere causato da problemi di rete.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" nella risposta è "InitialSilenceTimeout"

Questo problema è causato generalmente da dati audio. È possibile che venga visualizzato questo errore perché:

* È presente un lungo intervallo di silenzio all'inizio del file audio. In questo caso il servizio interrompe il riconoscimento dopo alcuni secondi e restituisce `InitialSilenceTimeout`.

* L'audio usa un formato codec non supportato e quindi i dati audio vengono trattati come silenzio.

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare le note sulla versione](releasenotes.md)

