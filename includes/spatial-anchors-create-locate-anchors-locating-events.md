---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232344"
---
Dopo aver creato il watcher, la `AnchorLocated` evento verrà generato per ogni ancoraggio richiesto. Questo evento viene attivata quando trova un ancoraggio o se il punto di ancoraggio non è stata trovata. Se si verifica questa situazione, il motivo sarà indicato nello stato. Dopo che vengono elaborati tutti i punti di ancoraggio per un controllo, trovato o non viene trovato, il `LocateAnchorsCompleted` viene generato l'evento.
