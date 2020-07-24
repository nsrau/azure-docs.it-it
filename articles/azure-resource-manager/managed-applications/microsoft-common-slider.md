---
title: Elemento Slider UI
description: Descrive l'elemento Microsoft. Common. Slider dell'interfaccia utente per portale di Azure. Consente agli utenti di impostare un valore da un intervallo di opzioni.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098537"
---
# <a name="microsoftcommonslider-ui-element"></a>Elemento Microsoft. Common. Slider dell'interfaccia utente

Il controllo dispositivo di scorrimento consente agli utenti di selezionare un intervallo di valori consentiti.

## <a name="ui-sample"></a>Esempio di interfaccia utente

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft. Common. Slider":::

## <a name="schema"></a>SCHEMA

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
26
```

## <a name="remarks"></a>Osservazioni

- I `min` `max` valori e sono obbligatori. Impostano i punti di inizio e di fine per il dispositivo di scorrimento.
- `showStepMarkers`Per impostazione predefinita, la proprietà è impostata su true. I marcatori di passaggio vengono visualizzati solo quando l'intervallo compreso tra min e Max è 100 o meno.


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
