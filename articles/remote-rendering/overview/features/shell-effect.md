---
title: Rendering della shell
description: Spiega come usare l'effetto di rendering della shell
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f59c4f8225d31b61df08f30863c8b9300e20e820
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447709"
---
# <a name="shell-rendering"></a>Rendering della shell

Lo stato della shell del [componente di override dello stato gerarchico](../../overview/features/override-hierarchical-state.md) è un effetto di trasparenza. Diversamente dal rendering di [See-through](../../overview/features/override-hierarchical-state.md) , è visibile solo il livello di primo livello degli oggetti, analogamente al rendering opaco. Inoltre, è possibile modificare l'aspetto normale degli oggetti quando ne viene eseguito il rendering come Shell. L'effetto è concepito per i casi d'uso in cui l'utente deve essere guidato visivamente da parti non importanti, mantenendo al tempo stesso la consapevolezza spaziale per l'intera scena.

È possibile configurare l'aspetto degli oggetti con rendering della shell tramite lo `ShellRenderingSettings` stato globale. Tutti gli oggetti che usano il rendering della shell utilizzeranno la stessa impostazione. Nessun parametro per oggetto.

## <a name="shellrenderingsettings-parameters"></a>Parametri ShellRenderingSettings

`ShellRenderingSettings`La classe include le impostazioni relative alle proprietà di rendering della shell globale:

| Parametro      | Tipo    | Descrizione                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | Quantità di desaturazione da applicare al normale colore dell'oggetto finale, nell'intervallo 0 (nessuna desaturazione) a 1 (desaturazione completa) |
| `Opacity`      | float   | Opacità degli oggetti sottoposti a rendering della shell, nell'intervallo da 0 (invisibile) a 1 (completamente opaco) |

Vedere anche la tabella seguente per esempi di effetti sui parametri applicati a una scena intera:

|                | 0 | 0,25 | 0.5 | 0,75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desaturazione** | ![Desaturazione-0,0](./media/shell-desaturation-00.png) | ![Desaturazione-0,25](./media/shell-desaturation-025.png) | ![Desaturazione-0,5](./media/shell-desaturation-05.png) | ![Desaturazione-0,75](./media/shell-desaturation-075.png) | ![Desaturazione-1,0](./media/shell-desaturation-10.png) |
| **Opacità**      | ![Opacità-0,0](./media/shell-opacity-00.png) | ![Opacità-0,25](./media/shell-opacity-025.png) | ![Opacità-0,5](./media/shell-opacity-05.png) | ![Opacità-0,75](./media/shell-opacity-075.png) | ![Opacità-1,0](./media/shell-opacity-10.png) |

L'effetto Shell viene applicato al colore opaco finale in cui verrebbe eseguito il rendering della scena. Che include l' [override dello stato gerarchico della tinta](../../overview/features/override-hierarchical-state.md).

## <a name="example"></a>Esempio

Il codice seguente mostra un esempio di utilizzo dello `ShellRenderingSettings` stato tramite l'API:

```cs
void SetShellSettings(AzureSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Actions.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<AzureSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Actions()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Prestazioni

La funzionalità di rendering della shell comporta un sovraccarico costante ridotto rispetto al rendering opaco standard. È molto più veloce rispetto all'uso di materiali Transparent sugli oggetti o sul rendering [See-through](../../overview/features/override-hierarchical-state.md) . Le prestazioni possono peggiorare più fortemente se solo parti della scena vengono passate al rendering della shell. Questa riduzione può verificarsi a causa di oggetti rivelati aggiuntivi che richiedono il rendering. In tal senso, le prestazioni si comportano in modo analogo alla funzionalità [taglia piani](../../overview/features/cut-planes.md) .

## <a name="next-steps"></a>Passaggi successivi

* [Componente di override dello stato gerarchico](../../overview/features/override-hierarchical-state.md)