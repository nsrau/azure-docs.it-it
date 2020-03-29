---
title: Personalizzare il pulsante Lettore immersivo
titleSuffix: Azure Cognitive Services
description: Questo articolo ti mostrerà come personalizzare il pulsante che avvia Immersive Reader.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946210"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Come personalizzare il pulsante Immersive Reader

In questo articolo viene illustrato come personalizzare il pulsante che avvia Immersive Reader per soddisfare le esigenze dell'applicazione.

## <a name="add-the-immersive-reader-button"></a>Aggiungere il pulsante Lettore immersivo

L'SDK di Immersive Reader fornisce lo stile predefinito per il pulsante che avvia Immersive Reader. Utilizzare `immersive-reader-button` l'attributo class per abilitare questo stile.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Personalizzare lo stile del pulsante

Utilizzare `data-button-style` l'attributo per impostare lo stile del pulsante. I valori `icon`consentiti sono , `text`, e `iconAndText`. Il valore predefinito è `icon`.

### <a name="icon-button"></a>Pulsante icona

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Questo rende il rendering di quanto segue:

![Pulsante icona](./media/button-icon.png)

### <a name="text-button"></a>Pulsante Testo

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Questo rende il rendering di quanto segue:

![Pulsante icona](./media/button-text.png)

### <a name="icon-and-text-button"></a>Pulsante icona e testo

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Questo rende il rendering di quanto segue:

![Pulsante icona](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Personalizzare il testo del pulsante

Configurare la lingua e il testo `data-locale` alternativo per il pulsante utilizzando l'attributo . La lingua predefinita è l'italiano.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Personalizzare le dimensioni dell'icona

La dimensione dell'icona Lettore immersivo può `data-icon-px-size` essere configurata utilizzando l'attributo . In questo modo vengono impostate le dimensioni dell'icona in pixel. La dimensione predefinita è 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)