---
title: Endpoint dell'Anteprima URL progetto - Servizi cognitivi Microsoft | Microsoft Docs
description: Riepilogo dell'endpoint dell'Anteprima URL.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: ddd53aa49db01d7a6db397eb285d0854edc59388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376225"
---
# <a name="project-url-preview-endpoint"></a>Endpoint dell'Anteprima URL progetto

L'API Anteprima URL include un endpoint.

## <a name="endpoint"></a>Endpoint
Per ottenere un'Anteprima URL, inviare una richiesta all'endpoint seguente. Usare le intestazioni e i parametri URL per le altre specifiche.

GET:
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

````

### <a name="query-parameters"></a>Parametri di query
|NOME|Valore|type|Obbligatoria|  
|----------|-----------|----------|--------------|  
|q|URL per l'anteprima|string |Sì|
|safeSearch|Il contenuto per adulti illegale o il contenuto pirata viene bloccato con il codice di errore 400 e non viene restituito il flag *isFamilyFriendly*. <p>Per il contenuto per adulti legale, il comportamento è riportato di seguito. Il codice di stato restituisce 200 e il flag *isFamilyFriendly* è impostato su false.<ul><li>safeSearch=strict: titolo, descrizione, URL e immagine non verranno restituiti.</li><li>safeSearch=moderate: si ottengono il titolo, l'URL e la descrizione ma non l'immagine descrittiva.</li><li>safeSearch=off: si ottengono tutti gli oggetti/elementi della risposta, ovvero titolo, URL, descrizione e immagine.</li></ul> |string|Non obbligatorio. </br> L'impostazione predefinita è safeSearch=strict.| 

## <a name="response-object"></a>Oggetto della risposta

La risposta include intestazioni HTTP e l'oggetto WebPage con gli attributi, come illustrato nell'esempio seguente: `name`, `url`, `description`, `isFamilyFriendly` e `primaryImageOfPage`.

````
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](csharp.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in JavaScript](javascript.md)
- [Guida introduttiva in Node](node-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)
