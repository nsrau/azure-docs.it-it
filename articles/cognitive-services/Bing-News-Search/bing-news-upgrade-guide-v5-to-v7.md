---
title: Aggiornare l'API Ricerca notizie Bing da v5 a v7
titlesuffix: Azure Cognitive Services
description: Identifica le parti dell'applicazione da aggiornare per usare la versione 7.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 46551680937eddef898739fb57b671a0918f2338
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259292"
---
# <a name="news-search-api-upgrade-guide"></a>Guida all'aggiornamento dell'API Ricerca notizie

Questa guida all'aggiornamento indica le differenze tra la versione 5 e la versione 7 dell'API Ricerca notizie Bing. Usare questa guida per identificare le parti dell'applicazione da aggiornare per usare la versione 7.

## <a name="breaking-changes"></a>Modifiche di rilievo

### <a name="endpoints"></a>Endpoint

- Il numero di versione dell'endpoint è stato modificato da v5 a v7. Ad esempio, https://api.cognitive.microsoft.com/bing/**v7.0**/news/search.

### <a name="error-response-objects-and-error-codes"></a>Oggetti di risposta di errore e codici di errore

- Tutte le richieste non riuscite devono ora includere un oggetto `ErrorResponse` nel corpo della risposta.

- All'oggetto `Error` sono stati aggiunti i campi seguenti.  
  - `subCode`&mdash;Partiziona il codice di errore in bucket discreti, se possibile
  - `moreDetails`&mdash;Include informazioni aggiuntive sull'errore descritto nel campo `message`

- I codici di errore della versione 5 sono stati sostituiti con i possibili valori `code` e `subCode` seguenti.

|Codice|Sottocodice|DESCRIZIONE
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

### <a name="object-changes"></a>Modifiche agli oggetti

- Il campo `contractualRules` è stato aggiunto all'oggetto [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle). Il campo `contractualRules` contiene un elenco di regole che è necessario seguire (ad esempio sull'attribuzione di articoli). È necessario applicare l'attribuzione specificata in `contractualRules` invece di usare `provider`. L'articolo include `contractualRules` solo quando la risposta dell'[API Ricerca Web](../bing-web-search/search-the-web.md) contiene un riscontro relativo a notizie.

## <a name="non-breaking-changes"></a>Modifiche che non causano un'interruzione

### <a name="query-parameters"></a>Parametri di query

- Il valore Products è stato aggiunto come possibile valore su cui può essere impostato il parametro di query [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category). Vedere [Categories By Markets](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#categories-by-market) (Categorie per mercato).

- È stato aggiunto il parametro di query [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby), che restituisce gli argomenti di tendenza ordinati per data con il più recente all'inizio.

- È stato aggiunto il parametro di query [Since](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since), che restituisce gli argomenti di tendenza individuati da Bing in corrispondenza o dopo il timestamp del periodo Unix specificato.

### <a name="object-changes"></a>Modifiche agli oggetti

- Il campo `mentions` è stato aggiunto all'oggetto [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle). Il campo `mentions` contiene un elenco di entità (persone o luoghi) trovate nell'articolo.

- Il campo `video` è stato aggiunto all'oggetto [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle). Il campo `video` contiene un video correlato all'articolo di notizie. Il video è un \<iframe\> che è possibile incorporare oppure un'anteprima del video.

- Il campo `sort` è stato aggiunto all'oggetto [News](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news). Il campo `sort` mostra l'ordinamento degli articoli. Ad esempio, gli articoli sono ordinati per pertinenza (impostazione predefinita) o per data.

- È stato aggiunto l'oggetto [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue), che definisce un ordinamento. Il campo `isSelected` indica se la risposta ha usato l'ordinamento. Se **true**, la risposta ha usato l'ordinamento. Se `isSelected` è **false**, è possibile usare l'URL nel campo `url` per richiedere un ordinamento diverso.
