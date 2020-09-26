---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 3b7f459e0e95e798e5430414d23f34a455719a40
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376617"
---
:::row:::
    :::column span="3":::
        Speech SDK per JavaScript è disponibile come pacchetto NPM, vedere <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> e il relativo repository GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-Services-Speech-SDK-JS <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Sebbene l'SDK di riconoscimento vocale per JavaScript sia disponibile come pacchetto NPM, sia Node.js che i Web browser client possono utilizzarlo, considerando le diverse implicazioni architettoniche di ogni ambiente. Il <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dom (Document Object Model) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> , ad esempio, non è disponibile per le applicazioni lato server, proprio come il <a href="https://nodejs.org/api/fs.html" target="_blank">file System <span class="docon docon-navigate-external x-hidden-focus"></span> </a> non è disponibile per le applicazioni lato client.

### <a name="nodejs-package-manager-npm"></a>Gestione pacchetti Node.js (NPM)

Per installare l'SDK di riconoscimento vocale per JavaScript, eseguire il `npm install` comando seguente.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Per ulteriori informazioni, vedere la <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Guida introduttiva <span class="docon docon-navigate-external x-hidden-focus"></span> diNode.js Speech SDK </a>.
