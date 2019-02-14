---
title: Chiamare l'API da un'app - Servizio decisionale personalizzato
titlesuffix: Azure Cognitive Services
description: Come chiamare le API Servizio decisionale personalizzato da un'app per smartphone.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: 0e5c99aae61fb927ea7f101bab74d661a747f88b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870011"
---
# <a name="call-api-from-an-app"></a>Chiamare l'API da un'app

Chiamare le API del Servizio decisionale personalizzato di Azure da un'app per smartphone. Questo articolo illustra come iniziare a usare alcune opzioni di base.

Assicurarsi per prima cosa di [Registrare l'applicazione](custom-decision-service-get-started-register.md).

Le chiamate API effettuate dall'app per smartphone al Servizio decisionale personalizzato possono essere due: una chiamata all'API classificazione per ottenere un elenco classificato del proprio contenuto e una chiamata all'API premio per segnalare un premio. Ecco le chiamate di esempio in [cURL](https://en.wikipedia.org/wiki/CURL).

Per altre informazioni, vedere l'[API](custom-decision-service-api-reference.md) di riferimento.

Iniziare con la chiamata all'API classificazione. Creare il file `<request.json>`, che ha l'ID del set di azioni. Questo ID è il nome del feed RSS o Atom corrispondente che è stato inserito nel portale:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

È possibile specificare molti set di azioni, come indicato di seguito:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Questo file JSON viene quindi inviato come parte della richiesta di classificazione:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Qui `<appId>` è il nome dell'applicazione registrato nel portale. Si dovrebbe ricevere un set ordinato di elementi di contenuto, di cui è possibile eseguire il rendering nell'applicazione. Ecco un esempio di restituzione:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

La prima parte della restituzione è un elenco di azioni ordinate, specificate per ID azione. Per un articolo, l'ID azione è un URL. La richiesta complessiva ha anche un `<eventId>` univoco, creato dal sistema.

Successivamente si può specificare se si è osservato un clic sul primo elemento di contenuto da questo evento, ovvero `<actionId3>`. È quindi possibile segnalare un premio per questo `<eventId>` al Servizio decisionale personalizzato tramite l'API premio con un'altra richiesta, ad esempio:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

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
