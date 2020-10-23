---
title: Usare Holographic Remoting e Rendering remoto in Unity
description: Come è possibile usare l'anteprima di comunicazione remota olografica in combinazione con il rendering remoto di Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201818"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Usare Holographic Remoting e Rendering remoto in Unity

La [comunicazione remota olografica](/windows/mixed-reality/holographic-remoting-player) e il rendering remoto di Azure si escludono a vicenda all'interno di un'applicazione. Di conseguenza, la [modalità di riproduzione Unity](/windows/mixed-reality/unity-play-mode) non è disponibile.

Per ogni esecuzione dell'editor di Unity è possibile usare solo uno dei due. Per usare l'altro, riavviare Unity prima di tutto.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Usare la modalità di riproduzione Unity per l'anteprima in HoloLens 2

 È comunque possibile usare la modalità di riproduzione Unity, ad esempio per testare l'interfaccia utente dell'applicazione. Tuttavia, è essenziale che ARR non venga mai inizializzato. In caso contrario, verrà arrestato in modo anomalo.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Usare un auricolare WMR VR per l'anteprima sul desktop

Se è presente una serie di cuffie VR di realtà mista di Windows, è possibile usarla per l'anteprima all'interno di Unity. In questo caso, è bene inizializzare ARR, ma non sarà possibile connettersi a una sessione mentre viene usata la cuffia WMR.