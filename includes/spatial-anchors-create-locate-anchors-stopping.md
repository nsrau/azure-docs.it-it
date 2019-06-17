---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110821"
---
## <a name="pause-reset-or-stop-the-session"></a>Sospendere, reimpostare o arrestare la sessione

Per arrestare la sessione temporanea, è possibile richiamare `Stop()`. In questo modo verrà arrestata qualsiasi Watcher e l'elaborazione di ambiente, anche se si richiama ProcessFrame(). È quindi possibile richiamare `Start()` per riprendere l'elaborazione. Quando si riprende, i dati di ambiente già acquisiti nella sessione viene mantenuti.
