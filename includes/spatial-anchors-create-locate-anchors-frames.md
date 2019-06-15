---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110628"
---
## <a name="provide-frames-to-the-session"></a>Specificare i frame alla sessione

La sessione di ancoraggio spaziali funziona eseguendo il mapping lo spazio intorno all'utente. In tal modo eviterai per determinare dove si trovano i punti di ancoraggio. Piattaforme per dispositivi mobili (iOS e Android) richiedono una chiamata alla fotocamera feed per ottenere i frame dalla libreria AR della piattaforma nativa. Al contrario, HoloLens è costantemente l'analisi dell'ambiente, in modo che non è necessario per una chiamata specifica, ad esempio con dispositivi mobili.