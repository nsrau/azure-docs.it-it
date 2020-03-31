---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67179868"
---
## <a name="pause-reset-or-stop-the-session"></a>Sospendere, reimpostare o arrestare la sessione

Per arrestare temporaneamente la sessione, è possibile richiamare `Stop()`. In questo modo verranno arrestati tutti i watcher oltre all'elaborazione dell'ambiente, anche se si richiama ProcessFrame (). È quindi possibile richiamare `Start()` per riprendere l'elaborazione. Quando si riprende l'elaborazione, i dati dell'ambiente già acquisiti nella sessione vengono mantenuti.
