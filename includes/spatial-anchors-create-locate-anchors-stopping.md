---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006493"
---
## <a name="pause-reset-or-stop-the-session"></a>Sospendere, reimpostare o arrestare la sessione

Per arrestare temporaneamente la sessione, è possibile richiamare `Stop()`. In questo modo verranno arrestati tutti i watcher oltre all'elaborazione dell'ambiente, anche se si richiama `ProcessFrame()`. È quindi possibile richiamare `Start()` per riprendere l'elaborazione. Quando si riprende l'elaborazione, i dati dell'ambiente già acquisiti nella sessione vengono mantenuti.
