---
title: Piani di taglio
description: Spiega cosa sono i piani di taglio e come usarli
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681025"
---
# <a name="cut-planes"></a>Piani di taglio

Un piano di *taglio* è una funzione visiva che ritaglia i pixel su un lato di un piano virtuale, rivelando l'interno delle file [di stampa.](../../concepts/meshes.md)
L'immagine seguente mostra l'effetto. La sinistra mostra la mesh originale, a destra si può guardare all'interno della mesh:

![Piano di taglio](./media/cutplane-1.png)

## <a name="limitations"></a>Limitazioni

* Per il momento, il rendering remoto di Azure supporta un **massimo di otto piani di taglio attivi.** È possibile creare più componenti del piano di taglio, ma se si tenta di abilitare più contemporaneamente, l'attivazione verrà ignorata. Disattivare prima gli altri piani se si desidera cambiare il componente che deve influire sulla scena.
* Ogni piano di taglio influisce su tutti gli oggetti sottoposti a rendering remoto. Attualmente non è possibile escludere oggetti o parti di mesh specifici.
* I piani di taglio sono puramente una caratteristica visiva, non influiscono sul risultato delle [query spaziali.](spatial-queries.md) Se si desidera proiettare il raggio gettato in una mesh taglia aperta, è possibile regolare il punto iniziale del raggio in modo che sia sul piano di taglio. In questo modo il raggio può colpire solo le parti visibili.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Ogni piano di taglio attivo comporta un piccolo costo durante il rendering. Disattivare o eliminare i piani di taglio quando non sono necessari.

## <a name="cutplanecomponent"></a>CutPlaneComponent

È possibile aggiungere un piano di taglio alla scena creando un *oggetto CutPlaneComponent*. La posizione e l'orientamento del piano sono determinati [dall'entità proprietaria](../../concepts/entities.md)del componente.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Proprietà CutPlaneComponent

Le seguenti proprietà vengono esposte su un componente del piano di taglio:

* **Abilitato:** È possibile disattivare temporaneamente i piani tagliati disattivando il componente. I piani di taglio disabilitati non comportano un sovraccarico di rendering e inoltre non vengono conteggiati rispetto al limite globale del piano di taglio.

* **Normale:** Consente di specificare la direzione utilizzata come normale al piano, ovvero X,-X,Y,-Y, z, z. Questa direzione è relativa all'orientamento dell'entità proprietario. Spostare e ruotare l'entità proprietario per il posizionamento esatto.

* **FadeColor** e **FadeLength:**

  Se il valore alfa di *FadeColor* è diverso da zero, i pixel vicini al piano di taglio si dissolveranno verso la parte RGB di FadeColor. L'intensità del canale alfa determina se si dissolverà completamente verso il colore di dissolvenza o solo parzialmente. *FadeLength* definisce su quale distanza avrà luogo questa dissolvenza.

## <a name="next-steps"></a>Passaggi successivi

* [Rendering a lato singolo](single-sided-rendering.md)
* [Query spaziali](spatial-queries.md)
