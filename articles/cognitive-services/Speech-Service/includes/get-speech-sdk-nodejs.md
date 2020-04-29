---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6dd4dfd0edd334005cc2af51a46f9ca2e634272f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399981"
---
:::row:::
    :::column span="3":::
        JavaScript Speech SDK è disponibile come pacchetto NPM, vedere <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> ed è il repository di GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-Services-Speech-SDK-JS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Sebbene JavaScript Speech SDK sia disponibile come pacchetto NPM, sia node. js che i Web browser client possono utilizzarlo, considerando le diverse implicazioni architettoniche di ogni ambiente. Il <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dom (Document Object Model) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> , ad esempio, non è disponibile per le applicazioni lato server, proprio come il <a href="https://nodejs.org/api/fs.html" target="_blank">file System <span class="docon docon-navigate-external x-hidden-focus"></span> </a> non è disponibile per le applicazioni lato client.

### <a name="nodejs-package-manager-npm"></a>Gestione pacchetti node. js (NPM)

Per installare JavaScript Speech SDK, eseguire il comando seguente `npm install` .

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Per altre informazioni, vedere la <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Guida introduttiva <span class="docon docon-navigate-external x-hidden-focus"> </span>di node. js Speech SDK </a>.
