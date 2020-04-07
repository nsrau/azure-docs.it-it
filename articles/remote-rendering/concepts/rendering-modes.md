---
title: Modalità di rendering
description: Descrive le diverse modalità di rendering lato server
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681701"
---
# <a name="rendering-modes"></a>Modalità di rendering

Il rendering remoto offre due modalità principali di funzionamento, la modalità **TileBasedComposition** e la modalità **DepthBasedComposition.** Queste modalità determinano il modo in cui il carico di lavoro viene distribuito tra più GPU sul server. La modalità deve essere specificata in fase di connessione e non può essere modificata durante il runtime.

Entrambe le modalità sono dotate di vantaggi, ma anche con limitazioni di funzionalità intrinseche, in modo da scegliere la modalità più adatta è uso caso specifico.

## <a name="modes"></a>Modalità

Le due modalità sono ora discusse in modo più dettagliato.

### <a name="tilebasedcomposition-mode"></a>Modalità 'TileBasedComposition'

In modalità **TileBasedComposition,** ogni GPU interessata esegue il rendering di rettangoli specifici (riquadri) sullo schermo. La GPU principale compone l'immagine finale dai riquadri prima che venga inviata come fotogramma video al client. Di conseguenza, tutte le GPU richiedono di avere lo stesso set di risorse per il rendering, pertanto le risorse caricate devono essere inserite nella memoria di una singola GPU.

La qualità di rendering in questa modalità è leggermente migliore rispetto alla modalità **DepthBasedComposition,** poiché MSAA può lavorare sul set completo di geometria per ogni GPU. La schermata seguente mostra che l'antialiasing funziona correttamente per entrambi i bordi allo stesso modo:The following screenshot shows that antialiasing works properly for both edges with asboth:

![MSAA in TileBasedComposition](./media/service-render-mode-quality.png)

Inoltre, in questa modalità ogni parte può essere commutata in un materiale trasparente o commutata alla modalità **trasparente** tramite [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Modalità 'DepthBasedComposition'

In modalità **DepthBasedComposition,** ogni GPU interessata esegue il rendering con risoluzione a schermo intero, ma solo un sottoinsieme di file. La composizione finale dell'immagine sulla GPU principale si occupa che le parti vengano unite correttamente in base alle loro informazioni di profondità. Naturalmente, il payload di memoria viene distribuito tra le GPU, consentendo così il rendering di modelli che non rientrano nella memoria di una singola GPU.

Ogni singola GPU utilizza MSAA per l'antialiasing del contenuto locale. Tuttavia, potrebbero esserci alias intrinseci tra i bordi da GPU distinte. Questo effetto viene attenuato dalla post-elaborazione dell'immagine finale, ma la qualità MSAA è ancora peggiore rispetto alla modalità **TileBasedComposition.This** effect is mitigated by postprocessing the final image, but MSAA quality is still worse than in TileBasedComposition mode.

Gli elementi MSAA sono illustrati ![nell'immagine seguente: MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

L'antialiasing funziona correttamente tra la scultura e la tenda, perché entrambe le parti vengono renderizzate sulla stessa GPU. D'altra parte, il bordo tra tenda e parete mostra alcuni alias perché queste due parti sono composte da GPU distinte.

La limitazione più grande di questa modalità è che le parti geometriche non possono essere commutate a materiali trasparenti in modo dinamico né la modalità **trasparente** funziona per [il HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Altre funzioni di sostituzione dello stato (contorno, tinta di colore, ...) funzionano, però. Anche i materiali contrassegnati come trasparenti al momento della conversione funzionano correttamente in questa modalità.

### <a name="performance"></a>Prestazioni

Le caratteristiche delle prestazioni per entrambe le modalità variano in base al caso d'uso ed è difficile ragionare o fornire raccomandazioni generali. Se non si è vincolati dalle limitazioni di cui sopra (memoria o trasparenza / see-through), si consiglia di provare entrambe le modalità e monitorare le prestazioni utilizzando varie posizioni della fotocamera.

## <a name="setting-the-render-mode"></a>Impostazione della modalità di rendering

La modalità di rendering utilizzata in `AzureSession.ConnectToRuntime` una `ConnectToRuntimeParams`macchina virtuale di rendering remoto viene specificata durante l'oggetto .

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Sessioni](../concepts/sessions.md)
* [Componente di sostituzione dello stato gerarchico](../overview/features/override-hierarchical-state.md)
