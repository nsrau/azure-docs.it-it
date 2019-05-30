---
title: Aggiornamento di API Ricerca video Bing da v5 a v7
titlesuffix: Azure Cognitive Services
description: Identifica le parti dell'applicazione da aggiornare per usare la versione 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 32dc928147af8fbb3c84bdb76e50cee4fdabe17d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386643"
---
# <a name="video-search-api-upgrade-guide"></a>Guida all'aggiornamento dell'API Ricerca video

Questa guida all'aggiornamento indica le differenze tra la versione 5 e la versione 7 dell'API Ricerca video Bing. Usare questa guida per identificare le parti dell'applicazione da aggiornare per usare la versione 7.

## <a name="breaking-changes"></a>Modifiche di rilievo

### <a name="endpoints"></a>Endpoint

- Il numero di versione dell'endpoint è stato modificato da v5 a v7. Ad esempio: `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

### <a name="error-response-objects-and-error-codes"></a>Oggetti di risposta di errore e codici di errore

- Tutte le richieste non riuscite devono ora includere un oggetto `ErrorResponse` nel corpo della risposta.

- All'oggetto `Error` sono stati aggiunti i campi seguenti.  
  - `subCode`&mdash;Partiziona il codice di errore in bucket discreti, se possibile
  - `moreDetails`&mdash;Include informazioni aggiuntive sull'errore descritto nel campo `message`
   

- I codici di errore della versione 5 sono stati sostituiti con i possibili valori `code` e `subCode` seguenti.

|Codice|Sottocodice|Descrizione
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing restituisce ServerError ogni volta che si verifica una delle condizioni del sottocodice. La risposta include questi errori se il codice di stato HTTP è 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloccato|Bing restituisce InvalidRequest ogni volta che una parte della richiesta non è valida, ad esempio quando non è specificato un parametro obbligatorio o un valore di parametro non è valido.<br/><br/>Se l'errore è ParameterMissing o ParameterInvalidValue, il codice di stato HTTP è 400.<br/><br/>Se l'errore è HttpNotAllowed, il codice di stato HTTP è 410.
|RateLimitExceeded||Bing restituisce RateLimitExceeded ogni volta che si supera la quota di query al secondo (QPS) o di query al mese (QPM).<br/><br/>Bing restituisce il codice di stato HTTP 429 se è stata superata la quota di query al secondo e 403 se è stata superata la quota di query al mese.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing restituisce InvalidAuthorization quando non riesce ad autenticare il chiamante, ad esempio quando l'intestazione `Ocp-Apim-Subscription-Key` non è specificata o la chiave di sottoscrizione non è valida.<br/><br/>La ridondanza si verifica se si specificano più metodi di autenticazione.<br/><br/>Se l'errore è InvalidAuthorization, il codice di stato HTTP è 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing restituisce InsufficientAuthorization quando il chiamante non ha le autorizzazioni per accedere alla risorsa. Ciò può verificarsi se la chiave di sottoscrizione è stata disabilitata o è scaduta. <br/><br/>Se l'errore è InsufficientAuthorization, il codice di stato HTTP è 403.

- L'elenco seguente associa i codici di errore precedenti ai nuovi codici. Se è stata definita una dipendenza dai codici di errore della versione 5, aggiornare il codice in modo appropriato.

|Codice della versione 5|Codice e sottocodice della versione 7
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloccato|InvalidRequest.Blocked

### <a name="query-parameters"></a>Parametri di query

- Il parametro di query `modulesRequested` è stato rinominato in [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Modifiche agli oggetti

- Il campo `nextOffsetAddCount` di [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) è stato rinominato in `nextOffset`. È stata modificata anche la modalità d'uso dell'offset. In precedenza il parametro di query [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) veniva impostato sul valore `nextOffset` più il valore di offset precedente e il numero di video nel risultato. Ora è sufficiente impostare il parametro di query `offset` sul valore `nextOffset`.  
  
- Il tipo di dati del campo `relatedVideos` è stato cambiato da `Video[]` a [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) (vedere [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)).

