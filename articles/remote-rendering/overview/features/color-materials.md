---
title: Materiali colore
description: Descrive il tipo di materiale colore.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681077"
---
# <a name="color-materials"></a>Materiali colore

*I materiali a* colori sono uno dei tipi di materiale supportati in Rendering remoto di Azure.Color materials are one of the supported material [types](../../concepts/materials.md) in Azure Remote Rendering. Sono utilizzati per [le maglie](../../concepts/meshes.md) che non dovrebbero ricevere alcun tipo di illuminazione, ma piuttosto essere piena luminosità in ogni momento. Questo potrebbe essere il caso di materiali "luminosi", come cruscotti per auto, lampadine o dati che incorporano già l'illuminazione statica, come i modelli ottenuti attraverso la [fotogrammetria](https://en.wikipedia.org/wiki/Photogrammetry).

I materiali a colori sono più efficienti da renderizzare rispetto ai [materiali PBR](pbr-materials.md) grazie al loro modello di ombreggiatura più semplice. Supportano anche diverse modalità di trasparenza.

## <a name="common-material-properties"></a>Proprietà comuni del materiale

Queste proprietà sono comuni a tutti i materiali:

* **albedoColor:** Questo colore viene moltiplicato con altri colori, ad esempio i colori *albedoMap* o *vertice*. Se *la trasparenza* è attivata su un materiale, il `1` canale alfa viene `0` utilizzato per regolare l'opacità, con significato completamente opaco e completamente trasparente. Il valore predefinito è bianco.

  > [!NOTE]
  > Poiché i materiali a colori non riflettono l'ambiente, un materiale di colore completamente trasparente diventa invisibile. Questo è diverso per i [materiali PBR](pbr-materials.md).

* **albedoMap:** Una [trama 2D](../../concepts/textures.md) per i valori albedo per pixel.

* **alphaClipEnabled** e **alphaClipThreshold:** se *alphaClipEnabled* è true, tutti i pixel in cui il valore alfa dell'albedo è inferiore a *alphaClipThreshold* non verranno disegnati. Il ritaglio alfa può essere utilizzato anche senza abilitare la trasparenza ed è molto più veloce da eseguire il rendering. I materiali ritagliati alfa sono comunque più lenti a essere sottoposti a rendering rispetto ai materiali completamente opachi. Per impostazione predefinita, il ritaglio alfa è disabilitato.

* **textureCoordinateScale** e **textureCoordinateOffset:** la scala viene moltiplicata nelle coordinate della trama UV, l'offset viene aggiunto ad esso. Può essere utilizzato per allungare e spostare le texture. La scala di default è (1, 1) e offset è (0, 0).

* **useVertexColor:** Se la mesh contiene i colori dei vertici e questa opzione è abilitata, i colori dei vertici delle mesh vengono moltiplicati in *albedoColor* e *albedoMap*. Per impostazione predefinita, i colori dei vertici sono disabilitati.

* **isDoubleSided:** Se il doppio lato è impostato su true, i triangoli con questo materiale vengono visualizzati anche se la fotocamera sta guardando le facce posteriori. Per impostazione predefinita, questa opzione è disabilitata. Consultate anche [Rendering su un lato.](single-sided-rendering.md)

## <a name="color-material-properties"></a>Proprietà del materiale di colore

Le seguenti proprietà sono specifiche dei materiali a colori:

* **vertexMix:** Questo valore `0` `1` compreso tra e specifica quanto fortemente il colore del vertice in una [mesh](../../concepts/meshes.md) contribuisce al colore finale. Con il valore predefinito 1, il colore del vertice viene moltiplicato completamente nel colore dell'albedo. Con un valore pari a 0, i colori dei vertici vengono ignorati completamente.

* **transparencyMode:** Contrariamente ai [materiali PBR,](pbr-materials.md)i materiali a colori distinguono tra diverse modalità di trasparenza:

  1. **Opaco:** La modalità predefinita disabilita la trasparenza. Il ritaglio alfa è comunque possibile, tuttavia, e dovrebbe essere preferito, se sufficiente.
  
  1. **AlphaBlended:** Questa modalità è simile alla modalità di trasparenza per i materiali PBR. Dovrebbe essere utilizzato per materiali trasparente come il vetro.

  1. **Additivo:** Questa modalità è la modalità di trasparenza più semplice ed efficiente. Il contributo del materiale viene aggiunto all'immagine sottoposta a rendering. Questa modalità può essere utilizzata per simulare oggetti luminosi (ma ancora trasparenti), ad esempio marcatori utilizzati per evidenziare oggetti importanti.

## <a name="next-steps"></a>Passaggi successivi

* [Materiali PBR](pbr-materials.md)
* [Trame](../../concepts/textures.md)
* [Maglie](../../concepts/meshes.md)
