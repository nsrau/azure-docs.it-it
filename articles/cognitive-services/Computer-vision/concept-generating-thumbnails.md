---
title: Miniature ritagliate in modo intelligente - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi alla generazione di anteprime di immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68945223"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generazione di anteprime con ritaglio intelligente con Visione artificiale

Un'immagine di anteprima è una rappresentazione di dimensioni ridotte di un'immagine. Le anteprime vengono usate per rappresentare le immagini e altri dati in modo più conveniente e con un layout più facile. L'API Visione artificiale usa il ritaglio intelligente, insieme al ridimensionamento dell'immagine, per creare anteprime intuitive per una determinata immagine.

L'algoritmo di generazione di anteprime di Visione artificiale funziona nel modo seguente:

1. Rimuovere gli elementi di distrazione dall'immagine e identificare _l'area di interesse_&mdash;dell'area dell'immagine in cui vengono visualizzati gli oggetti principali.
1. L'immagine viene ritagliata in base all'_area di interesse_ identificata.
1. Le proporzioni vengono modificate per adattare le dimensioni all'anteprima di destinazione.

## <a name="area-of-interest"></a>Area di interesse

Quando si carica un'immagine, l'API Visione artificiale la analizza per determinare l'*area di interesse*. Quindi può usare quest'area per determinare come ritagliare l'immagine. L'operazione di ritaglio, tuttavia, corrisponde sempre alle proporzioni desiderate se vengono specificate.

È anche possibile ottenere le coordinate del rettangolo delimitatore non elaborate di questa stessa *area di interesse* chiamando l'API **areaOfInterest**. Quindi si può usare queste informazioni per modificare l'immagine originale come si vuole.

## <a name="examples"></a>Esempi

L'anteprima generata può variare notevolmente a seconda dei valori specificati per l'altezza, la larghezza e il ritaglio intelligente, come illustrato nell'immagine seguente.

![Un'immagine di montagna accanto a varie configurazioni di ritaglio](./Images/thumbnail-demo.png)

La tabella seguente illustra anteprime tipiche generate da Visione artificiale per le immagini di esempio. Le anteprime sono state generate per un'altezza e una larghezza di destinazione pari a 50 pixel, con ritaglio intelligente abilitato.

| Immagine | Immagine di anteprima |
|-------|-----------|
|![Montagna all'aperto al tramonto, con la silhouette di una persona](./Images/mountain_vista.png) | ![Miniatura della montagna all'aperto al tramonto, con la silhouette di una persona](./Images/mountain_vista_thumbnail.png) |
|![Fiore bianco su sfondo verde](./Images/flower.png) | ![Anteprima di fiore - Analisi visione](./Images/flower_thumbnail.png) |
|![Donna sul tetto di un edificio](./Images/woman_roof.png) | ![miniatura di una donna sul tetto di un condominio](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'[assegnazione di tag alle immagini](concept-tagging-images.md) e la [classificazione delle immagini](concept-categorizing-images.md).
