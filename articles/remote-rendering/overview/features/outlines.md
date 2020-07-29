---
title: Rendering del contorno
description: Illustra come eseguire il rendering del contorno della selezione
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4f3889a0ba121cb9a3167c1f6ac95f0bed280539
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759014"
---
# <a name="outline-rendering"></a>Rendering del contorno

Gli oggetti selezionati possono essere evidenziati visivamente aggiungendo il rendering del contorno tramite il [componente di override dello stato gerarchico](../../overview/features/override-hierarchical-state.md). Questo capitolo illustra il modo in cui è possibile modificare i parametri globali per il rendering del contorno tramite l'API client.

Le proprietà del contorno sono un'impostazione globale. Tutti gli oggetti che usano il rendering del contorno usano la stessa impostazione. Non è possibile usare un colore di contorno per oggetto.

## <a name="parameters-for-outlinesettings"></a>Parametri per `OutlineSettings`

La classe `OutlineSettings` include le impostazioni correlate alle proprietà del contorno globali ed espone i membri seguenti:

| Parametro      | Type    | Descrizione                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Colore usato per disegnare il contorno. La parte alfa viene ignorata.         |
| `PulseRateHz`    | float   | Velocità di oscillazione del contorno al secondo|
| `PulseIntensity` | float   | Intensità dell'impulso del contorno. Il valore deve essere compreso tra 0,0 (nessun impulso) e 1,0 (impulso completo). L'intensità imposta in modo implicito l'opacità minima del contorno come `MinOpacity = 1.0 - PulseIntensity`. |

![Contorni](./media/outlines.png) Effetto della modifica del parametro `color` da giallo (a sinistra) a magenta (al centro) e di `pulseIntensity` da 0 a 0,8 (a destra).

## <a name="example"></a>Esempio

Il codice seguente illustra un esempio per l'impostazione dei parametri del contorno tramite l'API:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = *session->Actions()->OutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->Color(outlineColor);
    outlineSettings->PulseRateHz(2.0f);
    outlineSettings->PulseIntensity(0.5f);
}
```

## <a name="performance"></a>Prestazioni

Il rendering del contorno può avere un impatto significativo sulle prestazioni di rendering. Questo impatto varia in base alla relazione spaziale nello spazio dello schermo tra gli oggetti selezionati e quelli non selezionati per un fotogramma specifico.

## <a name="next-steps"></a>Passaggi successivi

* [Componente di override dello stato gerarchico](../../overview/features/override-hierarchical-state.md)
