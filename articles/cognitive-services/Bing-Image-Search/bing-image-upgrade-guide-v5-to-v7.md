---
title: Aggiornare l'API Ricerca immagini Bing dalla versione 5 alla versione 7
titleSuffix: Azure Cognitive Services
description: Questa guida all'aggiornamento descrive le differenze tra la versione 5 e la versione 7 dell'API Ricerca immagini Bing. Usare questa guida per identificare le parti dell'applicazione da aggiornare per usare la versione 7.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: c62ed151e1b09482c227f59ee5264de01d425255
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477811"
---
# <a name="bing-image-search-api-upgrade-guide"></a>Guida all'aggiornamento dell'API Ricerca immagini Bing

Questa guida all'aggiornamento indica le differenze tra la versione 5 e la versione 7 dell'API Ricerca immagini Bing. Usare questa guida per identificare le parti dell'applicazione da aggiornare per usare la versione 7.

## <a name="breaking-changes"></a>Modifiche di rilievo

### <a name="endpoints"></a>Endpoint

- Il numero di versione dell'endpoint è stato modificato da v5 a v7. Ad esempio, https://api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

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



### <a name="query-parameters"></a>Parametri di query

- Il parametro di query `modulesRequested` è stato rinominato in [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).  

- Gli oggetti Annotations sono stati rinominati in Tags. Vedere il valore Tags del parametro di query [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).  

- L'elenco dei mercati supportati del valore di filtro ShoppingSources è stato modificato ed è ora solo en-US. Vedere il parametro di query [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Modifiche alle informazioni dettagliate sulle immagini

- Il campo `annotations` di [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) è stato rinominato in `imageTags`.  

- L'oggetto `AnnotationModule` è stato rinominato in [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  

- L'oggetto `Annotation` è stato rinominato in [Tag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag) e rimosso dal campo `confidence`.  

- Il campo `insightsSourcesSummary` dell'oggetto [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) è stato rinominato in `insightsMetadata`.  

- L'oggetto `InsightsSourcesSummary` è stato rinominato in [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  

- È stato aggiunto l'endpoint `https://api.cognitive.microsoft.com/bing/v7.0/images/details`. Usare questo endpoint anziché l'endpoint /images/search per richiedere informazioni dettagliate sulle immagini. Vedere l'articolo relativo alle [informazioni dettagliate sulle immagini](./image-insights.md).

- I parametri di query seguenti sono ora validi solo con l'endpoint `/images/details`.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  

- L'oggetto `ImageInsightsResponse` è stato rinominato in [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  

- I tipi di dati dei campi seguenti sono stati modificati nell'oggetto [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  

    -   Il tipo del campo `relatedCollections` è stato modificato da `ImageGallery[]` a [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Il tipo del campo `pagesIncluding` è stato modificato da `Image[]` a [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Il tipo del campo `relatedSearches` è stato modificato da `Query[]` a [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Il tipo del campo `recipes` è stato modificato da `Recipe[]` a [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  

    -   Il tipo del campo `visuallySimilarImages` è stato modificato da `Image[]` a [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Il tipo del campo `visuallySimilarProducts` è stato modificato da `ProductSummaryImage[]` a [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   L'oggetto `ProductSummaryImage` è stato rimosso e i campi relativi al prodotto sono stati spostati nell'oggetto [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image). L'oggetto `Image` include i campi relativi al prodotto solo quando l'immagine è inclusa come parte di prodotti visivamente analoghi in una risposta con informazioni dettagliate sull'immagine.  

    -   Il tipo del campo `recognizedEntityGroups` è stato modificato da `RecognizedEntityGroup[]` a [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Il campo `categoryClassification` di [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) è stato rinominato in `annotations` e il relativo tipo è stato modificato in `AnnotationsModule`.  

### <a name="images-answer"></a>Risposta con immagini

-   I campi displayShoppingSourcesBadges e displayRecipeSourcesBadges sono stati rimossi da [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  

-   Il campo `nextOffsetAddCount` di [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) è stato rinominato in `nextOffset`. È stata modificata anche la modalità d'uso dell'offset. In precedenza il parametro di query [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) veniva impostato sul valore `nextOffsetAddCount` più il valore di offset precedente e il numero di immagini nel risultato. In questa versione `offset` viene impostato sul valore `nextOffset`.  


## <a name="non-breaking-changes"></a>Modifiche che non causano un'interruzione

### <a name="query-parameters"></a>Parametri di query

- È stato aggiunto Transparent come valore di filtro possibile per [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype). Il filtro Transparent restituisce solo le immagini con uno sfondo trasparente.

- È stato aggiunto Any come valore di filtro possibile per [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license). Il filtro Any restituisce solo le immagini associate a una licenza.

- Sono stati aggiunti i parametri di query [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) e [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize). Usare questi filtri per restituire le immagini all'interno di un intervallo di dimensioni di file.  

- Sono stati aggiunti i parametri di query [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth) e [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth). Usare questi filtri per restituire le immagini all'interno di un intervallo di altezze e larghezze.  

### <a name="object-changes"></a>Modifiche agli oggetti

- I campi `description` e `lastUpdated` sono stati aggiunti all'oggetto [Offer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer).  

- Il campo `name` è stato aggiunto all'oggetto [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery).  

- L'oggetto `similarTerms` è stato aggiunto all'oggetto [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images). Questo campo contiene un elenco di termini con significato simile alla stringa di query dell'utente.  
