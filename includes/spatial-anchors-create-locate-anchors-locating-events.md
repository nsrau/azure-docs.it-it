---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179866"
---
Dopo aver creato il watcher, la `AnchorLocated` evento verrà generato per ogni ancoraggio richiesto. Questo evento viene attivata quando trova un ancoraggio o se il punto di ancoraggio non è stata trovata. Se si verifica questa situazione, il motivo sarà indicato nello stato. Dopo che vengono elaborati tutti i punti di ancoraggio per un controllo, trovato o non viene trovato, il `LocateAnchorsCompleted` viene generato l'evento.
