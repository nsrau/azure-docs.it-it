---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179863"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Individuare un punto di ancoraggio cloud spaziali

La possibilità di individuare un punto di ancoraggio cloud caricato in precedenza spaziale è uno dei motivi principali per l'uso della libreria di Azure gli ancoraggi spaziale. Per individuare gli ancoraggi spaziali cloud, è necessario conoscere i relativi identificatori. Gli ID di ancoraggio possono essere archiviati nel servizio back-end dell'applicazione e accessibili a tutti i dispositivi che possono eseguire correttamente l'autenticazione ad esso. Per un esempio di questo, vedere [esercitazione: Condividere punti di ancoraggio spaziale tra dispositivi](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Creare un'istanza di un `AnchorLocateCriteria` dell'oggetto, impostare gli identificatori si sta cercando e richiamare il `CreateWatcher` metodo nella sessione, fornendo il `AnchorLocateCriteria`.
