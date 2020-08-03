---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 043a37c604f56c70fb568fd027913380b80e3452
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425264"
---
:::row:::
    :::column span="3":::
        L'SDK di riconoscimento vocale per JavaScript è disponibile come pacchetto NPM, vedere <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> ed è il repository di GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-Services-Speech-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> -JS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Sebbene l'SDK di riconoscimento vocale per JavaScript sia disponibile come pacchetto NPM, i Web browser client e Node.js possono utilizzarlo, in modo da considerare le diverse implicazioni architettoniche di ogni ambiente. Il <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dom (Document Object Model) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> , ad esempio, non è disponibile per le applicazioni lato server, proprio come il <a href="https://nodejs.org/api/fs.html" target="_blank">file System <span class="docon docon-navigate-external x-hidden-focus"></span> </a> non è disponibile per le applicazioni lato client.

### <a name="nodejs-package-manager-npm"></a>Gestione pacchetti Node.js (NPM)

Per installare l'SDK di riconoscimento vocale per JavaScript, eseguire il `npm install` comando seguente.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Tag script HTML

In alternativa, è possibile includere direttamente un `<script>` tag nell'elemento HTMLS `<head>` , che si basa su <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM Syndicate <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Per ulteriori informazioni, vedere la <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Guida introduttiva <span class="docon docon-navigate-external x-hidden-focus"></span> di Web browser Speech SDK </a>.
