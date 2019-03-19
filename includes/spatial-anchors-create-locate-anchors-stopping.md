---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907704"
---
## <a name="pause-reset-or-stop-the-session"></a>Sospendere, reimpostare o arrestare la sessione

Per arrestare la sessione temporanea, è possibile richiamare Stop (). In questo modo verrà arrestata qualsiasi Watcher e l'elaborazione di ambiente, anche se si richiama ProcessFrame(). È quindi possibile richiamare Start () per riprendere l'elaborazione. Quando si riprende, i dati di ambiente già acquisiti nella sessione viene mantenuti.
