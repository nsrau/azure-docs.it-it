---
title: Tagliare i piani
description: Illustra cosa sono i piani di taglio e come usarli
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 468d21abc861e905472d1d15405b1c8ba9e5be74
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904886"
---
# <a name="cut-planes"></a>Tagliare i piani

Un *piano di taglio* è una funzionalità visiva per il ritaglio dei pixel su un lato di un piano virtuale, rivelando l'interno delle [mesh](../../concepts/meshes.md).
L'immagine di seguito illustra l'effetto. A sinistra viene visualizzata la mesh originale, a destra è possibile esaminare l'interno della mesh:

![Piano di taglio](./media/cutplane-1.png)

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

* `ObjectFilterMask`: Maschera di bit del filtro che determina la geometria interessata dal piano di taglio. Per informazioni dettagliate, vedere il paragrafo successivo.

### <a name="selective-cut-planes"></a>Piani tagliati selettivi

È possibile configurare singoli piani tagliati in modo che influiscano solo sulla geometria specifica. Nell'immagine seguente viene illustrato il modo in cui questa configurazione può risultare utile:

![Piani tagliati selettivi](./media/selective-cut-planes.png)

Il filtro funziona tramite il **confronto tra maschere di bit logiche** tra una maschera di bit sul lato piano e una seconda maschera di bit impostata sulla geometria. Se il risultato di un' `AND` operazione logica tra le maschere è diverso da zero, il piano di taglio influirà sulla geometria.

* La maschera di bit sul componente del piano di taglio viene impostata tramite la relativa `ObjectFilterMask` Proprietà
* La maschera di bit in una gerarchia secondaria di Geometry viene impostata tramite [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features)

Esempi:

| Maschera di filtro del piano tagliato | Maschera filtro geometria  | Risultato della logica `AND` | Il piano tagliato influiscono sulla geometria?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) = = 1   | (0000 0001) = = 1  | (0000 0001) = = 1  | Sì |
| (1111 0000) = = 240 | (0001 0001) = = 17 | (0001 0000) = = 16 | Sì |
| (0000 0001) = = 1   | (0000 0010) = = 2  | (0000 0000) = = 0  | No |
| (0000 0011) = = 3   | (0000 1000) = = 8  | (0000 0000) = = 0  | No |

>[!TIP]
> L'impostazione di un piano di taglio `ObjectFilterMask` su 0 significa che non influirà su alcuna geometria perché il risultato di Logical non `AND` può mai essere non null. Il sistema di rendering non prenderà in considerazione tali piani, pertanto si tratta di un metodo leggero per disabilitare singoli piani tagliati. Anche questi piani tagliati non vengono conteggiati rispetto al limite di 8 piani attivi.

## <a name="limitations"></a>Limitazioni

* Il rendering remoto di Azure supporta un **massimo di otto piani di taglio attivi**. È possibile creare più componenti dei piano di taglio, ma se si tenta di abilitarne più di uno simultaneamente, l'abilitazione verrà ignorata. Disabilitare prima altri piani se si desidera cambiare i componenti che interessano la scena.
* I piani tagliati sono una funzionalità puramente visiva che non influiscono sul risultato delle [query spaziali](spatial-queries.md). Se si vuole eseguire il ray casting in una mesh tagliata aperta, è possibile regolare il punto iniziale del raggio in modo che si trovi sul piano di taglio. In questo modo, il raggio può raggiungere solo le parti visibili.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Ogni piano di taglio attivo comporta un piccolo addebito durante il rendering. Disabilitare o eliminare i piani di taglio quando non sono necessari.

## <a name="api-documentation"></a>Documentazione dell'API

* [C# CutPlaneComponent (classe)](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [Classe C++ CutPlaneComponent](https://docs.microsoft.com/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Passaggi successivi

* [Rendering a lato singolo](single-sided-rendering.md)
* [Query spaziali](spatial-queries.md)
