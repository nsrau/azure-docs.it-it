---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632219"
---
## <a name="update-properties"></a>Aggiornare le proprietà

Per aggiornare le proprietà in un ancoraggio, si utilizza il `UpdateAnchorProperties()` (metodo). Se due o più dispositivi tenta di aggiornare le proprietà per lo stesso ancoraggio allo stesso tempo, utilizziamo un modello di concorrenza ottimistica. Il che significa che la prima scrittura "vincente".  Tutte le altre operazioni di scrittura verrà visualizzato un errore di "Concorrenza": è necessario un aggiornamento delle proprietà prima di riprovare.
