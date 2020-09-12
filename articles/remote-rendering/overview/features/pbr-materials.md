---
title: Materiali PBR
description: Descrive il tipo di materiale PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: a1fedb637bee9d98fb09d8fc3fa133b2992ce86e
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613672"
---
# <a name="pbr-materials"></a>Materiali PBR

I *materiali PBR* sono uno dei [tipi di materiale](../../concepts/materials.md) supportati nel rendering remoto di Azure. Vengono usati per [mesh](../../concepts/meshes.md) che devono ricevere illuminazione realistica.

PBR sta per **P**hysically **B**ased **R**endering e significa che il materiale descrive le proprietà visive di una superficie in modo fisicamente plausibile, in modo che i risultati realistici siano possibili in tutte le condizioni di illuminazione. La maggior parte degli strumenti di gioco moderni e gli strumenti per la creazione di contenuti supportano i materiali PBR perché sono considerati la migliore approssimazione di scenari reali per il rendering in tempo reale.

![Modello di esempio di Helmet glTF eseguito con ARR](media/helmet.png)

I materiali PBR non sono tuttavia una soluzione universale. Sono disponibili materiali che riflettono il colore in modo diverso a seconda dell'angolo di visualizzazione. Ad esempio, alcuni tessuti o vernici auto. Questi tipi di materiali non vengono gestiti dal modello PBR standard e non sono attualmente supportati dal rendering remoto di Azure. Sono incluse le estensioni PBR, ad esempio il *thin film* (superfici a più livelli) e *Clear-Coat* (per i colori dell'auto).

## <a name="common-material-properties"></a>Proprietà materiali comuni

Queste proprietà sono comuni a tutti i materiali:

* **albedoColor:** Questo colore viene moltiplicato con altri colori, ad esempio *albedoMap* o * :::no-loc text="vertex "::: colori*. Se la *trasparenza* è abilitata su un materiale, il canale alfa viene usato per modificare l'opacità, con un `1` significato completamente opaco e un `0` significato completamente trasparente. Il valore predefinito è bianco.

  > [!NOTE]
  > Quando un materiale PBR è completamente trasparente, come un pezzo di vetro perfettamente pulito, riflette ancora l'ambiente. I punti luminosi come il sole sono ancora visibili nella reflection. Questa operazione è diversa per i [materiali dei colori](color-materials.md).

* **albedoMap:** [Trama 2D](../../concepts/textures.md) per i valori di albedo per pixel.

* **alphaClipEnabled** e **AlphaClipThreshold:** se *alphaClipEnabled* è true, tutti i pixel in cui il valore alfa albedo è inferiore a *alphaClipThreshold* non verranno tracciati. Il ritaglio alfa può essere usato anche senza abilitare la trasparenza ed è molto più veloce per il rendering. I materiali ritagliati alfa sono comunque più lenti per il rendering rispetto ai materiali completamente opachi. Per impostazione predefinita, il ritaglio alfa è disabilitato.

* **textureCoordinateScale** e **textureCoordinateOffset:** la scala viene moltiplicata per le coordinate di trama UV, a cui viene aggiunto l'offset. Può essere usato per estendere e spostare le trame. La scala predefinita è (1,1) e offset è (0, 0).

* **useVertexColor:** Se la mesh contiene :::no-loc text="vertex"::: colori e questa opzione è abilitata, il colore dei mesh :::no-loc text="vertex"::: viene moltiplicato in *albedoColor* e *albedoMap*. Per impostazione predefinita, *useVertexColor* è disabilitato.

* **isDoubleSided:** Se la doppia facciata è impostata su true, il rendering dei triangoli con questo materiale viene eseguito anche se la fotocamera esamina i visi posteriori. Per l'illuminazione dei materiali PBR viene anche calcolato correttamente per i back-face. Per impostazione predefinita, questa opzione è disabilitata. Vedere anche [ :::no-loc text="Single-sided"::: rendering](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>Proprietà del materiale PBR

Il concetto di base del rendering fisico consiste nell'usare le proprietà *BaseColor*, *metality*e *roughity* per emulare un'ampia gamma di materiali reali. Una descrizione dettagliata di PBR esula dall'ambito di questo articolo. Per ulteriori informazioni su PBR, vedere [altre origini](http://www.pbr-book.org). Le proprietà seguenti sono specifiche per i materiali PBR:

* **BaseColor:** Nei materiali di PBR, il *colore dell'albedo* viene definito colore di *base*. Nel rendering remoto di Azure la proprietà del *colore albedo* è già presente tramite le proprietà del materiale comune, quindi non esiste alcuna proprietà del colore di base aggiuntiva.

* **rugosità** e **roughnessMap:** la rugosità definisce il modo in cui la superficie è approssimativa o smussata. Le superfici ruvide disperdono la luce in più direzioni rispetto alle superfici smussate, che rendono sfocate le riflessioni piuttosto che nitide. L'intervallo di valori è compreso tra `0.0` e `1.0` . Quando `roughness` è uguale a `0.0` , i riflessi saranno nitidi. Quando `roughness` è uguale a `0.5` , le riflessioni diventeranno sfocate.

  Se vengono specificati sia un valore di rugosità che una mappa di rugosità, il valore finale sarà il prodotto dei due.

* **metality** e **metalnessMap:** in Physics questa proprietà corrisponde a se una superficie è conduttiva o dielettrica. I materiali conduttivi hanno proprietà riflessivi differenti e tendono a essere riflessivi senza alcun colore albedo. Nei materiali di PBR questa proprietà influiscono sulla quantità di superficie che riflette l'ambiente circostante. I valori sono compresi tra `0.0` e `1.0` . Quando la metallurgia è `0.0` , il colore dell'albedo è completamente visibile e il materiale è simile a Plastic o Ceramic. Quando la metallurgia è `0.5` , ha un aspetto simile a quello del metallo verniciato. Quando la metallurgia è `1.0` , la superficie perde quasi completamente il colore dell'albedo e riflette solo le aree circostanti. Ad esempio, se `metalness` è `1.0` e `roughness` è, `0.0` una superficie appare come mirror reale.

  Se vengono specificati sia un valore di metallurgia che una mappa di metalica, il valore finale sarà il prodotto dei due.

  ![Sfere sottoposte a rendering con valori diversi di metalismo e rugosità](./media/metalness-roughness.png)

  Nell'immagine precedente, la sfera nell'angolo in basso a destra assomiglia a un materiale metal reale, la parte inferiore sinistra sembra ceramica o plastica. Anche il colore dell'albedo viene modificato in base alle proprietà fisiche. Con una maggiore rugosità, il materiale perde la nitidezza della reflection.

* **normalMap:** Per simulare i dettagli con granularità fine, è possibile fornire una [mappa normale](https://en.wikipedia.org/wiki/Normal_mapping) .

* **occlusionMap** e **aoScale:** l' [occlusione di ambiente](https://en.wikipedia.org/wiki/Ambient_occlusion) rende gli oggetti con fessure più realistici aggiungendo ombre alle aree di cui è stato bloccato l'aspetto. Il valore di occlusione è compreso tra `0.0` `1.0` e, dove `0.0` significa Darkness (nascosto) e `1.0` significa nessuna occlusione. Se una trama 2D viene fornita come mappa di occlusione, l'effetto viene abilitato e *aoScale* funge da moltiplicatore.

  ![Oggetto di cui è stato eseguito il rendering con e senza occlusione ambientale](./media/boom-box-ao2.gif)

* **trasparente:** Per i materiali PBR, esiste una sola impostazione di trasparenza: è abilitata o meno. L'opacità è definita dal canale alfa del colore albedo. Quando è abilitata, viene richiamata una pipeline di rendering più complessa per creare superfici semi-trasparenti. Il rendering remoto di Azure implementa la trasparenza OIT (true [Order Independent Transparency](https://en.wikipedia.org/wiki/Order-independent_transparency) ).

  Il rendering della geometria trasparente è dispendioso. Se sono necessari solo buchi in una superficie, ad esempio per le foglie di un albero, è preferibile usare invece il ritaglio alfa.

  ![Sfere sottoposte a rendering con un avviso di trasparenza totale ](./media/transparency.png) nell'immagine precedente, come la sfera più a destra è completamente trasparente, ma la reflection è ancora visibile.

  > [!IMPORTANT]
  > Se si suppone che il materiale venga passato da opaco a trasparente in fase di esecuzione, il renderer deve usare la [modalità di rendering](../../concepts/rendering-modes.md) *TileBasedComposition* . Questa limitazione non si applica ai materiali convertiti come materiali trasparenti per iniziare con.

## <a name="technical-details"></a>Dettagli tecnici

Il rendering remoto di Azure usa il microfacet Cook-Torrance BRDF con GGX NDF, Schlick Fresnel e un termine di visibilità correlato di GGX Smith con un termine di diffusione di Lambert. Questo modello è attualmente lo standard di settore. Per informazioni più dettagliate, vedere questo articolo: [rendering basato su fisico-Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Un'alternativa al modello di riattivazione della *rugosità del metallo* usato nel rendering remoto di Azure è il modello di *lucentezza PBR speculare* . Questo modello può rappresentare una gamma più ampia di materiali. Tuttavia, è più costoso e in genere non funziona correttamente per i casi in tempo reale.
Non è sempre possibile eseguire la conversione da *speculare-lucentezza* a *metallo-rugosità* come le coppie valore *(diffusa, speculare)* che non possono essere convertite in *(baseColor, metality)*. La conversione nell'altra direzione è più semplice e precisa, dal momento che tutte le coppie *(baseColor, metality)* corrispondono a coppie ben definite *(diffuse, speculari)* .

## <a name="api-documentation"></a>Documentazione dell'API

* [C# PbrMaterial (classe)](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RemoteManager. CreateMaterial ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [Classe C++ PbrMaterial](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterial)
* [C++ RemoteManager:: CreateMaterial ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Passaggi successivi

* [Materiali a colori](color-materials.md)
* [Trame](../../concepts/textures.md)
* [Mesh](../../concepts/meshes.md)
