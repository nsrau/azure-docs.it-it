---
title: Rendering su un solo lato
description: Descrive le impostazioni di rendering su un solo lato e i casi d'uso
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682013"
---
# <a name="single-sided-rendering"></a>Rendering su un solo lato

La maggior parte dei renderer utilizza [l'abbattimento in faccia](https://en.wikipedia.org/wiki/Back-face_culling) per migliorare le prestazioni. Tuttavia, quando le maglie vengono tagliate con piani di [taglio,](cut-planes.md)gli utenti spesso guardano il lato posteriore dei triangoli. Se questi triangoli vengono abbattuti, il risultato non sembra convincente.

Il modo per prevenire in modo affidabile questo problema, è quello di eseguire il rendering dei triangoli *fronte-retro*. Poiché la non configurazione dell'eliminazione in versante ha implicazioni sulle prestazioni, per impostazione predefinita il rendering remoto di Azure passa solo al rendering fronte/retro per le file con dimensioni che si intersecano con un piano di taglio.

L'impostazione di *rendering a lato singolo* consente di personalizzare questo comportamento.

> [!CAUTION]
> L'impostazione di rendering su un solo lato è una funzionalità sperimentale. Potrebbe essere rimosso di nuovo in futuro. Si prega di non modificare l'impostazione predefinita, a meno che non risolve realmente un problema critico nell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

L'impostazione di rendering a lato singolo ha effetto solo `opaqueMaterialDefaultSidedness` per `SingleSided`le file con conversione [convertita](../../how-tos/conversion/configure-model-conversion.md) con l'opzione impostata su . Per impostazione predefinita, `DoubleSided`questa opzione è impostata su .

## <a name="single-sided-rendering-setting"></a>Impostazione del rendering su un lato

Ci sono tre diverse modalità:

**Normale:** In questa modalità, le ane vengono sempre visualizzate man mano che vengono convertite. Ciò significa che `opaqueMaterialDefaultSidedness` le `SingleSided` maglie convertite con set to verranno sempre renderizzate con l'eliminazione della faccia posteriore abilitata, anche quando intersecano un piano di taglio.

**DynamicDoubleSiding:** In questa modalità, quando un piano di taglio interseca una mesh, viene automaticamente commutato al rendering fronte/retro. Questa modalità è la modalità predefinita.

**AlwaysDoubleSided:** Forza il rendering di tutta la geometria fronte-retro in qualsiasi momento. Questa modalità è per lo più esposta in modo da poter confrontare facilmente l'impatto sulle prestazioni tra il rendering a lato singolo e a doppio lato.

La modifica delle impostazioni di rendering su un solo lato può essere eseguita come segue:

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Piani di taglio](cut-planes.md)
* [Configurazione della conversione del modello](../../how-tos/conversion/configure-model-conversion.md)
