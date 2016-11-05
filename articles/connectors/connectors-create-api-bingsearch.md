---
title: Aggiungere il connettore di ricerca di Bing alle app per la logica | Microsoft Docs
description: Panoramica del connettore di ricerca di Bing con i parametri dell'API REST.
services: ''
suite: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia

---
# Introduzione al connettore di ricerca di Bing
Connettersi alla funzionalità di ricerca di Bing per cercare notizie, video e così via. Con la ricerca di Bing è possibile:

* Creare il flusso aziendale in base ai dati ottenuti dalla ricerca.
* Usare azioni per cercare immagini, notizie e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. È ad esempio possibile cercare un video e quindi usare Twitter per pubblicarlo in un feed Twitter.

Per aggiungere un'operazione alle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
La ricerca di Bing include le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni |
| --- | --- |
| None |<ul><li>Cerca siti Web</li><li>Cerca video</li><li>Cerca immagini</li><li>Cerca notizie</li><li>Cerca risultati correlati</li><li>Cerca suggerimenti ortografici</li><li>Cerca tutto</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Cerca siti Web
Recupera siti Web da una ricerca di Bing. ```GET: /Web```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |no |query |nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| webFileType |stringa |no |query |nessuno |Tipo di file in base al quale restringere la ricerca, ad esempio 'DOC' |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Cerca video
Recupera video da una ricerca di Bing. ```GET: /Video```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |nessuno |Numero di risultati da ignorare |
| adultContent |stringa |no |query |nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |stringa |no |query |nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| videoFilters |stringa |no |query |nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Ad esempio, 'Duration:Short+Resolution:High' |
| videoSortBy |stringa |no |query |nessuno |Ordinamento dei risultati. Valori validi: <ul><li>Date</li><li>Relevance</li></ul> <p>L'ordinamento in base alla data implica l'uso di valori decrescenti.</p> |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Cerca immagini
Recupera immagini da una ricerca di Bing. ```GET: /Image```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |stringa |no |query |nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| imageFilters |stringa |no |query |nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Ad esempio, 'Size:Small+Aspect:Square' |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Cerca notizie
Recupera i risultati delle notizie da una ricerca di Bing. ```GET: /News```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |nessuno |Numero di risultati da ignorare |
| adultContent |stringa |no |query |nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |stringa |no |query |nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| newsSortBy |stringa |no |query |nessuno |Ordinamento dei risultati. Valori validi: <ul><li>Date</li><li>Relevance</li></ul> <p>L'ordinamento in base alla data implica l'uso di valori decrescenti.</p> |
| newsCategory |stringa |no |query | |Categoria di notizie in base a cui restringere la ricerca, ad esempio 'rt\_Business' |
| newsLocationOverride |string |no |query |nessuno |Sovrascrittura per l'individuazione delle posizioni di Bing. Questo parametro è applicabile solo al mercato it-IT. Il formato di input è US./<stato />, ad esempio "US.WA". |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Cerca suggerimenti ortografici
Recupera i suggerimenti ortografici. ```GET: /SpellingSuggestions```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |nessuno |Numero di risultati da ignorare |
| adultContent |stringa |no |query |nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |stringa |no |query |nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Cerca risultati correlati
Recupera i risultati di ricerca correlati da una ricerca di Bing. ```GET: /RelatedSearch```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |nessuno |Numero di risultati da ignorare |
| adultContent |stringa |no |query |nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |stringa |no |query |nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Cerca tutto
Recupera tutti i siti Web, i video, le immagini e così via da una ricerca di Bing. ```GET: /CompositeSearch```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da cercare, ad esempio 'xbox' |
| maxResult |integer |no |query |nessuno |Numero massimo di risultati da restituire |
| startOffset |integer |no |query |nessuno |Numero di risultati da ignorare |
| adultContent |string |no |query |nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |no |query |nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT |
| longitudine |number |no |query |nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450 |
| latitudine |number |no |query |nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696 |
| webFileType |stringa |no |query |nessuno |Tipo di file in base al quale restringere la ricerca, ad esempio 'DOC' |
| videoFilters |string |no |query |nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Ad esempio, 'Duration:Short+Resolution:High' |
| videoSortBy |stringa |no |query |nessuno |Ordinamento dei risultati. Valori validi: <ul><li>Date</li><li>Relevance</li></ul> <p>L'ordinamento in base alla data implica l'uso di valori decrescenti.</p> |
| imageFilters |string |no |query |nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Ad esempio, 'Size:Small+Aspect:Square' |
| newsSortBy |stringa |no |query |nessuno |Ordinamento dei risultati. Valori validi: <ul><li>Date</li><li>Relevance</li></ul> <p>L'ordinamento in base alla data implica l'uso di valori decrescenti.</p> |
| newsCategory |string |no |query |nessuno |Categoria di notizie in base a cui restringere la ricerca, ad esempio 'rt\_Business' |
| newsLocationOverride |string |no |query |nessuno |Sovrascrittura per l'individuazione delle posizioni di Bing. Questo parametro è applicabile solo al mercato it-IT. Il formato di input è US./<stato />, ad esempio "US.WA". |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## Definizioni oggetti
#### WebResultModel: risultati della ricerca di siti Web di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |stringa |no |
| Descrizione |stringa |no |
| DisplayUrl |stringa |no |
| ID |stringa |no |
| FullUrl |string |no |

#### VideoResultModel: risultati della ricerca di video di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |string |no |
| DisplayUrl |string |no |
| ID |stringa |no |
| MediaUrl |stringa |no |
| Runtime |integer |no |
| Immagine di anteprima |non definito |no |

#### ThumbnailModel: proprietà di anteprima dell'elemento multimediale
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| MediaUrl |string |no |
| ContentType |string |no |
| Larghezza |integer |no |
| Altezza: |integer |no |
| FileSize |integer |no |

#### ImageResultModel: risultati della ricerca di immagini di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |string |no |
| DisplayUrl |stringa |no |
| ID |string |no |
| MediaUrl |stringa |no |
| SourceUrl |stringa |no |
| Immagine di anteprima |non definito |no |

#### NewsResultModel: risultati della ricerca di notizie di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |stringa |no |
| Descrizione |stringa |no |
| DisplayUrl |string |no |
| ID |stringa |no |
| Sorgente |string |no |
| Date |stringa |no |

#### SpellResultModel: risultati dei suggerimenti ortografici di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ID |string |no |
| Valore |string |no |

#### RelatedSearchResultModel: risultati di ricerca correlati di Bing
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Titolo |stringa |no |
| ID |string |no |
| BingUrl |stringa |no |

#### CompositeSearchResultModel: risultati della ricerca complessa di Bing
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

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).

<!---HONumber=AcomDC_0824_2016-->