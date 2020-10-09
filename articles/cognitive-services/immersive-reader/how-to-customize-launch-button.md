---
title: Personalizzare il pulsante dello strumento di lettura immersiva
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come personalizzare il pulsante che avvia il lettore immersivo.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75946210"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Come personalizzare il pulsante Lettore immersivo

Questo articolo illustra come personalizzare il pulsante che avvia il lettore immersivo per soddisfare le esigenze dell'applicazione.

## <a name="add-the-immersive-reader-button"></a>Aggiungere il pulsante Lettore immersivo

Immersion Reader SDK fornisce lo stile predefinito per il pulsante che avvia il lettore immersivo. Usare l' `immersive-reader-button` attributo Class per abilitare questo stile.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Personalizzare lo stile del pulsante

Utilizzare l' `data-button-style` attributo per impostare lo stile del pulsante. I valori consentiti sono `icon` , `text` e `iconAndText` . Il valore predefinito è `icon`.

### <a name="icon-button"></a>Pulsante icona

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Viene eseguito il rendering degli elementi seguenti:

![Pulsante icona](./media/button-icon.png)

### <a name="text-button"></a>Pulsante testo

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Viene eseguito il rendering degli elementi seguenti:

![Pulsante icona](./media/button-text.png)

### <a name="icon-and-text-button"></a>Pulsante icona e testo

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Viene eseguito il rendering degli elementi seguenti:

![Pulsante icona](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Personalizzare il testo del pulsante

Configurare la lingua e il testo alternativo per il pulsante usando l' `data-locale` attributo. La lingua predefinita è l'italiano.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Personalizzare le dimensioni dell'icona

La dimensione dell'icona del lettore immersivo può essere configurata usando l' `data-icon-px-size` attributo. Questo consente di impostare le dimensioni dell'icona in pixel. La dimensione predefinita è 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)