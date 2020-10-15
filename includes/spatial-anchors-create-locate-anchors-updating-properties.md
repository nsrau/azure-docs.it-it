---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67179860"
---
## <a name="update-properties"></a>Aggiornare le proprietà

Per aggiornare le proprietà di un ancoraggio, si usa il metodo `UpdateAnchorProperties()`. Se due o più dispositivi provano ad aggiornare le proprietà per lo stesso ancoraggio nello stesso momento, viene usato un modello di concorrenza ottimistica, in base al quale prevale la prima operazione di scrittura eseguita.  Per tutte le altre operazioni di scrittura verrà restituito un errore di "concorrenza" e sarà necessario aggiornare le proprietà prima di riprovare.
