---
title: Codici di errore Azure Media Player
description: Riferimento al codice di errore per Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727255"
---
# <a name="error-codes"></a>Codici di errore #

Quando la riproduzione non può essere avviata o arrestata, viene generato un evento di errore e la `error()` funzione restituisce un codice e un messaggio facoltativo per aiutare gli sviluppatori di app a ottenere maggiori dettagli. `error().message`il messaggio non viene visualizzato all'utente.  Il messaggio visualizzato per l'utente si basa su `error().code` bits 27-20, vedere la tabella seguente.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Codici di errore, bit [31-28] (4 bit) ##

Descrive l'area dell'errore.

- 0 - Sconosciuto
- 1-AMP
- 2-AzureHtml5JS
- 3-Flash
- 4-silverlights
- 5-HTML5
- 6-Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Codici di errore, bit [27-0] (28 bit) ##

Descrivere i dettagli dell'errore. bits 27-20 fornisce un livello elevato, i bit 19-0 forniscono maggiori dettagli, se disponibili.


| amp. errorCode. nome | Codici, bit [27-0] (28 bit) | Descrizione |
|---|---:|---|
| **Intervallo di errori MEDIA_ERR_ABORTED (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Errore di interruzione generica |
| abortedErrNotImplemented | 0x0100001 | Errore di interruzione, non implementato |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Errore di interruzione, contenuto misto bloccato: in genere si verifica durante il caricamento `http://` di un flusso da una `https://` pagina |
| **Valore iniziale errori MEDIA_ERR_NETWORK (0x0200000-0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Errore di rete generico |
| networkErrHttpBadUrlFormat | 0x0200190 | Risposta di errore HTTP 400 |
| networkErrHttpUserAuthRequired | 0x0200191 | Risposta di errore HTTP 401 |
| networkErrHttpUserForbidden | 0x0200193 | Risposta di errore HTTP 403 |
| networkErrHttpUrlNotFound | 0x0200194 | Risposta di errore HTTP 404 |
| networkErrHttpNotAllowed | 0x0200195 | Risposta di errore HTTP 405 |
| networkErrHttpGone | 0x020019A | Risposta di errore HTTP 410 |
| networkErrHttpPreconditionFailed | 0x020019C | Risposta di errore HTTP 412 |
| networkErrHttpInternalServerFailure | 0x02001F4 | Risposta di errore HTTP 500
| networkErrHttpBadGateway | 0x02001F6 | Risposta di errore HTTP 502 |
| networkErrHttpServiceUnavailable | 0x02001F7 | Risposta di errore HTTP 503 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Risposta di errore HTTP 504 |
| networkErrTimeout | 0x0200258 | Errore di timeout di rete
| networkErrErr | 0x0200259 | Risposta di errore di connessione di rete |
| **Errori MEDIA_ERR_DECODE (0x0300000-0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Errore di decodifica generico |
| **Errori MEDIA_ERR_SRC_NOT_SUPPORTED (0x0400000-0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Errore di origine generica non supportata |
| srcErrParsePresentation | 0x0400001 | Errore di analisi della presentazione |
| srcErrParseSegment | 0x0400002 | Errore di analisi del segmento |
| srcErrUnsupportedPresentation | 0x0400003 | Presentazione non supportata |
| srcErrInvalidSegment | 0x0400004 | Segmento non valido |
| **Valore iniziale errori MEDIA_ERR_ENCRYPTED (0x0500000-0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Errore crittografato generico | 
| encryptErrDecrypterNotFound | 0x0500001 | Decrypter non trovato |
| encryptErrDecrypterInit | 0x0500002 | Errore di inizializzazione di Decrypter |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter non supportato |
| encryptErrKeyAcquire | 0x0500004 | Acquisizione chiave non riuscita |
| encryptErrDecryption | 0x0500005 | Decrittografia del segmento non riuscita |
| encryptErrLicenseAcquire | 0x0500006 | Acquisizione licenza non riuscita |
| **Valore iniziale errori SRC_PLAYER_MISMATCH (0x0600000-0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Generico nessun player tecnico corrispondente per riprodurre l'origine |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Il plug-in Flash non è installato, se è installato l'origine può essere riprodotto. *O* Flash 30 è installato e riproduce il contenuto AES.  In tal caso, provare a usare un browser diverso. Il flash 30 non è attualmente supportato a partire dal 7 giugno. Per altri dettagli, vedere [problemi noti](azure-media-player-known-issues.md) . Nota: se 0x00600003, Flash e Silverlight non sono installati, se specificati in techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Il plug-in Silverlight non è installato, se è installato, l'origine potrebbe essere riprodotto. Nota: se 0x00600003, Flash e Silverlight non sono installati, se specificati in techOrder. |
| | 0x00600003 | Flash e Silverlight non sono installati, se specificati in techOrder. |
| **Errori sconosciuti (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Errori sconosciuti |

## <a name="user-error-messages"></a>Messaggi di errore dell'utente ##

Il messaggio utente visualizzato è basato sui bit 27-20 del codice di errore.

- MEDIA_ERR_ABORTED (1)-"è stata interrotta la riproduzione video"
- MEDIA_ERR_NETWORK (2): "si è verificato un errore di rete durante il download del video."
- MEDIA_ERR_DECODE (3): "la riproduzione del video è stata interrotta a causa di un problema di danneggiamento o perché il video ha usato le funzionalità non supportate dal browser".
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"Impossibile caricare il video perché la rete o il server non è riuscito o perché il formato non è supportato".
- MEDIA_ERR_ENCRYPTED (5): "il video è crittografato e non sono disponibili le chiavi per decrittografarlo".
- SRC_PLAYER_MISMATCH (6)-"non sono state trovate origini compatibili per questo video".
- MEDIA_ERR_UNKNOWN (0xFF): "si è verificato un errore sconosciuto".

## <a name="examples"></a>Esempi ##

### <a name="0x10600001"></a>0x10600001 ##

"Non sono state trovate origini compatibili per questo video". viene visualizzato all'utente finale.

Non esiste alcun lettore tecnologico in grado di riprodurre le origini richieste, ma se è installato il plug-in Flash, è probabile che sia possibile riprodurre un'origine.  

### <a name="0x20200194"></a>0x20200194 ###

"Un errore di rete ha causato l'esito negativo del download del video". viene visualizzato all'utente finale.

AzureHtml5JS non è riuscito a eseguire la riproduzione da una risposta HTTP 404.

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

### <a name="catching-a-specific-error"></a>Rilevamento di un errore specifico ###

Il codice seguente rileva solo 404 errori:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)