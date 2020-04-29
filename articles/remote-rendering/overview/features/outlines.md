---
title: Rendering del contorno
description: Viene illustrato come eseguire il rendering della struttura di selezione
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680830"
---
# <a name="outline-rendering"></a>Rendering del contorno

Gli oggetti selezionati possono essere evidenziati visivamente aggiungendo il rendering del contorno tramite il [componente di override dello stato gerarchico](../../overview/features/override-hierarchical-state.md). Questo capitolo illustra il modo in cui i parametri globali per il rendering della struttura vengono modificati tramite l'API client.

Le proprietà della struttura sono un'impostazione globale. Tutti gli oggetti che usano il rendering della struttura utilizzeranno la stessa impostazione. non è possibile usare un colore di contorno per oggetto.

## <a name="parameters-for-outlinesettings"></a>Parametri per`OutlineSettings`

La `OutlineSettings` classe include le impostazioni correlate alle proprietà della struttura globale. Espone i membri seguenti:

| Parametro      | Type    | Descrizione                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Colore utilizzato per disegnare la struttura. La parte alfa viene ignorata.         |
| `PulseRateHz`    | float   | Velocità con cui viene oscillato il contorno al secondo|
| `PulseIntensity` | float   | Intensità dell'effetto di impulso del contorno. Il numero deve essere compreso tra 0,0 e 1,0 per la pulsazione completa. L'intensità imposta in modo implicito l'opacità minima `MinOpacity = 1.0 - PulseIntensity`del contorno come. |

![Descrive](./media/outlines.png) l'effetto della modifica del `color` parametro da giallo (a sinistra) a Magenta (al centro) e `pulseIntensity` da 0 a 0,8 (a destra).

## <a name="example"></a>Esempio

Il codice seguente illustra un esempio per l'impostazione dei parametri della struttura tramite l'API:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Prestazioni

Il rendering del contorno può avere un impatto significativo sulle prestazioni di rendering. Questo effetto varia in base alla relazione spaziale dello spazio dello schermo tra oggetti selezionati e non selezionati per un frame specificato.

## <a name="next-steps"></a>Passaggi successivi

* [Componente di override dello stato gerarchico](../../overview/features/override-hierarchical-state.md)
