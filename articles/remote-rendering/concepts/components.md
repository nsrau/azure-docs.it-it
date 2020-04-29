---
title: Componenti
description: Definizione dei componenti nell'ambito del rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681896"
---
# <a name="components"></a>Componenti

Il rendering remoto di Azure usa il modello [Entity Component System](https://en.wikipedia.org/wiki/Entity_component_system) . Mentre le [entità](entities.md) rappresentano la posizione e la composizione gerarchica degli oggetti, i componenti sono responsabili dell'implementazione del comportamento.

I tipi di componenti usati più di frequente sono i [componenti mesh](meshes.md)che aggiungono mesh alla pipeline di rendering. Analogamente, i [componenti leggeri](../overview/features/lights.md) vengono usati per aggiungere i [componenti del piano](../overview/features/cut-planes.md) di illuminazione e di taglio vengono usati per tagliare le mesh aperte.

Tutti questi componenti usano la trasformazione (posizione, rotazione, scala) dell'entità a cui sono collegati, come punto di riferimento.

## <a name="working-with-components"></a>Utilizzo dei componenti

È possibile aggiungere, rimuovere e modificare facilmente i componenti a livello di codice:

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

Un componente è associato a un'entità al momento della creazione. Non può essere spostato successivamente in un'altra entità. I componenti vengono eliminati in modo `Component.Destroy()` esplicito con o automaticamente quando l'entità proprietaria del componente viene distrutta.

È possibile aggiungere a un'entità solo un'istanza di ogni tipo di componente alla volta.

## <a name="unity-specific"></a>Specifico di Unity

L'integrazione di Unity dispone di funzioni di estensione aggiuntive per l'interazione con i componenti. Vedere [oggetti del gioco Unity e componenti](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Passaggi successivi

* [Limiti degli oggetti](object-bounds.md)
* [Mesh](meshes.md)
