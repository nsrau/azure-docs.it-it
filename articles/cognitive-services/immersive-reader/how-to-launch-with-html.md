---
title: Avviare lo strumento di lettura immersiva con contenuto HTML
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come avviare il lettore immersivo con contenuto HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-javascript
ms.openlocfilehash: fbe54b3bd6e0fc8c42b4dfc7401be74ae65ce6cd
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406929"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Come avviare il lettore immersivo con contenuto HTML

Questo articolo illustra come avviare il lettore immersivo con contenuto HTML.

## <a name="prepare-the-html-content"></a>Preparare il contenuto HTML

Posizionare il contenuto di cui si desidera eseguire il rendering nel Reader immersivo all'interno di un elemento contenitore. Assicurarsi che l'elemento contenitore disponga di un univoco `id` . Il lettore immersivo fornisce supporto per gli elementi HTML di base. per altre informazioni, vedere il [riferimento](./reference.md#html-support) .

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

## <a name="get-the-html-content-in-javascript"></a>Ottenere il contenuto HTML in JavaScript

Usare l'oggetto `id` dell'elemento contenitore per ottenere il contenuto HTML nel codice JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Avviare il lettore immersivo con il contenuto HTML

Quando si chiama `ImmersiveReader.launchAsync` , impostare la proprietà del blocco `mimeType` su `text/html` per abilitare il rendering del codice HTML.

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