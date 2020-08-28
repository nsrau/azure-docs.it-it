---
title: Componenti
description: Definizione di componenti nell'ambito del rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: d957da572bfdd3119dda506ac8f5bb42064d7758
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020304"
---
# <a name="components"></a>Componenti

Il rendering remoto di Azure usa il modello di [Sistema componente entità](https://en.wikipedia.org/wiki/Entity_component_system). Mentre le [entità](entities.md) rappresentano la posizione e la composizione gerarchica degli oggetti, i componenti sono responsabili dell'implementazione del comportamento.

I tipi di componenti usati più di frequente sono [:::no-loc text="mesh components":::](meshes.md) , che aggiungono mesh alla pipeline di rendering. Analogamente, vengono usati [componenti luminosi](../overview/features/lights.md) per aggiungere un'illuminazione e [componenti del piano di taglio](../overview/features/cut-planes.md), per tagliare le mesh aperte.

Tutti questi componenti usano come punto di riferimento la trasformazione (posizione, rotazione, scala) dell'entità a cui sono collegati.

## <a name="working-with-components"></a>Uso dei componenti

È possibile aggiungere, rimuovere e modificare facilmente i componenti a livello programmatico:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```


Un componente è associato a un'entità al momento della creazione e non può essere spostato successivamente in un'altra entità. I componenti vengono eliminati in modo esplicito con `Component.Destroy()` o automaticamente, quando l'entità proprietaria del componente viene distrutta.

È possibile aggiungere a un'entità una sola istanza di ogni tipo di componente alla volta.

## <a name="unity-specific"></a>Indicazioni specifiche per Unity

L'integrazione di Unity dispone di funzioni di estensione aggiuntive per l'interazione con i componenti. Vedere [Oggetti e componenti di gioco Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Passaggi successivi

* [Limiti degli oggetti](object-bounds.md)
* [Mesh](meshes.md)
