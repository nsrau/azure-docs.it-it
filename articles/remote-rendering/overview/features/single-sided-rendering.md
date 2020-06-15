---
title: Rendering a lato singolo
description: Descrive le impostazioni di rendering a lato singolo e i casi d'uso
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 97e0456e274adee7d678e373cfd92b5003f3d801
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759099"
---
# <a name="single-sided-rendering"></a>Rendering a lato singolo

Per migliorare le prestazioni, la maggior parte dei renderer usa il [back-face culling](https://en.wikipedia.org/wiki/Back-face_culling), ossia l'eliminazione delle facce posteriori. Tuttavia, quando le mesh vengono aperte con [piani di taglio](cut-planes.md), gli utenti spesso osservano il lato posteriore dei triangoli. Se questi triangoli non vengono raffigurati, il risultato non è convincente.

Per evitare in modo affidabile questo problema, è necessario eseguire il rendering di *entrambi i lati* dei triangoli. Poiché non usare il back-face culling ha implicazioni sulle prestazioni, per impostazione predefinita Rendering remoto di Azure passa al rendering di entrambi i lati solo per le mesh che intersecano un piano di taglio.

L'impostazione di *rendering a lato singolo* consente di personalizzare questo comportamento.

> [!CAUTION]
> L'impostazione di rendering a lato singolo è una funzionalità sperimentale. Potrebbe essere rimossa in futuro. Non modificare l'impostazione predefinita, a meno che non risolva un problema critico nell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

L'impostazione di rendering a lato singolo ha un effetto solo per le mesh che sono state [convertite](../../how-tos/conversion/configure-model-conversion.md) con l'opzione `opaqueMaterialDefaultSidedness` impostata su `SingleSided`. Per impostazione predefinita, questa opzione è impostata su `DoubleSided`.

## <a name="single-sided-rendering-setting"></a>Impostazione di rendering a lato singolo

Esistono tre modalità diverse:

**Normal:** in questa modalità, le mesh vengono sempre rappresentate così come sono state convertite. In altre parole, il rendering delle mesh convertite con `opaqueMaterialDefaultSidedness` impostato su `SingleSided` viene sempre eseguito con il back-face culling abilitato, anche se intersecano un piano di taglio.

**DynamicDoubleSiding:** in questa modalità, quando un piano di taglio interseca una mesh viene automaticamente impostato il rendering di entrambi i lati. Questa è la modalità predefinita.

**AlwaysDoubleSided:** forza il rendering di entrambi i lati di tutte le geometrie a lato singolo in qualsiasi momento. Questa modalità è per lo più esposta in modo che sia possibile confrontare facilmente l'effetto sulle prestazioni tra il rendering a lato singolo e quello a due lati.

La modifica delle impostazioni di rendering a lato singolo può essere eseguita come indicato di seguito:

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Piani di taglio](cut-planes.md)
* [Configurare la conversione di modelli](../../how-tos/conversion/configure-model-conversion.md)
