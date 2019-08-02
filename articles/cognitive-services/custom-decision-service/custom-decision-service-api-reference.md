---
title: Riferimenti per le API - Servizio decisionale personalizzato
titlesuffix: Azure Cognitive Services
description: Guida completa alle API del Servizio decisionale personalizzato.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 4f263e3b57103174f0084ab3d25430d8c47359fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707307"
---
# <a name="api"></a>API

Il Servizio decisionale personalizzato include due API che vengono chiamate a ogni decisione: l'[API classificazione](#ranking-api) per classificare le azioni e l'[API premio](#reward-api) per generare il premio. Si usa inoltre un'[API del set di azioni](#action-set-api-customer-provided) per specificare le azioni al Servizio decisionale personalizzato di Azure. Questo articolo descrive queste tre API. Di seguito viene usato uno scenario tipico per mostrare quando il Servizio decisionale personalizzato ottimizza la classificazione degli articoli.

## <a name="ranking-api"></a>API classificazione

L'API classificazione usa un modello di comunicazione standard in stile [JSONP](https://en.wikipedia.org/wiki/JSONP) per ottimizzare la latenza e ignorare i [criteri di corrispondenza dell'origine](https://en.wikipedia.org/wiki/Same-origin_policy). Questi ultimi impediscono a JavaScript di recuperare i dati dall'esterno dell'origine della pagina.

Inserire questo frammento di codice nell'intestazione HTML della pagina riepilogativa (in cui viene visualizzato un elenco personalizzato degli articoli):

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> La funzione di callback deve essere definita prima della chiamata all'API classificazione.

> [!TIP]
> Per migliorare la latenza, l'API classificazione viene esposta tramite HTTP invece di HTTPS, come in `https://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Occorre comunque usare un endpoint HTTPS se la pagina riepilogativa viene presentata tramite HTTPS.

Quando non vengono impostati i parametri, la risposta HTTP dell'API classificazione è una stringa in formato JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Il browser esegue quindi questa stringa come una chiamata alla funzione `callback()`.

Il parametro per la funzione di callback nell'esempio precedente presenta lo schema seguente:

- `ranking` consente la visualizzazione della classificazione degli URL.
- `eventId` viene usato internamente dal Servizio decisionale personalizzato per associare questa classificazione ai clic corrispondenti.
- `appId` consente alla funzione di callback di distinguere tra più applicazioni del Servizio decisionale personalizzato in esecuzione nella stessa pagina Web.
- `actionSets` elenca ogni set di azioni usate nella chiamata all'API classificazione, insieme al timestamp UTC dell'aggiornamento più recente che ha avuto esito positivo. Il Servizio decisionale personalizzato aggiorna periodicamente i feed dei set di azioni. Se, ad esempio, alcuni dei set di azioni non sono aggiornati, è possibile che la funzione di callback debba effettuare il fallback della relativa classificazione predefinita.

> [!IMPORTANT]
> I set di azioni specificati vengono elaborati, e possibilmente eliminati, per formare la classificazione predefinita degli articoli. La classificazione predefinita viene quindi riordinata e restituita nella risposta HTTP. Di seguito viene definita la classificazione predefinita:
>
> - All'interno di ogni set di azioni, se vengono restituiti più di 15 articoli, vengono conservati solo i 15 più recenti e gli altri vengono eliminati.
> - Quando si specificano più set di azioni, questi vengono uniti nello stesso ordine in cui si trovano nella chiamata all'API. L'ordinamento originale degli articoli viene mantenuto all'interno di ogni set di azioni. I duplicati vengono rimossi a favore delle copie più recenti.
> - I primi `n` articoli vengono conservati nell'elenco unito di articoli, dove `n=20` per impostazione predefinita.

### <a name="ranking-api-with-parameters"></a>API classificazione con parametri

Nell'API classificazione è possibile usare i parametri seguenti:

- `details=1` e `details=2` inseriscono dettagli aggiuntivi su ogni articolo elencato in `ranking`.
- `limit=<n>` specifica il numero massimo di articoli nella classificazione predefinita. `n` deve essere compreso tra `2` e `30` (altrimenti viene troncato rispettivamente a `2` o a `30`).
- `dnt=1` disabilita i cookie dell'utente.

I parametri possono essere combinati in una sintassi di stringa di query standard, ad esempio `details=2&dnt=1`.

> [!IMPORTANT]
> L'impostazione predefinita per l'Europa è `dnt=1` fintanto che il cliente non accetta il banner sui cookie. Questa deve essere anche l'impostazione predefinita per i siti Web che si rivolgono ai minorenni. Per altre informazioni, vedere le [Condizioni per l'utilizzo](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

L'elemento `details=1` inserisce il `guid` di ogni articolo, se viene fornito dall'API del set di azioni. La risposta HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

L'elemento `details=2` aggiunge altri dettagli che il Servizio decisionale personalizzato può estrarre dal [codice della funzionalità](https://github.com/Microsoft/mwt-ds/tree/master/Crawl) dei metatag SEO degli articoli:

- `title` da `<meta property="og:title" content="..." />` o `<meta property="twitter:title" content="..." />` o `<title>...</title>`
- `description` da `<meta property="og:description" ... />` o `<meta property="twitter:description" content="..." />` o `<meta property="description" content="..." />`
- `image` da `<meta property="og:image" content="..." />`
- `ds_id` da `<meta name=”microsoft:ds_id” content="..." />`

La risposta HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

L'elemento `<details>`:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>API premio

Il Servizio decisionale personalizzato usa i clic solo nello slot superiore. Ogni clic viene interpretato come un premio pari a 1. Ogni assenza di clic viene interpretato come un premio pari a 0. I clic vengono associati alle classifiche corrispondenti tramite ID evento, che vengono generati dalla chiamata all'[API classificazione](#ranking-api). Se necessario, gli ID evento possono essere passati tramite i cookie di sessione.

Per gestire un clic sullo slot superiore, inserire questo codice nella pagina di riepilogo:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Qui `data` è l'argomento della funzione `callback()`, come descritto in precedenza. L'uso di `data` nel codice di gestione dei clic richiede attenzione. Un esempio è illustrato in questa [esercitazione](custom-decision-service-tutorial-news.md#use-the-apis).

Solo per i test, l'API premio può essere chiamata tramite [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

L'effetto previsto è una risposta HTTP 200 (OK). È possibile vedere il premio pari a 1 per questo evento nel log (se è stata specificata una chiave dell'account di archiviazione di Azure nel portale).

## <a name="action-set-api-customer-provided"></a>API del set di azioni (specificata dal cliente)

A un alto livello, l'API del set di azioni restituisce un elenco di articoli (azioni). Ogni articolo è specificato dal relativo URL e (facoltativamente) dal titolo e dalla data di pubblicazione dell'articolo. È possibile specificare numerosi set di azioni nel portale. Per ogni set di azioni è consigliabile usare un'API del set di azioni diversa, come un URL distinto.

Ogni API del set di azioni può essere implementata in due modi: come feed RSS o come feed Atom. Entrambe le implementazioni devono essere conformi allo standard e restituire un XML corretto. Di seguito è riportato un esempio per RSS:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Ogni elemento `<item>` di livello superiore descrive un'azione:

- `<link>` è obbligatorio e viene usato come ID azione.
- `<date>` viene ignorato se è minore o uguale a 15 elementi; in caso contrario, è obbligatorio.
  - In presenza di più di 15 elementi, vengono usati i 15 più recenti.
  - Deve rispettare il formato standard per RSS o Atom:
    - [RFC 822](https://tools.ietf.org/html/rfc822) per RSS: ad esempio `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) per Atom: ad esempio `"2016-12-19T16:39:57-08:00"`
- `<title>` è facoltativo e viene usato per generare le funzionalità che descrivono l'articolo.
- `<guid>` è facoltativo e viene passato attraverso il sistema alla funzione di callback (se nella chiamata all'API classificazione è specificato il parametro `?details`).

Gli altri elementi all'interno di un `<item>` vengono ignorati.

La versione del feed Atom usa la stessa sintassi e le stesse convenzioni XML.

> [!TIP]
> Se il sistema usa ID di articolo propri, è possibile passarli nella funzione di callback usando `<guid>`.
