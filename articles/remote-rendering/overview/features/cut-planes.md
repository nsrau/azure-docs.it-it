---
title: Tagliare i piani
description: Illustra cosa sono i piani di taglio e come usarli
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b92bfad99c854f75c945121d352a7122d8c6db89
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011600"
---
# <a name="cut-planes"></a>Tagliare i piani

Un *piano di taglio* è una funzionalità visiva per il ritaglio dei pixel su un lato di un piano virtuale, rivelando l'interno delle [mesh](../../concepts/meshes.md).
L'immagine di seguito illustra l'effetto. A sinistra viene visualizzata la mesh originale, a destra è possibile esaminare l'interno della mesh:

![Piano di taglio](./media/cutplane-1.png)

## <a name="limitations"></a>Limitazioni

* Per il momento, il Rendering remoto di Azure supporta un **massimo di otto piani di taglio attivi**. È possibile creare più componenti dei piano di taglio, ma se si tenta di abilitarne più di uno simultaneamente, l'abilitazione verrà ignorata. Disabilitare prima gli altri piani se si intende passare al componente che interessa la scena.
* Ogni piano di taglio interessa tutti gli oggetti sottoposti a rendering remoto. Attualmente non è possibile escludere oggetti specifici o parti di mesh.
* I piani di taglio sono una funzionalità puramente visiva, non influiscono sul risultato di [query spaziali](spatial-queries.md). Se si vuole eseguire il ray casting in una mesh tagliata aperta, è possibile regolare il punto iniziale del raggio in modo che si trovi sul piano di taglio. In questo modo, il raggio può raggiungere solo le parti visibili.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Ogni piano di taglio attivo comporta un piccolo addebito durante il rendering. Disabilitare o eliminare i piani di taglio quando non sono necessari.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Per aggiungere un piano di taglio alla scena, è necessario creare un *CutPlaneComponent*. Il percorso e l'orientamento del piano sono determinati dall'[entità](../../concepts/entities.md) proprietario del componente.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>Proprietà di CutPlaneComponent

Le proprietà seguenti sono esposte in un componente del piano di taglio:

* `Enabled`: È possibile disattivare temporaneamente i piani di taglio disabilitando il componente. I piani di taglio disabilitati non comportano un sovraccarico di rendering e non vengono conteggiati rispetto al limite globale dei piani di taglio.

* `Normal`: Specifica quale direzione (+ X,-X, + Y,-Y, + Z,-Z) viene usata come piano normale. Questa direzione è relativa all'orientamento dell'entità proprietario. Spostare e ruotare l'entità proprietario per il posizionamento esatto.

* `FadeColor` e `FadeLength`:

  Se il valore alfa di *FadeColor* è diverso da zero, i pixel vicini al piano di taglio si dissolveranno verso la parte RGB di FadeColor. La potenza del canale alfa determina se la dissolvenza sarà totale verso il colore di dissolvenza o solo parziale. *FadeLength* definisce la distanza a cui avverrà la dissolvenza.

## <a name="next-steps"></a>Passaggi successivi

* [Rendering a lato singolo](single-sided-rendering.md)
* [Query spaziali](spatial-queries.md)
