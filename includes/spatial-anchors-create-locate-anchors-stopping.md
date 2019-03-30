---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632440"
---
## <a name="pause-reset-or-stop-the-session"></a>Sospendere, reimpostare o arrestare la sessione

Per arrestare la sessione temporanea, è possibile richiamare `Stop()`. In questo modo verrà arrestata qualsiasi Watcher e l'elaborazione di ambiente, anche se si richiama ProcessFrame(). È quindi possibile richiamare `Start()` per riprendere l'elaborazione. Quando si riprende, i dati di ambiente già acquisiti nella sessione viene mantenuti.
