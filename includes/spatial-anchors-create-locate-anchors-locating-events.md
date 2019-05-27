---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110805"
---
Dopo aver creato il watcher, la `AnchorLocated` evento verrà generato per ogni ancoraggio richiesto. Questo evento viene attivata quando trova un ancoraggio o se il punto di ancoraggio non è stata trovata. Se si verifica questa situazione, il motivo sarà indicato nello stato. Dopo che vengono elaborati tutti i punti di ancoraggio per un controllo, trovato o non viene trovato, il `LocateAnchorsCompleted` viene generato l'evento.
