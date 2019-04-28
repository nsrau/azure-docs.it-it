---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232407"
---
## <a name="update-properties"></a>Aggiornare le proprietà

Per aggiornare le proprietà in un ancoraggio, si utilizza il `UpdateAnchorProperties()` (metodo). Se due o più dispositivi tenta di aggiornare le proprietà per lo stesso ancoraggio allo stesso tempo, utilizziamo un modello di concorrenza ottimistica. Il che significa che la prima scrittura "vincente".  Tutte le altre operazioni di scrittura verrà visualizzato un errore di "Concorrenza": è necessario un aggiornamento delle proprietà prima di riprovare.
