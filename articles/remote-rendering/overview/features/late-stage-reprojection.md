---
title: Riproiezione in fase avanzata
description: Informazioni sulla riproiezione in fase avanzata e su come utilizzarla.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680986"
---
# <a name="late-stage-reprojection"></a>Riproiezione in fase avanzata

*Il riproiezione* a stadio avanzato (LSR) è una funzionalità hardware che consente di stabilizzare gli ologrammi quando l'utente si sposta.

I modelli statici dovrebbero mantenere visivamente la loro posizione quando ci si sposta intorno a loro. Se sembrano essere instabili, questo comportamento può suggerire problemi LSR. Ricorda che altre trasformazioni dinamiche, come animazioni o visualizzazioni di esplosione, potrebbero mascherare questo comportamento.

È possibile scegliere tra due diverse modalità LSR, vale a dire **Planar LSR** o **Depth LSR**. Quale è attivo è determinato dal fatto che l'applicazione client invia un buffer di profondità.

Entrambe le modalità LSR migliorano la stabilità dell'ologramma, anche se hanno i loro limiti distinti. Iniziare provando Profondità LSR, come è probabilmente dando risultati migliori nella maggior parte dei casi.

## <a name="choose-lsr-mode-in-unity"></a>Scegliere la modalità LSR in Unity

Nell'editor Unity passare a *File > Impostazioni di compilazione*. Seleziona *Impostazioni lettore* in basso a sinistra, quindi controlla in Impostazioni del > del *giocatore > XR > Gli SDK* della realtà virtuale > Windows Mixed Reality se l'opzione Abilita **condivisione buffer profondità** è selezionata:

![Flag Condivisione buffer profondità abilitata](./media/unity-depth-buffer-sharing-enabled.png)

In caso affermativo, l'app utilizzerà Depth LSR, altrimenti utilizzerà Planar LSR.

## <a name="depth-lsr"></a>Profondità LSR

Affinché Depth LSR funzioni, l'applicazione client deve fornire un buffer di profondità valido contenente tutta la geometria pertinente da considerare durante LSR.

Profondità LSR tenta di stabilizzare il fotogramma video in base al contenuto del buffer di profondità fornito. Di conseguenza, il contenuto che non è stato sottoposto a rendering su di esso, ad esempio gli oggetti trasparenti, non può essere regolato da LSR e può mostrare artefatti di instabilità e riproiezione.

## <a name="planar-lsr"></a>Planare LSR

Planar LSR non dispone di informazioni sulla profondità per pixel, come l'LSR profondità. Riproietta invece tutto il contenuto in base a un piano che è necessario fornire ogni fotogramma.

Planar LSR riprocorre meglio quegli oggetti che si trovano vicino al piano in dotazione. Più un oggetto è lontano, più instabile sarà. Mentre Depth LSR è migliore nel riproiettare gli oggetti a diverse profondità, Planar LSR può funzionare meglio per il contenuto allineato bene con un piano.

### <a name="configure-planar-lsr-in-unity"></a>Configurare Planar LSR in Unity

I parametri del piano derivano da un cosiddetto *punto* `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`di messa a fuoco , che è necessario fornire ogni fotogramma tramite . Vedere [l'API Unity Focus Point](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) per i dettagli. Se non imposti un punto focale, verrà scelto un fallback per te. Tuttavia, il fallback automatico spesso porta a risultati non ottimali.

È possibile calcolare il punto di messa a fuoco manualmente, anche se potrebbe essere opportuno basarlo su quello calcolato dall'host di rendering remoto. Chiama `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` per ottenerlo. Viene chiesto di fornire un frame di coordinate in cui esprimere il punto di messa a fuoco. Nella maggior parte dei casi, ti basta `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` fornire il risultato da qui.

In genere sia il client che l'host eseguono il rendering del contenuto di cui l'altro lato non è a conoscenza, ad esempio gli elementi dell'interfaccia utente nel client. Pertanto, potrebbe avere senso combinare il punto di messa a fuoco remoto con uno calcolato localmente.

I punti di messa a fuoco calcolati in due fotogrammi successivi possono essere molto diversi. Semplicemente usandoli così com'è può portare a ologrammi che sembrano saltare in giro. Per evitare questo comportamento, è consigliabile eseguire l'interpolazione tra i punti di messa a fuoco precedenti e correnti.

## <a name="next-steps"></a>Passaggi successivi

* [Query sulle prestazioni lato server](performance-queries.md)
