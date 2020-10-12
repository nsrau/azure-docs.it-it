---
title: Rendering del contorno
description: Illustra come eseguire il rendering del contorno della selezione
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 5dd6d682c9db044763cad64eec420c1974d4ac03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613695"
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

![Un oggetto di cui è stato eseguito il rendering tre volte con parametri di struttura diversi ](./media/outlines.png) l'effetto della modifica del `color` parametro da giallo (a sinistra) a Magenta (al centro) e `pulseIntensity` da 0 a 0,8 (destra).

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
    ApiHandle<OutlineSettings> outlineSettings = session->Actions()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Prestazioni

Il rendering del contorno può avere un impatto significativo sulle prestazioni di rendering. Questo impatto varia in base alla relazione spaziale nello spazio dello schermo tra gli oggetti selezionati e quelli non selezionati per un fotogramma specifico.

## <a name="api-documentation"></a>Documentazione dell'API

* [Proprietà C# RemoteManager. OutlineSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.outlinesettings)
* [C++ RemoteManager:: OutlineSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#outlinesettings)

## <a name="next-steps"></a>Passaggi successivi

* [Componente di override dello stato gerarchico](../../overview/features/override-hierarchical-state.md)
