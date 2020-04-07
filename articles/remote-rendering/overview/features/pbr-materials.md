---
title: Materiali PBR
description: Descrive il tipo di materiale PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680258"
---
# <a name="pbr-materials"></a>Materiali PBR

*I materiali PBR* sono uno dei tipi di materiale supportati in Rendering remoto di Azure.PBR materials are one of the supported [material types](../../concepts/materials.md) in Azure Remote Rendering. Sono utilizzati per [le maglie](../../concepts/meshes.md) che dovrebbero ricevere un'illuminazione realistica.

PBR sta per **P**isicamente **B**ased **R**endering e significa che il materiale descrive le proprietà visive di una superficie in modo fisicamente plausibile, in modo tale che risultati realistici sono possibili in tutte le condizioni di illuminazione. La maggior parte dei moderni motori di gioco e strumenti di creazione dei contenuti supportano i materiali PBR perché sono considerati la migliore approssimazione di scenari del mondo reale per il rendering in tempo reale.

![Modello di campione glTF casco reso da ARR](media/helmet.png)

I materiali PBR non sono una soluzione universale, però. Ci sono materiali che riflettono il colore in modo diverso a seconda dell'angolo di visione. Ad esempio, alcuni tessuti o vernici per auto. Questi tipi di materiali non sono gestiti dal modello PBR standard e non sono attualmente supportati dal rendering remoto di Azure.These kinds of materials are not handled by the standard PBR model, and currently not supported by Azure Remote Rendering. Questo include le estensioni PBR, come *Thin-Film* (superfici multistrato) e *Clear-Coat* (per vernici per auto).

## <a name="common-material-properties"></a>Proprietà comuni del materiale

Queste proprietà sono comuni a tutti i materiali:

* **albedoColor:** Questo colore viene moltiplicato con altri colori, ad esempio i colori *albedoMap* o *vertice*. Se *la trasparenza* è attivata su un materiale, il `1` canale alfa viene `0` utilizzato per regolare l'opacità, con significato completamente opaco e completamente trasparente. Il valore predefinito è bianco.

  > [!NOTE]
  > Quando un materiale PBR è completamente trasparente, come un pezzo di vetro perfettamente pulito, riflette ancora l'ambiente. Le macchie luminose come il sole sono ancora visibili nel riflesso. Questo è diverso per i materiali di [colore](color-materials.md).

* **albedoMap:** Una [trama 2D](../../concepts/textures.md) per i valori albedo per pixel.

* **alphaClipEnabled** e **alphaClipThreshold:** se *alphaClipEnabled* è true, tutti i pixel in cui il valore alfa dell'albedo è inferiore a *alphaClipThreshold* non verranno disegnati. Il ritaglio alfa può essere utilizzato anche senza abilitare la trasparenza ed è molto più veloce da eseguire il rendering. I materiali ritagliati alfa sono comunque più lenti a essere sottoposti a rendering rispetto ai materiali completamente opachi. Per impostazione predefinita, il ritaglio alfa è disabilitato.

* **textureCoordinateScale** e **textureCoordinateOffset:** la scala viene moltiplicata nelle coordinate della trama UV, l'offset viene aggiunto ad esso. Può essere utilizzato per allungare e spostare le texture. La scala di default è (1, 1) e offset è (0, 0).

* **useVertexColor:** Se la mesh contiene i colori dei vertici e questa opzione è abilitata, i colori dei vertici delle mesh vengono moltiplicati in *albedoColor* e *albedoMap*. Per impostazione predefinita, i colori dei vertici sono disabilitati.

* **isDoubleSided:** Se il doppio lato è impostato su true, i triangoli con questo materiale vengono visualizzati anche se la fotocamera sta guardando le facce posteriori. Per i materiali PBR l'illuminazione viene calcolata correttamente anche per le facce posteriori. Per impostazione predefinita, questa opzione è disabilitata. Consultate anche [Rendering su un lato.](single-sided-rendering.md)

## <a name="pbr-material-properties"></a>Proprietà del materiale PBR

L'idea di base del rendering basato fisicamente consiste nell'utilizzare le proprietà *BaseColor*, *Metalness*e *Roughness* per emulare una vasta gamma di materiali reali. Una descrizione dettagliata di PBR esula dall'ambito di questo articolo. Per ulteriori informazioni su PBR, vedere [altre origini](http://www.pbr-book.org). Le seguenti proprietà sono specifiche dei materiali PBR:

* **baseColor:** Nei materiali PBR, il *colore albedo* è indicato come il colore di *base*. In Azure Remote Rendering la proprietà *del colore albedo* è già presente tramite le proprietà comuni del materiale, pertanto non esiste alcuna proprietà aggiuntiva del colore di base.

* **ruvidità** e **rugositàMappa:** la rugosità definisce quanto è ruvida o liscia la superficie. Le superfici ruvide disperdono la luce in più direzioni rispetto alle superfici lisce, il che rende i riflessi sfocati piuttosto che nitidi. L'intervallo `0.0` di `1.0`valori è compreso tra . Quando `roughness` è `0.0`uguale a , i riflessi saranno nitidi. Quando `roughness` è `0.5`uguale a , i riflessi diventeranno sfocati.

  Se vengono forniti sia un valore di rugosità che una mappa di rugosità, il valore finale sarà il prodotto dei due.

* **metalness** e **metalnessMap:** In fisica, questa proprietà corrisponde a se una superficie è conduttiva o dielettrica. I materiali conduttivi hanno diverse proprietà riflettenti e tendono ad essere riflettenti senza colore albedo. Nei materiali PBR, questa proprietà influisce sulla quantità di superficie che riflette l'ambiente circostante. I valori `0.0` `1.0`vanno da a . Quando la `0.0`metallo è, il colore albedo è completamente visibile e il materiale sembra plastica o ceramica. Quando la `0.5`metallo è, sembra metallo dipinto. Quando la `1.0`metallo è, la superficie perde quasi completamente il suo colore albedo e riflette solo l'ambiente circostante. Ad esempio, `metalness` `1.0` se `roughness` `0.0` è ed è allora una superficie sembra specchio del mondo reale.

  Se vengono forniti sia un valore di metallia che una mappa di metallo, il valore finale sarà il prodotto dei due.

  ![metallità e rugosità](./media/metalness-roughness.png)

  Nella foto sopra, la sfera nell'angolo in basso a destra si presenta come un vero materiale metallico, il fondo sinistro sembra ceramica o plastica. Anche il colore dell'albedo sta cambiando a seconda delle proprietà fisiche. Con l'aumento della rugosità, il materiale perde la nitidezza del riflesso.

* **normalMappa:** Per simulare dettagli granulari, è possibile fornire una [mappa normale.](https://en.wikipedia.org/wiki/Normal_mapping)

* **occlusionMap** e **aoScale:** [L'occlusione ambientale](https://en.wikipedia.org/wiki/Ambient_occlusion) rende gli oggetti con fessure più realistiche aggiungendo ombre alle aree occluse. Il valore dell'occlusione `0.0` va da a `1.0`, dove `0.0` significa oscurità (occluso) e `1.0` non significa occlusioni. Se una trama 2D viene fornita come mappa di occlusione, l'effetto viene abilitato e *aoScale* funge da moltiplicatore.

  ![Mappa dell'occlusione](./media/boom-box-ao2.gif)

* **trasparente:** Per i materiali PBR è disponibile una sola impostazione di trasparenza: è abilitata o meno. L'opacità è definita dal canale alfa del colore albedo. Se abilitata, viene richiamata una pipeline di rendering più complessa per disegnare superfici semitrasparenti. Il rendering remoto di Azure implementa la [trasparenza indipendente dall'ordine](https://en.wikipedia.org/wiki/Order-independent_transparency) reale.

  La geometria trasparente è costosa da eseguire il rendering. Se sono necessari solo fori in una superficie, ad esempio per le foglie di un albero, è preferibile utilizzare il ritaglio alfa.

  ![Transparency](./media/transparency.png) Notice nell'immagine precedente, come la sfera più a destra è completamente trasparente, ma il riflesso è ancora visibile.

  > [!IMPORTANT]
  > Se si suppone che un materiale venga commutato da opaco a trasparente in fase di esecuzione, il renderer deve utilizzare la [modalità](../../concepts/rendering-modes.md)di rendering *TileBasedComposition* . Questa limitazione non si applica ai materiali convertiti come materiali trasparenti per cominciare.

## <a name="technical-details"></a>Dettagli tecnici

Il rendering remoto di Azure usa il micro-sfaccettato di Cook-Torrance BRDF con GGX NDF, Schlick Fresnel e un termine di visibilità correlato GGX Smith con un termine diffuso Lambert. Questo modello è lo standard industriale de facto al momento. Per ulteriori dettagli, fare riferimento a questo articolo: [Rendering basato su fisico -](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Un'alternativa al modello PBR *Metalness-Roughness* usato nel rendering remoto di Azure è il modello PBR *Speculare-Glossiness.* Questo modello può rappresentare una gamma più ampia di materiali. Tuttavia, è più costoso e di solito non funziona bene per i casi in tempo reale.
Non è sempre possibile eseguire la conversione da *Specular-Glossiness* a *Metalness-Roughness* in quanto esistono coppie di valori *(Diffuse, Specular)* che non possono essere convertite *in (BaseColor, Metalness)*. La conversione nell'altra direzione è più semplice e precisa, poiché tutte le coppie *(BaseColor, Metalness)* corrispondono a coppie ben definite *(Diffuse, Specular).*

## <a name="next-steps"></a>Passaggi successivi

* [Materiali colore](color-materials.md)
* [Trame](../../concepts/textures.md)
* [Maglie](../../concepts/meshes.md)
