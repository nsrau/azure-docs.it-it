---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110805"
---
Dopo aver creato il watcher, la `AnchorLocated` evento verrà generato per ogni ancoraggio richiesto. Questo evento viene attivata quando trova un ancoraggio o se il punto di ancoraggio non è stata trovata. Se si verifica questa situazione, il motivo sarà indicato nello stato. Dopo che vengono elaborati tutti i punti di ancoraggio per un controllo, trovato o non viene trovato, il `LocateAnchorsCompleted` viene generato l'evento.
