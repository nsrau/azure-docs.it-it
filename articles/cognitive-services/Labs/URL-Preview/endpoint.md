---
title: Endpoint dell'Anteprima URL progetto
titlesuffix: Azure Cognitive Services
description: Riepilogo dell'endpoint dell'Anteprima URL.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 43254db734a48f3e7aaa5a26a7fbf3981c9e9d87
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592858"
---
# <a name="project-url-preview-endpoint"></a>Endpoint dell'Anteprima URL progetto

L'API Anteprima URL include un endpoint.

## <a name="endpoint"></a>Endpoint
Per ottenere un'Anteprima URL, inviare una richiesta all'endpoint seguente. Usare le intestazioni e i parametri URL per le altre specifiche.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Parametri di query
|NOME|Value|Type|Obbligatorio|  
|----------|-----------|----------|--------------|  
|q|URL per l'anteprima|String |Yes|
|safeSearch|Il contenuto per adulti illegale o il contenuto pirata viene bloccato con il codice di errore 400 e non viene restituito il flag *isFamilyFriendly*. <p>Per il contenuto per adulti legale, il comportamento è riportato di seguito. Il codice di stato restituisce 200 e il flag *isFamilyFriendly* è impostato su false.<ul><li>safeSearch=strict: titolo, descrizione, URL e immagine non verranno restituiti.</li><li>safeSearch=moderate: si ottengono il titolo, l'URL e la descrizione ma non l'immagine descrittiva.</li><li>safeSearch=off: si ottengono tutti gli oggetti/elementi della risposta, ovvero titolo, URL, descrizione e immagine.</li></ul> |string|Non obbligatorio. </br> L'impostazione predefinita è safeSearch=strict.| 

## <a name="response-object"></a>Oggetto della risposta

La risposta include intestazioni HTTP e l'oggetto WebPage con gli attributi, come illustrato nell'esempio seguente: `name`, `url`, `description`, `isFamilyFriendly` e `primaryImageOfPage`.

```
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

```

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](csharp.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in JavaScript](javascript.md)
- [Guida introduttiva in Node](node-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)
