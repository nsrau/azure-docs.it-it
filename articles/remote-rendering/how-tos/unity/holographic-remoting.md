---
title: Usare Holographic Remoting e Rendering remoto in Unity
description: Come è possibile usare l'anteprima di comunicazione remota olografica in combinazione con il rendering remoto di Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681207"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Usare Holographic Remoting e Rendering remoto in Unity

La [comunicazione remota olografica](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) e il rendering remoto di Azure si escludono a vicenda all'interno di un'applicazione. Di conseguenza, la [modalità di riproduzione Unity](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) non è disponibile.

Per ogni esecuzione dell'editor di Unity è possibile usare solo uno dei due. Per usare l'altro, riavviare Unity prima di tutto.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Usare la modalità di riproduzione Unity per l'anteprima in Hololens 2

 È comunque possibile usare la modalità di riproduzione Unity, ad esempio per testare l'interfaccia utente dell'applicazione. Tuttavia, è essenziale che ARR non venga mai inizializzato. In caso contrario, verrà arrestato in modo anomalo.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Usare un auricolare WMR VR per l'anteprima sul desktop

Se è presente una serie di cuffie VR di realtà mista di Windows, è possibile usarla per l'anteprima all'interno di Unity. In questo caso, è bene inizializzare ARR, ma non sarà possibile connettersi a una sessione mentre viene usata la cuffia WMR.
