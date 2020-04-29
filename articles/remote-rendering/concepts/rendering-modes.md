---
title: Modalità di rendering
description: Descrive le diverse modalità di rendering lato server
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681701"
---
# <a name="rendering-modes"></a>Modalità di rendering

Il rendering remoto offre due modalità operative principali, modalità **TileBasedComposition** e modalità **DepthBasedComposition** . Queste modalità determinano il modo in cui il carico di lavoro viene distribuito tra più GPU sul server. La modalità deve essere specificata al momento della connessione e non può essere modificata in fase di esecuzione.

Entrambe le modalità sono caratterizzate da vantaggi ma anche da limitazioni di funzionalità intrinseche, quindi la scelta della modalità più idonea è specifica del caso d'uso.

## <a name="modes"></a>Modalità

Le due modalità sono ora descritte in modo più dettagliato.

### <a name="tilebasedcomposition-mode"></a>Modalità' TileBasedComposition '

In modalità **TileBasedComposition** , tutte le GPU incluse eseguono il rendering di sottorettangoli specifici (riquadri) sullo schermo. La GPU principale compone l'immagine finale dai riquadri prima che venga inviata come frame video al client. Di conseguenza, tutte le GPU richiedono lo stesso set di risorse per il rendering, quindi le risorse caricate devono rientrare nella memoria di una singola GPU.

La qualità di rendering in questa modalità è leggermente migliore rispetto alla modalità **DepthBasedComposition** , poiché MSAA può lavorare sul set completo di geometria per ogni GPU. Lo screenshot seguente mostra che l'anti-aliasing funziona correttamente per entrambi i bordi:

![MSAA in TileBasedComposition](./media/service-render-mode-quality.png)

Inoltre, in questa modalità ogni parte può essere impostata su un materiale trasparente o passare alla modalità di **visualizzazione** tramite [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Modalità' DepthBasedComposition '

In modalità **DepthBasedComposition** , tutti i rendering GPU coinvolgono la risoluzione a schermo intero, ma solo un subset di mesh. La composizione dell'immagine finale sulla GPU principale si occupa della corretta Unione delle parti in base alle informazioni di profondità. Naturalmente, il payload di memoria viene distribuito tra le GPU, consentendo così di eseguire il rendering dei modelli che non rientrano nella memoria di una singola GPU.

Ogni singola GPU USA MSAA per antialias il contenuto locale. Tuttavia, potrebbe esserci un alias intrinseco tra i bordi da GPU distinte. Questo effetto viene mitigato dalla postelaborazione dell'immagine finale, ma la qualità MSAA è ancora peggiore rispetto alla modalità **TileBasedComposition** .

Gli artefatti MSAA sono illustrati nell'immagine seguente ![: MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

L'anti-aliasing funziona correttamente tra la scultura e la Cortina, perché entrambe le parti vengono sottoposte a rendering nella stessa GPU. D'altra parte, il bordo tra Curtain e Wall Mostra alcuni alias, perché queste due parti sono costituite da GPU distinte.

La limitazione principale di questa modalità è che non è possibile passare in modo dinamico le parti Geometry ai materiali trasparenti né eseguire la modalità di **visualizzazione** per [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Tuttavia, le altre funzionalità di sostituzione dello stato (struttura, tinta,...) funzionano. Anche i materiali contrassegnati come trasparenti al momento della conversione funzionano correttamente in questa modalità.

### <a name="performance"></a>Prestazioni

Le caratteristiche delle prestazioni per entrambe le modalità variano in base al caso d'uso ed è difficile ragionare o fornire indicazioni generali. Se non si è vincolati dalle limitazioni indicate in precedenza (memoria o trasparenza/visualizzazione), è consigliabile provare entrambe le modalità e monitorare le prestazioni usando varie posizioni della fotocamera.

## <a name="setting-the-render-mode"></a>Impostazione della modalità di rendering

La modalità di rendering utilizzata in una macchina virtuale per il rendering `AzureSession.ConnectToRuntime` remoto viene `ConnectToRuntimeParams`specificata durante tramite il.

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
* [Componente di override dello stato gerarchico](../overview/features/override-hierarchical-state.md)
