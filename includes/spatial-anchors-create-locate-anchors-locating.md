---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006503"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Individuare un ancoraggio nello spazio cloud

La possibilità di individuare un ancoraggio nello spazio cloud salvato in precedenza è uno dei motivi principali per usare gli ancoraggi nello spazio di Azure. Esistono diversi modi in cui è possibile individuare un ancoraggio nello spazio cloud. È possibile usare una strategia in un watcher alla volta.
- Individuare gli ancoraggi in base all'identificatore.
- Individuare gli ancoraggi connessi a un ancoraggio individuato in precedenza. Per altre informazioni sulle relazioni tra ancoraggi, vedere [qui](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Individuare un ancoraggio tramite la [rilocalizzazione grossolana](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Ogni volta che si individua un ancoraggio, Ancoraggi nello spazio di Azure proverà a usare i dati dell'ambiente raccolti per incrementare le informazioni che visualizza. Se si verificano problemi durante l'individuazione di un ancoraggio, può essere utile creare un ancoraggio, quindi individuarlo più volte da varie angolazioni e con condizioni di illuminazione diverse.

Se si prevede di individuare gli ancoraggi nello spazio cloud in base all'identificatore, sarà necessario archiviare l'identificatore dell'ancoraggio nello spazio cloud nel servizio back-end dell'applicazione e renderlo accessibile a tutti i dispositivi che possono autenticarsi correttamente. Per un esempio, vedere [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Creare un'istanza dell'oggetto `AnchorLocateCriteria`, impostare gli identificatori da cercare e richiamare il metodo `CreateWatcher` nella sessione specificando `AnchorLocateCriteria`.