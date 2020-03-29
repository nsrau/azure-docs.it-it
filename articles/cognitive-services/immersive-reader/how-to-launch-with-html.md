---
title: Avviare lo strumento di lettura immersiva con contenuto HTML
titleSuffix: Azure Cognitive Services
description: Questo articolo ti mostrerà come lanciare Immersive Reader con contenuti HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946243"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Come avviare Immersive Reader con contenuti HTML

In questo articolo viene illustrato come avviare Immersive Reader con contenuto HTML.

## <a name="prepare-the-html-content"></a>Preparare il contenuto HTML

Inserire il contenuto di cui si desidera eseguire il rendering nel lettore Immersive all'interno di un elemento contenitore. Assicurarsi che l'elemento `id`contenitore abbia un oggetto univoco. Immersive Reader fornisce supporto per gli elementi HTML di base, vedere il [riferimento](./reference.md#html-support) per ulteriori informazioni.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Ottenere il contenuto HTML in JavaScriptGet the HTML content in JavaScript

Usa `id` l'elemento container per ottenere il contenuto HTML nel codice JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Lancia Immersive Reader con i tuoi contenuti HTML

Quando `ImmersiveReader.launchAsync`si chiama , `mimeType` impostare `text/html` la proprietà del blocco su per abilitare il rendering HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)