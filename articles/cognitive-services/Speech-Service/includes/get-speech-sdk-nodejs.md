---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6dd4dfd0edd334005cc2af51a46f9ca2e634272f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399981"
---
:::row:::
    :::column span="3":::
        JavaScript Speech SDK è disponibile come pacchetto npm, vedere <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> microsoft-cognitiveservices-speech-sdk</a> e il repository GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">complementare <span class="docon docon-navigate-external x-hidden-focus"> </span>cognitive-services-speech-sdk-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Anche se JavaScript Speech SDK è disponibile come pacchetto npm, pertanto sia Node.js che i browser Web client possono utilizzarlo, considerate le varie implicazioni architetturali di ogni ambiente. Ad esempio, il <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">modello DOM <span class="docon docon-navigate-external x-hidden-focus"></span> (Document Object Model)</a> non è disponibile per le applicazioni sul lato server, così come il <a href="https://nodejs.org/api/fs.html" target="_blank">file <span class="docon docon-navigate-external x-hidden-focus"></span> system</a> non è disponibile per le applicazioni lato client.

### <a name="nodejs-package-manager-npm"></a>Gestione pacchetti Node.js (NPM)

Per installare JavaScript Speech SDK, `npm install` eseguire il comando seguente.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Per ulteriori informazioni, vedere la <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">guida <span class="docon docon-navigate-external x-hidden-focus"> </span>introduttiva di Node.js Speech SDK </a>.
