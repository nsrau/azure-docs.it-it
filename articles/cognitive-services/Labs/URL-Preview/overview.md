---
title: Informazioni su Project URL Preview
titlesuffix: Azure Cognitive Services
description: Introduzione a Project URL Preview.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 7022c3b2d2f3618d55b0a70d2690abf1497ec6a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "61473180"
---
# <a name="what-is-project-url-preview"></a>Informazioni su Project URL Preview
L'endpoint URL Preview accetta un parametro di query URL e restituisce una risposta JSON con il nome della risorsa di destinazione, una breve descrizione e un collegamento a un'immagine da visualizzare in un'anteprima. La risposta include anche il flag [isFamilyFriendly](url-preview-reference.md#query-parameters) che indica se l'URL presenta contenuti per adulti, pirata o altri contenuti illegali. 

Per ottenere i risultati di URL Preview, inviare una richiesta GET e includere l'intestazione *Ocp-Apim-Subscription-Key* con un token valido:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
La risposta: 
```
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

```
## <a name="scenarios"></a>Scenari 

L'API URL Preview supporta brevi descrizioni delle risorse Web. Gli sviluppatori la usano per creare esperienze di anteprima avanzate.  Gli utenti possono condividere o aggiungere ai segnalibri pagine Web, notizie, blog, forum e così via. Questa API può anche essere usata per la moderazione dei contenuti.    

Le applicazioni usano URL Preview per inviare richieste Web all'endpoint con una query assegnata all'URL da visualizzare in anteprima.  La risposta JSON contiene le informazioni di anteprima: nome, descrizione della risorsa, flag *familyFriendly* e collegamenti che forniscono l'accesso a un'immagine rappresentativa e alla risorsa online completa. 

## <a name="terms-of-use"></a>Condizioni per l'utilizzo
Usare solo i dati di Project URL Preview per visualizzare frammenti e immagini in anteprima collegati ai siti di origine, nella condivisione URL avviata dall'utente finale su social media, chat bot o offerte simili. Non copiare, archiviare o memorizzare nella cache i dati ricevuti da Project URL Preview. Onorare eventuali richieste di disabilitazione delle anteprime che si possono ricevere dai proprietari di siti Web o contenuti.

L'utente, o una terza parte per conto dell'utente, non può usare, conservare, archiviare, memorizzare nella cache, condividere o distribuire alcun dato dall'API URL Preview per testare, sviluppare, eseguire il training, distribuire o rendere disponibile un servizio o una funzionalità non Microsoft. 

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](csharp.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in JavaScript](javascript.md)
- [Guida introduttiva in Node](node-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)
