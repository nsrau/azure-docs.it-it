---
title: Visualizza la matematica nel Lettore Immersivo
titleSuffix: Azure Cognitive Services
description: Questo articolo ti mostrerà come visualizzare la matematica in Immersive Reader.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946122"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Come visualizzare la matematica in Immersive Reader

Il lettore immersivo può visualizzare la matematica quando viene fornito sotto forma di Mathematical Markup Language ([MathML](https://developer.mozilla.org/docs/Web/MathML)).
Il tipo MIME può essere impostato tramite il [blocco](../reference.md#chunk)Lettore immersivo. Per ulteriori informazioni, vedere [Tipi MIME supportati.](../reference.md#supported-mime-types)

## <a name="send-math-to-the-immersive-reader"></a>Invia matematica al lettore immersivo
Per inviare operazioni matematiche al lettore Immersive, fornire un blocco contenente ```application/mathml+xml```MathML e impostare il tipo MIME su ;

Ad esempio, se i contenuti sono i seguenti:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Quindi è possibile visualizzare il contenuto utilizzando il seguente JavaScript.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Quando avvii il Lettore Immersivo, dovresti vedere:

![Matematica in Lettore Immersivo](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../reference.md)