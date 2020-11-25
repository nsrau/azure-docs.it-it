---
title: Materiali a colori
description: Descrive il tipo di materiale colore.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 26ac1714330bba06c01d33b47105f04c600c7729
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024096"
---
# <a name="color-materials"></a>Materiali a colori

I *materiali colori* sono uno dei [tipi di materiale](../../concepts/materials.md) supportati nel rendering remoto di Azure. Vengono usati per le [mesh](../../concepts/meshes.md) che non dovrebbero ricevere alcun tipo di illuminazione, ma devono essere sempre completamente luminosi. Questo potrebbe essere il caso di materiali "luminosi", come i dashboard dell'automobile, le lampadine o i dati che incorporano già l'illuminazione statica, ad esempio i modelli ottenuti tramite [fotogrammetria](https://en.wikipedia.org/wiki/Photogrammetry).

I materiali colori sono più efficienti per il rendering rispetto ai [materiali PBR](pbr-materials.md) a causa del modello di ombreggiatura più semplice. Supportano inoltre diverse modalità di trasparenza.

## <a name="common-material-properties"></a>Proprietà materiali comuni

Queste proprietà sono comuni a tutti i materiali:

* **albedoColor:** Questo colore viene moltiplicato con altri colori, ad esempio *albedoMap* o *:::no-loc text="vertex"::: colori*. Se la *trasparenza* è abilitata su un materiale, il canale alfa viene usato per modificare l'opacità, con un `1` significato completamente opaco e un `0` significato completamente trasparente. Il valore predefinito è bianco.

  > [!NOTE]
  > Poiché i materiali dei colori non riflettono l'ambiente, un materiale colorato completamente trasparente diventa invisibile. Questa operazione è diversa per i [materiali PBR](pbr-materials.md).

* **albedoMap:** [Trama 2D](../../concepts/textures.md) per i valori di albedo per pixel.

* **alphaClipEnabled** e **AlphaClipThreshold:** se *alphaClipEnabled* è true, tutti i pixel in cui il valore alfa albedo è inferiore a *alphaClipThreshold* non verranno tracciati. Il ritaglio alfa può essere usato anche senza abilitare la trasparenza ed è molto più veloce per il rendering. I materiali ritagliati alfa sono comunque più lenti per il rendering rispetto ai materiali completamente opachi. Per impostazione predefinita, il ritaglio alfa è disabilitato.

* **textureCoordinateScale** e **textureCoordinateOffset:** la scala viene moltiplicata per le coordinate di trama UV, a cui viene aggiunto l'offset. Può essere usato per estendere e spostare le trame. La scala predefinita è (1,1) e offset è (0, 0).

* **useVertexColor:** Se la mesh contiene :::no-loc text="vertex"::: colori e questa opzione è abilitata, il colore dei mesh :::no-loc text="vertex"::: viene moltiplicato in *albedoColor* e *albedoMap*. Per impostazione predefinita, *useVertexColor* è disabilitato.

* **isDoubleSided:** Se la doppia facciata è impostata su true, il rendering dei triangoli con questo materiale viene eseguito anche se la fotocamera esamina i visi posteriori. Per impostazione predefinita, questa opzione è disabilitata. Vedere anche [ :::no-loc text="Single-sided"::: rendering](single-sided-rendering.md).

* **TransparencyWritesDepth:** Se il flag TransparencyWritesDepth è impostato sul materiale e il materiale è trasparente, gli oggetti che usano questo materiale contribuiranno anche al buffer di profondità finale. Vedere la proprietà Color Material *transparencyMode* nella sezione successiva. L'abilitazione di questa funzionalità è consigliata se il caso d'uso necessita di una riproiezione più plausibile della [fase ritardata](late-stage-reprojection.md) di scene completamente trasparenti. Per le scene miste/trasparenti miste, questa impostazione può presentare un comportamento di riproiezione non plausibile o elementi di riproiezione. Per questo motivo, l'impostazione predefinita e consigliata per il caso d'uso generale è disabilitare questo flag. I valori di profondità scritti sono ricavati dal livello di profondità per pixel dell'oggetto più vicino alla fotocamera.

* **FresnelEffect:** Questo flag di materiale Abilita l' [effetto Fresnel](../../overview/features/fresnel-effect.md) aggiuntivo sul rispettivo materiale. L'aspetto dell'effetto è disciplinato dagli altri parametri Fresnel illustrati nell'esempio seguente. 

* **FresnelEffectColor:** Colore Fresnel usato per questo materiale. Importante solo quando il bit dell'effetto Fresnel è stato impostato su questo materiale (vedere sopra). Questa proprietà controlla il colore di base dello splendore Fresnel (vedere [effetto Fresnel](../../overview/features/fresnel-effect.md) per una spiegazione completa). Attualmente solo i valori del canale RGB sono importanti e il valore alfa verrà ignorato.

* **FresnelEffectExponent:** Esponente Fresnel utilizzato per questo materiale. Importante solo quando il bit dell'effetto Fresnel è stato impostato su questo materiale (vedere sopra). Questa proprietà controlla la diffusione del luccichio di Fresnel. Il valore minimo 0,01 causa una distribuzione nell'intero oggetto. Il valore massimo 10,0 limita lo splendore solo ai bordi più abbellibili visibili.

## <a name="color-material-properties"></a>Proprietà del materiale colori

Le proprietà seguenti sono specifiche dei materiali colori:

* **vertexMix:** Questo valore tra `0` e `1` specifica il modo :::no-loc text="vertex"::: in cui il colore in una [mesh](../../concepts/meshes.md) contribuisce al colore finale. Il valore predefinito è 1, il :::no-loc text="vertex"::: colore viene moltiplicato per il colore di albedo completamente. Con un valore pari a 0, i :::no-loc text="vertex"::: colori vengono ignorati interamente.

* **transparencyMode:** Contrariamente ai [materiali di PBR](pbr-materials.md), i materiali dei colori si distinguono tra modalità di trasparenza diverse:

  1. **Opaco:** La modalità predefinita Disabilita la trasparenza. Il ritaglio alfa è comunque possibile, ma deve essere preferito, se sufficiente.
  
  1. **AlphaBlended:** Questa modalità è simile alla modalità di trasparenza per i materiali PBR. Deve essere usato per i materiali See-through, ad esempio Glass.

  1. **Additivo:** Questa modalità è la modalità di trasparenza più semplice e più efficiente. Il contributo del materiale viene aggiunto all'immagine di cui è stato eseguito il rendering. Questa modalità può essere usata per simulare oggetti luminosi (ma ancora trasparenti), ad esempio marcatori usati per evidenziare oggetti importanti.

## <a name="api-documentation"></a>Documentazione dell'API

* [C# ColorMaterial (classe)](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RemoteManager. CreateMaterial ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [Classe C++ ColorMaterial](/cpp/api/remote-rendering/colormaterial)
* [C++ RemoteManager:: CreateMaterial ()](/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Passaggi successivi

* [Materiali PBR](pbr-materials.md)
* [Trame](../../concepts/textures.md)
* [Mesh](../../concepts/meshes.md)