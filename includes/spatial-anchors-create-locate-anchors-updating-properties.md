---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907680"
---
## <a name="update-properties"></a>Aggiornare le proprietà

Per aggiornare le proprietà in un ancoraggio, si usa il metodo UpdateAnchorProperties. Se due o più dispositivi tenta di aggiornare le proprietà per lo stesso ancoraggio allo stesso tempo, utilizziamo un modello di concorrenza ottimistica. Il che significa che la prima scrittura "vincente".  Tutte le altre operazioni di scrittura verrà visualizzato un errore di "Concorrenza": è necessario un aggiornamento delle proprietà prima di riprovare.
