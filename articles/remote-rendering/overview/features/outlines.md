---
title: Rendering della struttura
description: Spiega come eseguire il rendering del contorno di selezione
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680830"
---
# <a name="outline-rendering"></a>Rendering della struttura

Gli oggetti selezionati possono essere evidenziati visivamente aggiungendo il rendering del contorno tramite il [componente di sostituzione dello stato gerarchico](../../overview/features/override-hierarchical-state.md). Questo capitolo spiega come vengono modificati i parametri globali per il rendering della struttura tramite l'API client.

Le proprietà della struttura sono un'impostazione globale. Tutti gli oggetti che utilizzano il rendering del contorno utilizzeranno la stessa impostazione: non è possibile utilizzare un colore del contorno per oggetto.

## <a name="parameters-for-outlinesettings"></a>Parametri per`OutlineSettings`

Classe `OutlineSettings` contiene le impostazioni relative alle proprietà della struttura globale. Espone i seguenti membri:

| Parametro      | Type    | Descrizione                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Colore utilizzato per disegnare il contorno. La parte alfa viene ignorata.         |
| `PulseRateHz`    | float   | Velocità con cui il contorno oscilla al secondo|
| `PulseIntensity` | float   | L'intensità dell'effetto impulso del contorno. Deve essere compreso tra 0,0 per nessuna pulsazione e 1,0 per il pulsare completo. Intensità imposta implicitamente l'opacità `MinOpacity = 1.0 - PulseIntensity`minima del contorno come . |

![Contorni](./media/outlines.png) L'effetto `color` della modifica del parametro da giallo (a sinistra) a magenta (al centro) e `pulseIntensity` da 0 a 0,8 (a destra).

## <a name="example"></a>Esempio

The following code shows an example for setting outline parameters via the API:

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

Il rendering della struttura può avere un impatto significativo sulle prestazioni di rendering. Questo impatto varia in base alla relazione spaziale dello spazio dello schermo tra gli oggetti selezionati e non selezionati per un determinato fotogramma.

## <a name="next-steps"></a>Passaggi successivi

* [Componente di sostituzione dello stato gerarchico](../../overview/features/override-hierarchical-state.md)
