---
title: Codici di errore di Azure Media PlayerAzure Media Player error codes
description: Informazioni di riferimento sul codice di errore per Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727255"
---
# <a name="error-codes"></a>Codici di errore #

Quando la riproduzione non può avviarsi o è `error()` stata interrotta, verrà generato un evento di errore e la funzione restituirà un codice e un messaggio facoltativo per aiutare lo sviluppatore dell'app a ottenere ulteriori dettagli. `error().message`non è il messaggio visualizzato all'utente.  Il messaggio visualizzato all'utente `error().code` si basa sui bit 27-20, vedere la tabella seguente.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Codici di errore, bit [31-28] (4 bit) ##

Descrivere l'area dell'errore.

- 0 - Sconosciuto
- 1 - AMPLIFICATORE
- 2 - AzureHtml5JS
- 3 - FlashSS
- 4 - SilverlightSS
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Codici di errore, bit [27-0] (28 bit) ##

Descrivere i dettagli dell'errore, i bit 27-20 forniscono un livello elevato, i bit 19-0 forniscono maggiori dettagli, se disponibili.


| amp.errorCode. [nome] | Codici, Bit [27-0] (28 bit) | Descrizione |
|---|---:|---|
| **MEDIA_ERR_ABORTED'intervallo di errori (0x0100000 - 0x01FFFFF)** | | |
| abortedErrSconosciuto | 0x0100000 | Errore generico di interruzione |
| abortedErrNotImplemented | 0x0100001 | Errore di interruzione, non implementato |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Errore di interruzione, contenuto misto `http://` bloccato `https://` - in genere si verifica durante il caricamento di un flusso da una pagina |
| **MEDIA_ERR_NETWORK valore iniziale degli errori (0x0200000 - 0x02FFFFF)** | | |
| networkErrSconosciuto | 0x0200000 | Errore di rete generico |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400 risposta di errore |
| networkErrHttpUserAuthRequired | 0x0200191 | Risposta di errore Http 401Http 401 error response |
| networkErrHttpUserPer accesso negato | 0x0200193 | Risposta di errore Http 403Http 403 error response |
| networkErrHttpUrlNotFound | 0x0200194 | Risposta di errore Http 404Http 404 error response |
| networkErrHttpNotAllowed | 0x0200195 | Risposta di errore Http 405Http 405 error response |
| networkErrHttpGone | 0x020019A | Risposta di errore http 410Http 410 error response |
| networkErrHttpPreconditionFailed | 0x020019C | Risposta di errore http 412Http 412 error response |
| networkErrHttpInternalServerFailure | 0x02001F4 | Risposta di errore Http 500Http 500 error response
| networkErrHttpBadGateway | 0x02001F6 | Risposta di errore Http 502Http 502 error response |
| networkErrHttpServiceUndisponibile | 0x02001F7 | Risposta di errore http 503Http 503 error response |
| networkErrHttpGatewayTimeout (informazioni in lingua inlinguastiina inlinguasta dei | 0x02001F8 | Risposta di errore Http 504Http 504 error response |
| networkErrTimeout (timeout di rete) | 0x0200258 | Errore di timeout di rete
| networkErrErr | 0x0200259 | Risposta di errore di connessione di rete |
| **errori MEDIA_ERR_DECODE (0x0300000 - 0x03FFFFF)** | | |
| decodeErrSconosciuto | 0x0300000 | Errore di decodifica generico |
| **MEDIA_ERR_SRC_NOT_SUPPORTED errori (0x0400000 - 0x04FFFFF)** | | |
| srcErrSconosciuto | 0x0400000 | Errore di origine generica non supportataGeneric source not supported error |
| SrcErrParsePresentation (informazioni in lingua srcErParsePresentation) | 0x0400001 | Errore di analisi della presentazione |
| srcErrParseSegment | 0x0400002 | Errore di analisi del segmento |
| srcErrUnsupportedPresentation (presentazione di srcErrUnsupportedPresentation) | 0x0400003 | Presentazione non supportata |
| srcErrInvalidSegment | 0x0400004 | Segmento non valido |
| **MEDIA_ERR_ENCRYPTED errori valore iniziale(0x0500000 - 0x05FFFFF)** | | |
| encryptErrSconosciuto | 0x0500000 | Errore crittografato generico | 
| crittografoNotFound | 0x0500001 | Decrittografia non trovata |
| crittodecrypterInit | 0x0500002 | Errore di inizializzazione di decrittografiaDecrypter initialization error |
| crittoDecrypterNon Supportato | 0x0500003 | Decrittografia non supportata |
| cripta-chiaviAcquire | 0x0500004 | Acquisizione chiave non riuscita |
| crittodezione crittom | 0x0500005 | Decrittografia del segmento non riuscita |
| cripLicenseAcquire | 0x0500006 | Acquisizione licenza non riuscita |
| **SRC_PLAYER_MISMATCH errore (0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchSconosciuto | 0x0600000 | Generico nessun lettore tecnologico corrispondente per riprodurre la fonte |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Flash plugin non è installato, se installato la sorgente può giocare. *O OPPURE* Flash 30 è installato e riproduce il contenuto AES.  Se questo è il caso, si prega di provare un altro browser. Flash 30 non è supportato oggi a partire dal 7 giugno. Per ulteriori dettagli, vedere [Problemi noti.](azure-media-player-known-issues.md) Nota: se 0x00600003 non sono installati sia Flash che Silverlight, se specificato nell'ordine tech.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Il plug-in Silverlight non è installato, se installato l'origine potrebbe essere riprodotta. Nota: se 0x00600003 non sono installati sia Flash che Silverlight, se specificato nell'ordine tech. |
| | 0x00600003 | Sia Flash che Silverlight non sono installati, se specificato in techOrder. |
| **Errori sconosciuti (0x0FF00000)** | | |
| errSconosciuto | 0xFF00000 | Errori sconosciuti |

## <a name="user-error-messages"></a>Messaggi di errore dell'utente ##

Messaggio utente visualizzato si basa sui bit 27-20 del codice di errore.

- MEDIA_ERR_ABORTED (1) -"Hai interrotto la riproduzione video"
- MEDIA_ERR_NETWORK (2) - "Un errore di rete ha causato l'esito negativo del download del video a metà strada."
- MEDIA_ERR_DECODE (3) - "La riproduzione video è stata interrotta a causa di un problema di corruzione o perché il video utilizzato non supporta il browser."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"Impossibile caricare il video, perché il server o la rete non è riuscita o perché il formato non è supportato."
- MEDIA_ERR_ENCRYPTED (5) - "Il video è criptato e non abbiamo le chiavi per decifrarlo."
- SRC_PLAYER_MISMATCH (6) - "Nessuna fonte compatibile è stata trovata per questo video."
- MEDIA_ERR_UNKNOWN (0xFF) - "Si è verificato un errore sconosciuto".

## <a name="examples"></a>Esempi ##

### <a name="0x10600001"></a>0x10600001 ##

"Non è stata trovata alcuna fonte compatibile per questo video." viene visualizzato all'utente finale.

Non c'è nessun lettore tecnologico in grado di riprodurre le sorgenti richieste, ma se Flash plugin è installato, è probabile che una fonte potrebbe essere riprodotta.  

### <a name="0x20200194"></a>0x20200194 ###

"Un errore di rete ha causato il mancato mancato scaricamento del video a metà strada." viene visualizzato all'utente finale.

AzureHtml5JS non è riuscito a riprodurre da una risposta http 404.AzureHtml5JS failed to playback from an http 404 response.

### <a name="categorizing-errors"></a>Categorizzazione degli errori ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Intercettazione di un errore specifico ###

Il codice seguente rileva solo gli errori 404:The following code catches just 404 errors:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Passaggi successivi ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)