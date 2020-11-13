---
title: Aggiornare l'API Ricerca immagini Bing dalla versione 5 alla versione 7
titleSuffix: Azure Cognitive Services
description: Questa guida all'aggiornamento descrive le differenze tra la versione 5 e la versione 7 dell'API Ricerca immagini Bing. Usare questa guida per identificare le parti dell'applicazione da aggiornare per usare la versione 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 0e8a3ec38a79edb52031d6c18596038ab4c6a8af
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592142"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Guida all'aggiornamento dell'API Ricerca immagini Bing v7

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Questa guida all'aggiornamento indica le differenze tra la versione 5 e la versione 7 dell'API Ricerca immagini Bing. Usare questa guida per identificare le parti dell'applicazione da aggiornare per usare la versione 7.

## <a name="breaking-changes"></a>Modifiche di rilievo

### <a name="endpoints"></a>Endpoint

- Il numero di versione dell'endpoint è stato modificato da v5 a v7. Ad esempio, https: \/ /API.cognitive.Microsoft.com/Bing/ \* \* v 7.0 * */images/search.

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

- Il parametro di query `modulesRequested` è stato rinominato in [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Gli oggetti Annotations sono stati rinominati in Tags. Vedere il valore Tags del parametro di query [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- L'elenco dei mercati supportati del valore di filtro ShoppingSources è stato modificato ed è ora solo en-US. Vedere il parametro di query [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Modifiche alle informazioni dettagliate sulle immagini

- Il campo `annotations` di [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) è stato rinominato in `imageTags`.  

- L'oggetto `AnnotationModule` è stato rinominato in [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- L'oggetto `Annotation` è stato rinominato in [Tag](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag) e rimosso dal campo `confidence`.  

- Il campo `insightsSourcesSummary` dell'oggetto [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) è stato rinominato in `insightsMetadata`.  

- L'oggetto `InsightsSourcesSummary` è stato rinominato in [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- È stato aggiunto l'endpoint `https://api.cognitive.microsoft.com/bing/v7.0/images/details`. Usare questo endpoint anziché l'endpoint /images/search per richiedere informazioni dettagliate sulle immagini. Vedere l'articolo relativo alle [informazioni dettagliate sulle immagini](./image-insights.md).

- I parametri di query seguenti sono ora validi solo con l'endpoint `/images/details`.  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [moduli](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [auto](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [CT](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- L'oggetto `ImageInsightsResponse` è stato rinominato in [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- I tipi di dati dei campi seguenti sono stati modificati nell'oggetto [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

    -   Il tipo del campo `relatedCollections` è stato modificato da `ImageGallery[]` a [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Il tipo del campo `pagesIncluding` è stato modificato da `Image[]` a [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Il tipo del campo `relatedSearches` è stato modificato da `Query[]` a [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Il tipo del campo `recipes` è stato modificato da `Recipe[]` a [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Il tipo del campo `visuallySimilarImages` è stato modificato da `Image[]` a [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Il tipo del campo `visuallySimilarProducts` è stato modificato da `ProductSummaryImage[]` a [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   L'oggetto `ProductSummaryImage` è stato rimosso e i campi relativi al prodotto sono stati spostati nell'oggetto [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image). L'oggetto `Image` include i campi relativi al prodotto solo quando l'immagine è inclusa come parte di prodotti visivamente analoghi in una risposta con informazioni dettagliate sull'immagine.  

    -   Il tipo del campo `recognizedEntityGroups` è stato modificato da `RecognizedEntityGroup[]` a [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Il campo `categoryClassification` di [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) è stato rinominato in `annotations` e il relativo tipo è stato modificato in `AnnotationsModule`.  

### <a name="images-answer"></a>Risposta con immagini

-   I campi displayShoppingSourcesBadges e displayRecipeSourcesBadges sono stati rimossi da [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Il campo `nextOffsetAddCount` di [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) è stato rinominato in `nextOffset`. È stata modificata anche la modalità d'uso dell'offset. In precedenza il parametro di query [offset](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) veniva impostato sul valore `nextOffsetAddCount` più il valore di offset precedente e il numero di immagini nel risultato. In questa versione `offset` viene impostato sul valore `nextOffset`.  


## <a name="non-breaking-changes"></a>Modifiche che non causano un'interruzione

### <a name="query-parameters"></a>Parametri di query

- È stato aggiunto Transparent come valore di filtro possibile per [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype). Il filtro Transparent restituisce solo le immagini con uno sfondo trasparente.

- È stato aggiunto Any come valore di filtro possibile per [license](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license). Il filtro Any restituisce solo le immagini associate a una licenza.

- Sono stati aggiunti i parametri di query [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) e [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize). Usare questi filtri per restituire le immagini all'interno di un intervallo di dimensioni di file.  

- Sono stati aggiunti i parametri di query [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth) e [minWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth). Usare questi filtri per restituire le immagini all'interno di un intervallo di altezze e larghezze.  

### <a name="object-changes"></a>Modifiche agli oggetti

- I campi `description` e `lastUpdated` sono stati aggiunti all'oggetto [Offer](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer).  

- Il campo `name` è stato aggiunto all'oggetto [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery).  

- L'oggetto `similarTerms` è stato aggiunto all'oggetto [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images). Questo campo contiene un elenco di termini con significato simile alla stringa di query dell'utente.