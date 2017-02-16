---
title: Aggiungere il connettore di ricerca di Bing alle app per la logica | Microsoft Docs
description: Panoramica del connettore di ricerca di Bing con i parametri dell&quot;API REST.
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: a7f530e8-1573-4612-8899-c9c84aa2de6d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: dbb920f212d46365233e83ba1e0a0ae99917e405


---
# <a name="get-started-with-the-bing-search-connector"></a>Introduzione al connettore di ricerca di Bing
Connettersi alla funzionalità di ricerca di Bing per cercare notizie, video e così via. Con la ricerca di Bing è possibile: 

* Creare il flusso aziendale in base ai dati ottenuti dalla ricerca. 
* Usare azioni per cercare immagini, notizie e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. È ad esempio possibile cercare un video e quindi usare Twitter per pubblicarlo in un feed Twitter.

Per aggiungere un'operazione alle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
La ricerca di Bing include le azioni seguenti. Non sono disponibili trigger. 

| Trigger | Azioni |
| --- | --- |
| Nessuno |<ul><li>Cerca siti Web</li><li>Cerca video</li><li>Cerca immagini</li><li>Cerca notizie</li><li>Cerca risultati correlati</li><li>Cerca suggerimenti ortografici</li><li>Cerca tutto</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## <a name="swagger-rest-api-reference"></a>Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### <a name="search-web"></a>Cerca siti Web
Recupera siti Web da una ricerca di Bing.  
```GET: /Web```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |Nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |Nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |Nessuno |Filtro contenuti adulti. Valori validi:  <ul><li>Off</li><li>Moderata</li><li>Strict</li></ul> |
| market |string |no |query |Nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |Nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |Nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| webFileType |string |no |query |Nessuno |Tipo di file in base al quale restringere la ricerca, ad esempio 'DOC' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="search-videos"></a>Cerca video
Recupera video da una ricerca di Bing.  
```GET: /Video```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |Nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |Nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |Nessuno |Filtro contenuti adulti. Valori validi:  <ul><li>Off</li><li>Moderata</li><li>Strict</li></ul> |
| market |string |no |query |Nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |Nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |Nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| videoFilters |string |no |query |Nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione.  Valori validi:  <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Ad esempio, 'Duration:Short+Resolution:High' |
| videoSortBy |string |no |query |Nessuno |Ordinamento dei risultati. Valori validi:  <ul><li>Data</li><li>Pertinenza</li></ul> <p>L'ordinamento per data implica un ordine decrescente.</p> |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="search-images"></a>Cerca immagini
Recupera immagini da una ricerca di Bing.  
```GET: /Image```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |Nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |Nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |Nessuno |Filtro contenuti adulti. Valori validi:  <ul><li>Off</li><li>Moderata</li><li>Strict</li></ul> |
| market |string |no |query |Nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |Nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |Nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| imageFilters |string |no |query |Nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi:  <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Ad esempio, 'Size:Small+Aspect:Square' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="search-news"></a>Cerca notizie
Recupera i risultati delle notizie da una ricerca di Bing.  
```GET: /News```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |Nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |Nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |Nessuno |Filtro contenuti adulti. Valori validi:  <ul><li>Off</li><li>Moderata</li><li>Strict</li></ul> |
| market |string |no |query |Nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |Nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |Nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| newsSortBy |string |no |query |Nessuno |Ordinamento dei risultati. Valori validi:  <ul><li>Data</li><li>Pertinenza</li></ul> <p>L'ordinamento per data implica un ordine decrescente.</p> |
| newsCategory |string |no |query | |Categoria di notizie in base a cui restringere la ricerca, ad esempio 'rt_Business' |
| newsLocationOverride |string |no |query |Nessuno |Sovrascrittura per l'individuazione delle posizioni di Bing. Questo parametro è applicabile solo al mercato en-US. Il formato di input è US./<state /> , ad esempio 'US.WA' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="search-spellings"></a>Cerca suggerimenti ortografici
Recupera i suggerimenti ortografici.  
```GET: /SpellingSuggestions```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |Nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |Nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |Nessuno |Filtro contenuti adulti. Valori validi:  <ul><li>Off</li><li>Moderata</li><li>Strict</li></ul> |
| market |string |no |query |Nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |Nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |Nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="search-related"></a>Cerca risultati correlati
Recupera i risultati di ricerca correlati da una ricerca di Bing.  
```GET: /RelatedSearch```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |Nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |Nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |Nessuno |Filtro contenuti adulti. Valori validi:  <ul><li>Off</li><li>Moderata</li><li>Strict</li></ul> |
| market |string |no |query |Nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |Nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |Nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="search-all"></a>Cerca tutto
Recupera tutti i siti web, i video, le immagini e così via da una ricerca di Bing.  
```GET: /CompositeSearch```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |Nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |Nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |Nessuno |Filtro contenuti adulti. Valori validi:  <ul><li>Off</li><li>Moderata</li><li>Strict</li></ul> |
| market |string |no |query |Nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |Nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |Nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| webFileType |string |no |query |Nessuno |Tipo di file in base al quale restringere la ricerca, ad esempio 'DOC' |
| videoFilters |string |no |query |Nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione.  Valori validi:  <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Ad esempio, 'Duration:Short+Resolution:High' |
| videoSortBy |string |no |query |Nessuno |Ordinamento dei risultati. Valori validi:  <ul><li>Data</li><li>Pertinenza</li></ul> <p>L'ordinamento per data implica un ordine decrescente.</p> |
| imageFilters |string |no |query |Nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi:  <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Ad esempio, 'Size:Small+Aspect:Square' |
| newsSortBy |string |no |query |Nessuno |Ordinamento dei risultati. Valori validi:  <ul><li>Data</li><li>Pertinenza</li></ul> <p>L'ordinamento per data implica un ordine decrescente.</p> |
| newsCategory |string |no |query |nessuno |Categoria di notizie in base a cui restringere la ricerca, ad esempio 'rt_Business' |
| newsLocationOverride |string |no |query |Nessuno |Sovrascrittura per l'individuazione delle posizioni di Bing. Questo parametro è applicabile solo al mercato en-US. Il formato di input è US./<state /> , ad esempio 'US.WA' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetto
#### <a name="webresultmodel-bing-web-search-results"></a>WebResultModel: risultati della ricerca di siti Web di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |string |no |
| Descrizione |string |no |
| DisplayUrl |string |no |
| ID |string |no |
| FullUrl |string |no |

#### <a name="videoresultmodel-bing-video-search-results"></a>VideoResultModel: risultati della ricerca di video di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |string |no |
| DisplayUrl |string |no |
| ID |string |no |
| MediaUrl |string |no |
| Runtime |integer |no |
| Immagine di anteprima |non definito |no |

#### <a name="thumbnailmodel-thumbnail-properties-of-the-multimedia-element"></a>ThumbnailModel: proprietà di anteprima dell'elemento multimediale
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| MediaUrl |string |no |
| ContentType |string |no |
| Larghezza |integer |no |
| Altezza |integer |no |
| FileSize |integer |no |

#### <a name="imageresultmodel-bing-image-search-results"></a>ImageResultModel: risultati della ricerca di immagini di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |string |no |
| DisplayUrl |string |no |
| ID |string |no |
| MediaUrl |string |no |
| SourceUrl |string |no |
| Immagine di anteprima |non definito |no |

#### <a name="newsresultmodel-bing-news-search-results"></a>NewsResultModel: risultati della ricerca di notizie di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |string |no |
| Descrizione |string |no |
| DisplayUrl |string |no |
| ID |string |no |
| Source |string |no |
| Date |string |no |

#### <a name="spellresultmodel-bing-spelling-suggestions-results"></a>SpellResultModel: risultati dei suggerimenti ortografici di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| Valore |string |no |

#### <a name="relatedsearchresultmodel-bing-related-search-results"></a>RelatedSearchResultModel: risultati di ricerca correlati di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |string |no |
| ID |string |no |
| BingUrl |string |no |

#### <a name="compositesearchresultmodel-bing-composite-search-results"></a>CompositeSearchResultModel: risultati della ricerca complessa di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| WebResultsTotal |integer |no |
| ImageResultsTotal |integer |no |
| VideoResultsTotal |integer |no |
| NewsResultsTotal |integer |no |
| SpellSuggestionsTotal |integer |no |
| WebResults |array |no |
| ImageResults |array |no |
| VideoResults |array |no |
| NewsResults |array |no |
| SpellSuggestionResults |array |no |
| RelatedSearchResults |array |no |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

Tornare all' [elenco di API](apis-list.md).




<!--HONumber=Feb17_HO2-->


