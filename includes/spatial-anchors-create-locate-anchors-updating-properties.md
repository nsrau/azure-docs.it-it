---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110573"
---
## <a name="update-properties"></a>Aggiornare le proprietà

Per aggiornare le proprietà in un ancoraggio, si utilizza il `UpdateAnchorProperties()` (metodo). Se due o più dispositivi tenta di aggiornare le proprietà per lo stesso ancoraggio allo stesso tempo, utilizziamo un modello di concorrenza ottimistica. Il che significa che la prima scrittura "vincente".  Tutte le altre operazioni di scrittura verrà visualizzato un errore di "Concorrenza": è necessario un aggiornamento delle proprietà prima di riprovare.
