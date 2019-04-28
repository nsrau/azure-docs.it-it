---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232328"
---
## <a name="provide-frames-to-the-session"></a>Specificare i frame alla sessione

La sessione di ancoraggio spaziali funziona eseguendo il mapping lo spazio intorno all'utente. In tal modo eviterai per determinare dove si trovano i punti di ancoraggio. Piattaforme per dispositivi mobili (iOS e Android) richiedono una chiamata alla fotocamera feed per ottenere i frame dalla libreria AR della piattaforma nativa. Al contrario, HoloLens è costantemente l'analisi dell'ambiente, in modo che non è necessario per una chiamata specifica, ad esempio con dispositivi mobili.