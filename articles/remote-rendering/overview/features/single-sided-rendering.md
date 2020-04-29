---
title: Rendering a lato singolo
description: Descrive le impostazioni di rendering a lato singolo e i casi di utilizzo
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682013"
---
# <a name="single-sided-rendering"></a>Rendering a lato singolo

Per migliorare le prestazioni, la maggior parte dei renderer utilizza l' [abbattimento del back-face](https://en.wikipedia.org/wiki/Back-face_culling) . Tuttavia, quando i mesh sono tagliati aperti con [piani tagliati](cut-planes.md), gli utenti spesso osserveranno il lato posteriore dei triangoli. Se questi triangoli vengono ricavati, il risultato non risulta convincente.

Per evitare in modo affidabile questo problema, è necessario eseguire il rendering di triangoli *bilaterali*. Poiché il mancato utilizzo dell'abbreviazione del back-face ha implicazioni sulle prestazioni, per impostazione predefinita il rendering remoto di Azure passa solo al rendering a doppio lato per le maglie che si intersecano con un piano di taglio.

L'impostazione di *rendering a lato singolo* consente di personalizzare questo comportamento.

> [!CAUTION]
> L'impostazione di rendering a lato singolo è una funzionalità sperimentale. Potrebbe essere rimossa in futuro. Non modificare l'impostazione predefinita, a meno che non risolvono effettivamente un problema critico nell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

L'impostazione di rendering a lato singolo ha un effetto solo per le mesh [convertite](../../how-tos/conversion/configure-model-conversion.md) con l' `opaqueMaterialDefaultSidedness` opzione impostata su `SingleSided`. Per impostazione predefinita, questa opzione è `DoubleSided`impostata su.

## <a name="single-sided-rendering-setting"></a>Impostazione del rendering a lato singolo

Sono disponibili tre modalità diverse:

**Normale:** In questa modalità viene sempre eseguito il rendering delle mesh quando vengono convertite. Ciò significa che le mesh convertite `opaqueMaterialDefaultSidedness` con `SingleSided` impostato su verranno sempre visualizzate con l'abbreviazione di back-face abilitata, anche quando si intersecano un piano di taglio.

**DynamicDoubleSiding:** In questa modalità, quando un piano tagliato interseca una mesh, viene automaticamente impostato il rendering a doppio lato. Questa modalità è la modalità predefinita.

**AlwaysDoubleSided:** Forza il rendering di tutte le geometrie a lato singolo in qualsiasi momento. Questa modalità viene esposta per lo più in modo che sia possibile confrontare facilmente l'effetto sulle prestazioni tra il rendering a lato singolo e quello a doppio lato.

La modifica delle impostazioni di rendering a lato singolo può essere eseguita come indicato di seguito:

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

* [Tagliare i piani](cut-planes.md)
* [Configurazione della conversione del modello](../../how-tos/conversion/configure-model-conversion.md)
