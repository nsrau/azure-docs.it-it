<properties
	pageTitle="Aggiungere l'API di ricerca di Bing a PowerApps o app per la logica | Microsoft Azure"
	description="Panoramica dell'API di ricerca di Bing con i parametri dell'API REST."
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/11/2016"
   ms.author="mandia"/>

# Introduzione all'API di ricerca di Bing 
Connettersi alla funzionalità di ricerca di Bing per cercare notizie, video e così via.

L'API di ricerca di Bing può essere usata da PowerApps e dalle app per la logica.

Con la ricerca di Bing è possibile:

- Creare il flusso aziendale in base ai dati ottenuti dalla ricerca. 
- Usare azioni per cercare immagini, notizie e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. È ad esempio possibile cercare un video e quindi usare Twitter per pubblicarlo in un feed Twitter.
- Aggiungere l'API di ricerca di Bing a PowerApps Enterprise, in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
La ricerca di Bing include le azioni seguenti. Non sono disponibili trigger.

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Cerca siti Web</li><li>Cerca video</li><li>Cerca immagini</li><li>Cerca notizie</li><li>Cerca risultati correlati</li><li>Cerca suggerimenti ortografici</li><li>Cerca tutto</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Configurazione aggiuntiva
Quando si aggiunge ricerca di Bing a PowerApps Enterprise, viene richiesta una chiave dell'account. Se non si dispone di una chiave di ricerca di Bing, creare un'[offerta di ricerca di Bing](https://datamarket.azure.com/dataset/bing/search) per ottenerne una.


## Riferimento all'API REST Swagger
Si applica alla versione 1.0.

### Cerca siti Web 
Recupera siti Web da una ricerca di Bing. ```GET: /Web```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|sì|query|nessuno |Testo da cercare, ad esempio 'xbox'|
|maxResult|integer|no|query|nessuno |Numero massimo di risultati da restituire|
|startOffset|integer|no|query| nessuno|Numero di risultati da ignorare|
|adultContent|string|no|query|nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT|
|longitudine|number|no|query| nessuno|Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450|
|latitudine|number|no|query| nessuno|Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696|
|webFileType|string|no|query|nessuno |Tipo di file in base al quale restringere la ricerca, ad esempio 'DOC'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Cerca video 
Recupera video da una ricerca di Bing. ```GET: /Video```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|sì|query|nessuno |Testo da cercare, ad esempio 'xbox'|
|maxResult|integer|no|query| nessuno|Numero massimo di risultati da restituire|
|startOffset|integer|no|query|nessuno |Numero di risultati da ignorare|
|adultContent|string|no|query|nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT|
|longitudine|number|no|query|nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450|
|latitudine|number|no|query|nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696|
|videoFilters|string|no|query|nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <p>Ad esempio, 'Duration:Short+Resolution:High'</p>|
|videoSortBy|string|no|query|nessuno |Ordinamento dei risultati. Valori validi: <ul><li>Date</li><li>Relevance</li></ul> <p>L'ordinamento in base alla data implica l'uso di valori decrescenti.</p>|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Cerca immagini    
Recupera immagini da una ricerca di Bing. ```GET: /Image```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|sì|query|nessuno |Testo da cercare, ad esempio 'xbox'|
|maxResult|integer|no|query|nessuno |Numero massimo di risultati da restituire|
|startOffset|integer|no|query|nessuno |Numero di risultati da ignorare|
|adultContent|string|no|query|nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT|
|longitudine|number|no|query| nessuno|Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450|
|latitudine|number|no|query|nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696|
|imageFilters|string|no|query|nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><p>Ad esempio, 'Size:Small+Aspect:Square'</p>|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Cerca notizie    
Recupera i risultati delle notizie da una ricerca di Bing. ```GET: /News```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|sì|query|nessuno |Testo da cercare, ad esempio 'xbox'|
|maxResult|integer|no|query|nessuno |Numero massimo di risultati da restituire|
|startOffset|integer|no|query| nessuno|Numero di risultati da ignorare|
|adultContent|string|no|query|nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT|
|longitudine|number|no|query|nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450|
|latitudine|number|no|query|nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696|
|newsSortBy|string|no|query| nessuno|Ordinamento dei risultati. Valori validi: <ul><li>Date</li><li>Relevance</li></ul> <p>L'ordinamento in base alla data implica l'uso di valori decrescenti.</p>|
|newsCategory|string|no|query| |Categoria di notizie in base a cui restringere la ricerca, ad esempio 'rt\_Business'|
|newsLocationOverride|string|no|query|nessuno |Sovrascrittura per l'individuazione delle posizioni di Bing. Questo parametro è applicabile solo al mercato it-IT. Il formato di input è US./<state />, ad esempio 'US.WA'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Cerca suggerimenti ortografici    
Recupera i suggerimenti ortografici. ```GET: /SpellingSuggestions```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|sì|query| nessuno|Testo da cercare, ad esempio 'xbox'|
|maxResult|integer|no|query|nessuno |Numero massimo di risultati da restituire|
|startOffset|integer|no|query| nessuno|Numero di risultati da ignorare|
|adultContent|string|no|query|nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query| nessuno|Mercato o area in base a cui restringere la ricerca, ad esempio it-IT|
|longitudine|number|no|query|nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450|
|latitudine|number|no|query|nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Cerca risultati correlati    
Recupera i risultati di ricerca correlati da una ricerca di Bing. ```GET: /RelatedSearch```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|sì|query|nessuno |Testo da cercare, ad esempio 'xbox'|
|maxResult|integer|no|query|nessuno |Numero massimo di risultati da restituire|
|startOffset|integer|no|query| nessuno|Numero di risultati da ignorare|
|adultContent|string|no|query|nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT|
|longitudine|number|no|query|nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450|
|latitudine|number|no|query| nessuno|Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Cerca tutto    
Recupera tutti i siti web, i video, le immagini e così via da una ricerca di Bing. ```GET: /CompositeSearch```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|sì|query|nessuno |Testo da cercare, ad esempio 'xbox'|
|maxResult|integer|no|query|nessuno |Numero massimo di risultati da restituire|
|startOffset|integer|no|query|nessuno |Numero di risultati da ignorare|
|adultContent|string|no|query|nessuno |Filtro contenuti adulti. Valori validi: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|nessuno |Mercato o area in base a cui restringere la ricerca, ad esempio it-IT|
|longitudine|number|no|query|nessuno |Longitudine (coordinate est/ovest) in base a cui restringere la ricerca, ad esempio 47.603450|
|latitudine|number|no|query|nessuno |Latitudine (coordinate nord/sud) in base a cui restringere la ricerca, ad esempio -122.329696|
|webFileType|string|no|query|nessuno |Tipo di file in base al quale restringere la ricerca, ad esempio 'DOC'|
|videoFilters|string|no|query|nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <p>Ad esempio, 'Duration:Short+Resolution:High'</p>|
|videoSortBy|string|no|query|nessuno |Ordinamento dei risultati. Valori validi: <ul><li>Date</li><li>Relevance</li></ul> <p>L'ordinamento in base alla data implica l'uso di valori decrescenti.</p>|
|imageFilters|string|no|query|nessuno |Filtro di ricerca in base a parametri quali dimensioni, aspetto, colore, stile, volto o qualsiasi altra combinazione. Valori validi: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><p>Ad esempio, 'Size:Small+Aspect:Square'</p>|
|newsSortBy|string|no|query|nessuno |Ordinamento dei risultati. Valori validi: <ul><li>Date</li><li>Relevance</li></ul> <p>L'ordinamento in base alla data implica l'uso di valori decrescenti.</p>|
|newsCategory|string|no|query|nessuno |Categoria di notizie in base a cui restringere la ricerca, ad esempio 'rt\_Business'|
|newsLocationOverride|string|no|query|nessuno |Sovrascrittura per l'individuazione delle posizioni di Bing. Questo parametro è applicabile solo al mercato it-IT. Il formato di input è US./<state />, ad esempio 'US.WA'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## Definizioni oggetto

#### WebResultModel: risultati della ricerca di siti Web di Bing

|Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Titolo|string|no|
|Descrizione|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|FullUrl|string|no|

#### VideoResultModel: risultati della ricerca di video di Bing

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|Titolo|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|MediaUrl|string|no|
|Runtime|integer|no|
|Immagine di anteprima|non definito|no|

#### ThumbnailModel: proprietà di anteprima dell'elemento multimediale

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|MediaUrl|string|no|
|ContentType|string|no|
|Larghezza|integer|no|
|Altezza|integer|no|
|FileSize|integer|no|

#### ImageResultModel: risultati della ricerca di immagini di Bing

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|Titolo|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|MediaUrl|string|no|
|SourceUrl|string|no|
|Immagine di anteprima|non definito|no|

#### NewsResultModel: risultati della ricerca di notizie di Bing

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|Titolo|string|no|
|Descrizione|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|Source|string|no|
|Date|string|no|

#### SpellResultModel: risultati dei suggerimenti ortografici di Bing

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|ID|string|no|
|Valore|string|no|

#### RelatedSearchResultModel: risultati di ricerca correlati di Bing

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|Titolo|string|no|
|ID|string|no|
|BingUrl|string|no|

#### CompositeSearchResultModel: risultati della ricerca complessa di Bing

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|WebResultsTotal|integer|no|
|ImageResultsTotal|integer|no|
|VideoResultsTotal|integer|no|
|NewsResultsTotal|integer|
|SpellSuggestionsTotal|integer|no|
|WebResults|array|no|
|ImageResults|array|no|
|VideoResults|array|no|
|NewsResults|array|no|
|SpellSuggestionResults|array|no|
|RelatedSearchResults|array|no|

## Passaggi successivi

Dopo aver aggiunto l'API di ricerca di Bing a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle proprie app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->