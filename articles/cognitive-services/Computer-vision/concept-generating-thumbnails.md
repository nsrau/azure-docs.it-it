---
title: Generazione di anteprime - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi alla generazione di anteprime di immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 9cb82b40d1fbec513b0219f26d1959fbd7f64570
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343963"
---
# <a name="generating-thumbnails"></a>Generazione di anteprime

Un'anteprima è una rappresentazione di piccole dimensioni di un'immagine con le dimensioni originali. Diversi dispositivi, ad esempio telefoni, tablet e PC, comportano la necessità di layout e di dimensioni delle anteprime diversi per l'esperienza utente. Grazie a una funzione di ritaglio intelligente, Visione artificiale consente di risolvere questo problema.

Dopo il caricamento di un'immagine, Visione artificiale genera un'anteprima di alta qualità e quindi analizza gli oggetti inclusi nell'immagine per identificare l'*area di interesse*. Facoltativamente può ritagliare l'immagine in base ai requisiti dell'area di interesse. L'anteprima generata può essere presentata con proporzioni diverse rispetto all'immagine originale in modo da soddisfare le esigenze dell'utente.

L'algoritmo per la generazione delle anteprime algoritmo funziona nel modo seguente:

1. Rimuove gli elementi indesiderati dall'immagine e identifica l'oggetto principale, ovvero l'area di interesse.
2. Ritaglia l'immagine in base all'area di interesse identificata.
3. Modifica le proporzioni per adattare le dimensioni all'anteprima di destinazione.

L'anteprima generata può variare notevolmente a seconda dei valori specificati per l'altezza, la larghezza e il ritaglio intelligente, come illustrato nell'immagine seguente.

![Anteprime](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>Esempi di generazione di anteprime

La tabella seguente illustra anteprime tipiche generate da Visione artificiale per le immagini di esempio. Le anteprime sono state generate per un'altezza e una larghezza di destinazione pari a 50 pixel, con ritaglio intelligente abilitato.

| Image | Immagine di anteprima |
|-------|-----------|
|![Panorama montano](./Images/mountain_vista.png) | ![Anteprima di panorama montano](./Images/mountain_vista_thumbnail.png) |
|![Fiore - Analisi visione](./Images/flower.png) | ![Anteprima di fiore - Analisi visione](./Images/flower_thumbnail.png) |
|![Donna su un tetto](./Images/woman_roof.png) | ![Anteprima di donna sul tetto](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi all'[assegnazione di tag alle immagini](concept-tagging-images.md) e alla [classificazione delle immagini](concept-categorizing-images.md).