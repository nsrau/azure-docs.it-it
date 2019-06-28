---
title: Tipi e struttura delle risposte dell'API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Informazioni sulle risposte e sui tipi di risposta usati dall'API Ricerca Web Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 537a03710d28be607630cf252d2f187843991048
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438714"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Tipi e struttura delle risposte dell'API Ricerca Web Bing  

Quando si invia una richiesta di ricerca a Ricerca Web Bing, viene restituito un oggetto [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) nel corpo della risposta. L'oggetto include un campo per ogni risposta considerata da Bing pertinente alla query. Questo esempio illustra un oggetto risposta nel caso in cui Bing abbia restituito tutte le risposte:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Ricerca Web Bing restituisce in genere un subset delle risposte. Se ad esempio il termine della query era *sailing dinghies*, la risposta potrebbe includere `webPages`, `images` e `rankingResponse`. A meno che non sia stata usata la query [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) per filtrare le pagine Web, la risposta include sempre le risposte `webpages` e `rankingResponse`.

## <a name="webpages-answer"></a>Risposta Webpages

La risposta [Webpages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) contiene un elenco di collegamenti a pagine Web considerate da Ricerca Web Bing pertinenti alla query. Ogni [pagina Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) nell'elenco includerà il nome della pagina, l'URL, l'URL visualizzato, una breve descrizione del contenuto e la data in cui Bing ha trovato il contenuto.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Usare `name` e `url` per creare un collegamento ipertestuale che indirizzi l'utente alla pagina Web.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Risposta con immagini

La risposta [images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contiene un elenco di immagini considerate da Bing pertinenti alla query. Ogni [immagine](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) nell'elenco include l'URL, le dimensioni, le dimensioni fisiche e il formato di codifica dell'immagine. L'oggetto immagine include anche l'URL di un'anteprima dell'immagine e le dimensioni dell'anteprima.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

A seconda del dispositivo usato dall'utente, viene in genere visualizzato un subset delle anteprime con un'opzione che consente di [spostarsi tra le pagine](paging-webpages.md) delle immagini rimanenti.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

È anche possibile espandere le anteprime quando l'utente vi passa sopra il cursore. Se si espande l'immagine, assicurarsi di assegnarle un attributo, ad esempio estraendo l'host da `hostPageDisplayUrl` e visualizzandolo sotto l'immagine. Per informazioni sul ridimensionamento dell'anteprima, vedere [Resizing and Cropping Thumbnails](./resize-and-crop-thumbnails.md) (Ridimensionamento e ritaglio di anteprime).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se si fa clic sull'anteprima, usare `webSearchUrl` per passare alla pagina dei risultati della ricerca immagini di Bing, contenente un collage delle immagini.

Per informazioni dettagliate sulla risposta images e sulle immagini, vedere l'articolo relativo all'[API Ricerca immagini](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Risposta relatedSearches

La risposta [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) contiene un elenco delle query correlate più comuni eseguite da altri utenti. Ogni [query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) nell'elenco include una stringa di query (`text`), una stringa di query con evidenziazione dei caratteri (`displayText`) e un URL (`webSearchUrl`) della pagina di risultati di ricerca di Bing per la query.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Usare la stringa di query `displayText` e l'URL `webSearchUrl` per creare un collegamento ipertestuale che indirizzi l'utente alla pagina di risultati di ricerca di Bing per la query. È anche possibile usare la stringa di query `text` nella query dell'API Ricerca Web e visualizzare i risultati.

Per informazioni su come gestire i marcatori di evidenziazione in `displayText`, vedere l'articolo relativo all'[evidenziazione](./hit-highlighting.md).

Di seguito è riportato un esempio di utilizzo delle query correlate in Bing.com.

![Esempio di ricerche correlate in Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Risposta videos

La risposta [videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contiene un elenco di video considerati da Bing pertinenti alla query. Ogni [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) nell'elenco include l'URL, la durata, le dimensioni e il formato di codifica del video. L'oggetto video include anche l'URL di un'anteprima del video e le dimensioni dell'anteprima.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

A seconda del dispositivo usato dall'utente, viene in genere visualizzato un subset dei video con un'opzione per la visualizzazione dei video rimanenti. Viene visualizzata un'anteprima del video con la lunghezza, la descrizione (nome) e l'attribuzione (autore).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Se si passa con il mouse sopra l'anteprima, è possibile usare `motionThumbnailUrl` per riprodurre una versione di anteprima del video. Assicurarsi di assegnare un attributo all'anteprima animata quando la si visualizza.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Se si fa clic sull'anteprima, saranno disponibili le opzioni di visualizzazione del video seguenti:

- Usare `hostPageUrl` per visualizzare il video sul sito Web host, ad esempio YouTube
- Usare `webSearchUrl` per visualizzare il video nel browser video di Bing
- Usare `embedHtml` per incorporare il video nell'esperienza utente

Per informazioni dettagliate sulla risposta videos e sui video, vedere l'articolo relativo all'[API Ricerca video](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Risposta news

La risposta [news](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) contiene un elenco di articoli di notizie considerati da Bing pertinenti alla query. Ogni [articolo di notizie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) nell'elenco include nome, descrizione e URL dell'articolo nel sito Web dell'host. Se l'articolo contiene un'immagine, l'oggetto include un'anteprima dell'immagine.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

A seconda del dispositivo usato dall'utente, viene in genere visualizzato un subset degli articoli di notizie con un'opzione per la visualizzazione degli articoli rimanenti. Usare `name` e `url` per creare un collegamento ipertestuale che indirizzi l'utente all'articolo di notizie nel sito dell'host. Se l'articolo include un'immagine, rendere l'immagine selezionabile tramite `url`. Assicurarsi di usare `provider` per assegnare un attributo all'articolo.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Per informazioni dettagliate sulla risposta news e sugli articoli di notizie, vedere l'articolo relativo all'[API Ricerca notizie](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Risposta Computation

Se si immette un'espressione matematica o una query di conversione unità, la risposta può contenere una risposta [Computation](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation). La risposta `computation` contiene l'espressione normalizzata e il relativo risultato.

Una query di conversione unità è una query che converte un'unità in un'altra. Ad esempio *A quanti piedi corrispondono 10 metri?* oppure *A quanti cucchiai corrisponde 1/4 di tazza?*

Di seguito è riportata la risposta `computation` alla domanda *A quanti piedi corrispondono 10 metri?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Di seguito sono riportati esempi di query matematiche e le relative risposte `computation`.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Un'espressione matematica può contenere i simboli seguenti:

|Simbolo|Descrizione|
|------------|-----------------|
|+|Addizione|
|-|Sottrazione|
|/|Divisione|
|*|Moltiplicazione|
|^|Potenza|
|!|Fattoriale|
|.|Decimal|
|()|Raggruppamento con precedenza|
|[]|Funzione|

Un'espressione matematica può contenere le costanti seguenti:

|Simbolo|Descrizione|
|------------|-----------------|
|Pi|3,14159...|
|Gradi|Gradi|
|i|Numero immaginario|
|e|e, 2,71828...|
|GoldenRatio|Rapporto aureo, 1,61803...|

Un'espressione matematica può contenere le funzioni seguenti:

|Simbolo|Descrizione|
|------------|-----------------|
|Ordina|Radice quadrata|
|Sin[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Cot[x]|Funzioni trigonometriche con argomenti in radianti|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Funzioni trigonometriche inverse con risultati in radianti|
|Exp[x], E^x|Funzione esponenziale|
|Log[x]|Logaritmo naturale|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Funzioni iperboliche|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Funzioni iperboliche inverse|

Le espressioni matematiche che contengono variabili (ad esempio, 4x + 6 = 18, dove x è la variabile) non sono supportate.

## <a name="timezone-answer"></a>Risposta TimeZone

Se si immette una query di ora o data, la risposta può contenere una risposta [TimeZone](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone). Questa risposta supporta query implicite o esplicite. Una query implicita, ad esempio *Che ora è?* , restituisce l'ora locale in base alla posizione dell'utente. Una query esplicita, ad esempio *Che ora è a Seattle?* , restituisce l'ora locale di Seattle, WA.

La risposta `timeZone` include il nome della posizione, la data e l'ora UTC corrente nella posizione specificata e la differenza dall'ora UTC. Se all'interno dei confini della posizione sono presenti più fusi orari, la risposta contiene la data e l'ora UTC corrente di tutti i fusi orari presenti. Lo Stato della Florida rientra ad esempio in due fusi orari, pertanto la risposta contiene la data e l'ora locali di entrambi i fusi orari.  

Se la query richiede il tempo di uno stato o paese/area geografica, Bing determina la città primaria all'interno di confine geografico del percorso e lo restituisce nel `primaryCityTime` campo. Se il confine contiene più fusi orari, i fusi orari rimanenti vengono restituiti nel campo `otherCityTimes`.

Di seguito sono riportate query di esempio che restituiscono la risposta `timeZone`.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>Risposta SpellSuggestion

Se Bing stabilisce che l'utente potrebbe aver voluto cercare qualcosa di diverso, la risposta include un oggetto [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions). Se si cerca ad esempio *carlos pen*, Bing può stabilire che probabilmente l'utente intendeva cercare Carlos Pena, sulla base delle ricerche di *carlos pen* eseguite in precedenza da altri utenti. Di seguito è riportato un esempio di risposta SpellSuggestions.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Intestazioni della risposta

Le risposte dell'API Ricerca Web Bing possono contenere le intestazioni seguenti:

|||
|-|-|
|`X-MSEdge-ClientID`|ID univoco assegnato da Bing all'utente|
|`BingAPIs-Market`|Mercato usato per soddisfare la richiesta|
|`BingAPIs-TraceId`|Voce di registro nel server API Bing per questa richiesta (ai fini del supporto)|

È particolarmente importante rendere permanente l'ID client e restituirlo con le richieste successive. In questo modo la ricerca userà il contesto passato per classificare i risultati della ricerca e fornirà anche un'esperienza utente coerente.

Tuttavia, quando si chiama l'API Ricerca Web Bing da JavaScript, le funzionalità di sicurezza predefinite del browser potrebbero impedire l'accesso ai valori di queste intestazioni.

Per poter accedere alle intestazioni, è possibile effettuare la richiesta dell'API Ricerca Web Bing tramite un proxy CORS. La risposta da un proxy di questo tipo ha un'intestazione `Access-Control-Expose-Headers` che inserisce le intestazioni di risposta in un elenco elementi consentiti e le rende disponibili a JavaScript.

Si può installare facilmente un proxy CORS per consentire all'[app dell'esercitazione](tutorial-bing-web-search-single-page-app.md) di accedere alle intestazioni client facoltative. Per prima cosa [installare Node.js](https://nodejs.org/en/download/), se non è già disponibile. Immettere quindi il comando seguente al prompt dei comandi.

    npm install -g cors-proxy-server

Modificare quindi l'endpoint dell'API Ricerca Web Bing nel file HTML in:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Infine avviare il proxy CORS con il comando seguente:

    cors-proxy-server

Lasciare aperta la finestra di comando mentre si usa l'app dell'esercitazione. La chiusura della finestra determina l'arresto del proxy. Nella sezione espandibile delle intestazioni HTTP sotto i risultati della ricerca viene ora visualizzata, tra le altre, l'intestazione `X-MSEdge-ClientID`. Verificare che sia la stessa per ogni richiesta.

## <a name="response-headers-in-production"></a>Intestazioni di risposta in produzione

L'approccio del proxy CORS descritto nella risposta precedente è appropriato per i contesti di sviluppo, testing e apprendimento.

In un ambiente di produzione, è necessario ospitare uno script lato server nello stesso dominio della pagina Web che usa l'API Ricerca Web Bing. Questo script dovrebbe eseguire le chiamate API su richiesta dal codice JavaScript della pagina Web e restituire al client tutti i risultati, incluse le intestazioni. Poiché le due risorse (pagina e script) condividono un'origine, CORS non viene usato e le intestazioni speciali sono accessibili al codice JavaScript nella pagina Web.

Questo approccio protegge inoltre la chiave API dall'esposizione al pubblico, in quanto è necessaria solo allo script lato server. Lo script può usare un altro metodo per verificare che la richiesta sia autorizzata.

Di seguito è riportato un esempio di suggerimento ortografico da parte di Bing.

![Esempio di suggerimento ortografico di Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Passaggi successivi  

* Rivedere la documentazione relativa alla [limitazione delle richieste](throttling-requests.md).  

## <a name="see-also"></a>Vedere anche  

* [Informazioni di riferimento per l'API Ricerca Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
