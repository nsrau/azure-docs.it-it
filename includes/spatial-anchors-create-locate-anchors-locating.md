---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76887544"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Individuare un ancoraggio nello spazio cloud

La possibilità di individuare un ancoraggio nello spazio cloud salvato in precedenza è uno dei motivi principali per usare gli ancoraggi nello spazio di Azure. Esistono diversi modi in cui è possibile individuare un ancoraggio nello spazio cloud. È possibile usare una strategia in un watcher alla volta.
- Individuare gli ancoraggi in base all'identificatore.
- Individuare gli ancoraggi connessi a un ancoraggio individuato in precedenza. Per altre informazioni sulle relazioni tra ancoraggi, vedere [qui](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Individuare un ancoraggio tramite la [rilocalizzazione grossolana](/azure/spatial-anchors/concepts/coarse-reloc/).

Se si prevede di individuare gli ancoraggi nello spazio cloud in base all'identificatore, sarà necessario archiviare l'identificatore dell'ancoraggio nello spazio cloud nel servizio back-end dell'applicazione e renderlo accessibile a tutti i dispositivi che possono autenticarsi correttamente. Per un esempio, vedere [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Creare un'istanza dell'oggetto `AnchorLocateCriteria`, impostare gli identificatori da cercare e richiamare il metodo `CreateWatcher` nella sessione specificando `AnchorLocateCriteria`.