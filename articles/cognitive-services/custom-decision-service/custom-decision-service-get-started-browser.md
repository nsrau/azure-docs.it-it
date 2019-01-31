---
title: Chiamare l'API da un browser - Servizio decisionale personalizzato
titlesuffix: Azure Cognitive Services
description: Come ottimizzare una pagina Web effettuando chiamate API direttamente da un browser al Servizio decisionale personalizzato.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: db993693acc7e64a789564b92f4d0eacfa0e69f7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225262"
---
# <a name="call-api-from-a-browser"></a>Chiamare l'API da un browser

Questo articolo consente di effettuare chiamate alle API del Servizio decisionale personalizzato di Azure direttamente da un browser.

Assicurarsi per prima cosa di [Registrare l'applicazione](custom-decision-service-get-started-register.md).

Di seguito sono riportati i requisiti iniziali. L'applicazione viene modellata per avere una pagina di riepilogo con collegamenti a più pagine di articoli. La pagina di riepilogo usa il Servizio decisionale personalizzato per specificare l'ordinamento delle relative pagine di articolo. Inserire il codice seguente nell'intestazione HTML della pagina di riepilogo:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

L'argomento `data` contiene la classificazione degli URL da sottoporre a rendering. Per altre informazioni, vedere l'[API](custom-decision-service-api-reference.md) di riferimento.

Per gestire un clic dell'utente sull'articolo principale, richiamare il codice seguente nella pagina di riepilogo:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

In questo caso `data` corrisponde all'argomento alla funzione `callback()`. Un esempio di implementazione è reperibile in questa [esercitazione](custom-decision-service-tutorial-news.md#use-the-apis).

Infine, è necessario fornire l'API del set di azioni, che restituisce l'elenco degli articoli (azioni) che deve essere considerato dal Servizio decisionale personalizzato. Implementare questa API come un feed RSS, come illustrato di seguito:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Qui ogni elemento `<item>` di livello superiore descrive un articolo. `<link>` è obbligatorio e viene usato come un ID azione dal Servizio decisionale personalizzato. Specificare `<date>` (in formato RSS standard) se si hanno più di 15 articoli. Vengono usati i 15 articoli più recenti. `<title>` è facoltativo e viene usato per creare funzionalità correlate al testo per l'articolo.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare un'[esercitazione](custom-decision-service-tutorial-news.md) per un esempio più dettagliato.
* Consultare l'[API](custom-decision-service-api-reference.md) di riferimento per altre informazioni sulla funzionalità fornita.
