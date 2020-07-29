---
title: Riproiezione con ritardo della fase
description: Informazioni sulla riproiezione della fase finale e su come usarla.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 8d42087008f1812bc3713456025ed3be351d0917
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022181"
---
# <a name="late-stage-reprojection"></a>Riproiezione con ritardo della fase

*Ritardo fase riproiezione* (LSR) è una funzionalità hardware che consente di stabilizzare gli ologrammi quando l'utente si sposta.

Si prevede che i modelli statici mantengano la loro posizione quando ci si sposta intorno. Se sembrano essere instabili, questo comportamento può suggerire problemi di LSR. Si tenga presente che le trasformazioni dinamiche aggiuntive, come animazioni o viste di esplosione, potrebbero mascherare questo comportamento.

È possibile scegliere tra due diverse modalità LSR, ovvero **LSR planari** o **profondità LSR**. Quello attivo è determinato dal fatto che l'applicazione client invii un buffer di profondità.

Entrambe le modalità LSR migliorano la stabilità dell'ologramma, anche se presentano limitazioni distinte. Per iniziare, provare LSR di profondità, in quanto è probabile che nella maggior parte dei casi si diano risultati migliori.

## <a name="choose-lsr-mode-in-unity"></a>Scegliere la modalità LSR in Unity

Nell'editor di Unity passare a *:::no-loc text="File > Build Settings":::* . Selezionare *:::no-loc text="Player Settings":::* in basso a sinistra, quindi controllare *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* se **:::no-loc text="Enable Depth Buffer Sharing":::** è selezionato:

![Flag di abilitazione condivisione buffer di profondità](./media/unity-depth-buffer-sharing-enabled.png)

In caso contrario, l'App utilizzerà LSR di profondità, in caso contrario utilizzerà LSR planari.

## <a name="depth-lsr"></a>Profondità LSR

Per il funzionamento del LSR di profondità, l'applicazione client deve fornire un buffer di profondità valido che contiene tutta la geometria pertinente da considerare durante LSR.

Depth LSR tenta di stabilizzare il fotogramma video in base al contenuto del buffer di profondità fornito. Di conseguenza, il contenuto di cui non è stato eseguito il rendering, ad esempio gli oggetti trasparenti, non può essere modificato da LSR e può mostrare elementi di instabilità e di riproiezione.

## <a name="planar-lsr"></a>LSR planare

Il LSR planare non dispone di informazioni di profondità per pixel, come LSR di profondità. Viene invece riproiettato tutto il contenuto in base a un piano in cui è necessario fornire ogni frame.

LSR planare riproietta gli oggetti più vicini al piano fornito. Maggiore è il numero di oggetti, più instabile sarà l'aspetto. Mentre LSR di profondità è migliore quando si riproiettano oggetti a profondità diverse, LSR planari può funzionare meglio per l'allineamento del contenuto con un piano.

### <a name="configure-planar-lsr-in-unity"></a>Configurare LSR planari in Unity

I parametri del piano sono derivati da un cosiddetto *punto di messa a fuoco*, che è necessario fornire a ogni frame `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` . Per informazioni dettagliate, vedere l' [API del punto di interesse di Unity](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) . Se non si imposta un punto di attivazione, verrà scelto un fallback. Tuttavia, il fallback automatico spesso genera risultati non ottimali.

È possibile calcolare il punto di messa a fuoco autonomamente, anche se potrebbe essere opportuno basarlo su quello calcolato dall'host di rendering remoto. Chiamare `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` per ottenere questo. Viene richiesto di fornire un frame di coordinate in cui esprimere il punto di interesse. Nella maggior parte dei casi, è sufficiente fornire il risultato da `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` qui.

In genere il client e l'host eseguono il rendering del contenuto che l'altro lato non è in grado di riconoscere, ad esempio gli elementi dell'interfaccia utente nel client. Pertanto, potrebbe essere opportuno combinare il punto focale remoto con uno calcolato localmente.

I punti di interesse calcolati in due frame successivi possono essere molto diversi. Il semplice uso di tali elementi può comportare la visualizzazione di ologrammi. Per evitare questo comportamento, è consigliabile eseguire l'interpolazione tra i punti di attivazione precedenti e quelli correnti.

## <a name="next-steps"></a>Passaggi successivi

* [Query sulle prestazioni lato server](performance-queries.md)
