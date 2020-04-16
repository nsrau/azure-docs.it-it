---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400049"
---
:::row:::
    :::column span="3":::
        JavaScript Speech SDK è disponibile come pacchetto npm, vedere <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> microsoft-cognitiveservices-speech-sdk</a> e il repository GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">complementare <span class="docon docon-navigate-external x-hidden-focus"> </span>cognitive-services-speech-sdk-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Anche se JavaScript Speech SDK è disponibile come pacchetto npm, pertanto sia i browser Web client che Node.js possono utilizzarlo, considerate le varie implicazioni architetturali di ogni ambiente. Ad esempio, il <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">modello DOM <span class="docon docon-navigate-external x-hidden-focus"></span> (Document Object Model)</a> non è disponibile per le applicazioni sul lato server, così come il <a href="https://nodejs.org/api/fs.html" target="_blank">file <span class="docon docon-navigate-external x-hidden-focus"></span> system</a> non è disponibile per le applicazioni lato client.

### <a name="nodejs-package-manager-npm"></a>Gestione pacchetti Node.js (NPM)

Per installare JavaScript Speech SDK, `npm install` eseguire il comando seguente.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Tag script HTML

In alternativa, è possibile `<script>` includere direttamente un `<head>` tag nell'elemento HTMLs, basandosi sul <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">sindacato <span class="docon docon-navigate-external x-hidden-focus"> </span> **JSDelivr** NPM </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Per ulteriori informazioni, vedere la <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">guida <span class="docon docon-navigate-external x-hidden-focus"> </span>introduttiva </a>di Web Browser Speech SDK .
