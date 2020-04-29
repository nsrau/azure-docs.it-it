---
title: Tagliare i piani
description: Spiega quali sono i piani tagliati e come usarli
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681025"
---
# <a name="cut-planes"></a>Tagliare i piani

Un *piano taglia* è una funzionalità visiva che consente di ritagliare i pixel su un lato di un piano virtuale, rivelando l'interno di [mesh](../../concepts/meshes.md).
L'immagine seguente illustra l'effetto. A sinistra viene visualizzata la mesh originale, a destra che può apparire all'interno della rete:

![Taglia piano](./media/cutplane-1.png)

## <a name="limitations"></a>Limitazioni

* Per il momento, il rendering remoto di Azure supporta un **massimo di otto piani di taglio attivi**. È possibile creare più componenti del piano tagliati, ma se si tenta di abilitare più simultaneamente, l'attivazione verrà ignorata. Disabilitare prima altri piani se si desidera cambiare il componente che influisce sulla scena.
* Ogni piano tagliato influiscono su tutti gli oggetti sottoposti a rendering remoto. Attualmente non è possibile escludere oggetti specifici o parti mesh.
* I piani tagliati sono puramente una funzionalità visiva e non influiscono sul risultato delle [query spaziali](spatial-queries.md). Se si desidera eseguire il cast a una mesh tagliata aperta, è possibile modificare il punto iniziale del raggio affinché si trovi sul piano tagliato. In questo modo, il raggio può raggiungere solo parti visibili.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Ogni piano di taglio attivo comporta un piccolo costo durante il rendering. Disabilitare o eliminare i piani tagliati quando non sono necessari.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Per aggiungere un piano tagliato alla scena, creare un *CutPlaneComponent*. La posizione e l'orientamento del piano sono determinati dall' [entità](../../concepts/entities.md)del proprietario del componente.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Proprietà di CutPlaneComponent

Le proprietà seguenti sono esposte in un componente del piano di taglio:

* **Abilitato:** È possibile disattivare temporaneamente i piani taglia disabilitando il componente. I piani tagliati disabilitati non comportano un sovraccarico di rendering e non vengono conteggiati al limite globale del piano di taglio.

* **Normale:** Specifica la direzione (+ X,-X, + Y,-Y, + Z,-Z) utilizzata come normale del piano. Questa direzione è relativa all'orientamento dell'entità proprietario. Spostare e ruotare l'entità Owner per la posizione esatta.

* **FadeColor** e **FadeLength:**

  Se il valore alfa di *FadeColor* è diverso da zero, i pixel vicini al piano tagliato si dissolveranno verso la parte RGB di FadeColor. Il livello di attendibilità del canale alfa determina se si dissolverà completamente verso il colore di dissolvenza o solo parzialmente. *FadeLength* definisce la distanza in base alla quale verrà eseguita questa dissolvenza.

## <a name="next-steps"></a>Passaggi successivi

* [Rendering a lato singolo](single-sided-rendering.md)
* [Query spaziali](spatial-queries.md)
