---
title: Utilizzo della comunicazione remota olografica e del rendering remoto in Unity
description: Come usare l'anteprima dei servizi remoti olografici in combinazione con il rendering remoto di AzureHow Holographic Remoting preview can be used in combination with Azure Remote Rendering
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681207"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Utilizzo della comunicazione remota olografica e del rendering remoto in Unity

[I servizi remoti olografici](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) e il rendering remoto di Azure si escludono a vicenda all'interno di un'unica applicazione. Come tale, anche la [modalità di riproduzione Unity](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) non è disponibile.

Per ogni esecuzione dell'editor Unity è possibile utilizzare solo uno dei due. Per utilizzare l'altro, riavviare prima Unity.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Utilizzare la modalità di riproduzione Unity per visualizzare l'anteprima su Hololens 2

 La modalità di riproduzione Unity può comunque essere utilizzata, ad esempio per testare l'interfaccia utente dell'applicazione. Tuttavia, è fondamentale che ARR non viene mai inizializzato. Altrimenti andrà in crash.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Utilizzare un auricolare WMR VR per visualizzare l'anteprima sul desktop

Se è presente un auricolare Windows Mixed Reality VR, può essere utilizzato per visualizzare l'anteprima all'interno di Unity. In questo caso, è bene inizializzare ARR, tuttavia non sarà possibile connettersi a una sessione mentre viene utilizzato l'auricolare WMR.
