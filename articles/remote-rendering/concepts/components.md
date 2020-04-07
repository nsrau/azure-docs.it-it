---
title: Componenti
description: Definizione dei componenti nell'ambito del rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681896"
---
# <a name="components"></a>Componenti

Il rendering remoto di Azure usa il modello [di sistema del componente entità.](https://en.wikipedia.org/wiki/Entity_component_system) Mentre [le entità](entities.md) rappresentano la posizione e la composizione gerarchica degli oggetti, i componenti sono responsabili dell'implementazione del comportamento.

I tipi di componenti utilizzati più di frequente sono [i componenti mesh](meshes.md), che aggiungono mesh nella pipeline di rendering. Analogamente, i [componenti della luce](../overview/features/lights.md) vengono utilizzati per aggiungere l'illuminazione e tagliare i componenti del [piano](../overview/features/cut-planes.md) vengono utilizzati per tagliare le meglie aperte.

Tutti questi componenti utilizzano la trasformazione (posizione, rotazione, scala) dell'entità a cui sono collegati, come punto di riferimento.

## <a name="working-with-components"></a>Utilizzo dei componenti

È possibile aggiungere, rimuovere e modificare facilmente i componenti a livello di codice:You can easily add, remove, and manipulate components programmatically:

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

Un componente viene collegato a un'entità al momento della creazione. Non può essere spostato in un'altra entità in un secondo momento. I componenti vengono `Component.Destroy()` eliminati in modo esplicito con o automaticamente quando l'entità proprietaria del componente viene eliminata.

È possibile aggiungere a un'entità solo un'istanza di ogni tipo di componente alla volta.

## <a name="unity-specific"></a>Specifico dell'unità

L'integrazione Unity dispone di funzioni di estensione aggiuntive per l'interazione con i componenti. Consultate [Componenti e oggetti di gioco Unity.](../how-tos/unity/objects-components.md)

## <a name="next-steps"></a>Passaggi successivi

* [Limiti dell'oggetto](object-bounds.md)
* [Maglie](meshes.md)
