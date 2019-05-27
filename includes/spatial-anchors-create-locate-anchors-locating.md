---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110784"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Individuare un punto di ancoraggio cloud spaziali

La possibilità di individuare un punto di ancoraggio cloud caricato in precedenza spaziale è uno dei motivi principali per l'uso della libreria di Azure gli ancoraggi spaziale. Per individuare gli ancoraggi spaziali cloud, è necessario conoscere i relativi identificatori. Gli ID di ancoraggio possono essere archiviati nel servizio back-end dell'applicazione e accessibili a tutti i dispositivi che possono eseguire correttamente l'autenticazione ad esso. Per un esempio di questo, vedere [esercitazione: Condividere punti di ancoraggio spaziale tra dispositivi](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Creare un'istanza di un `AnchorLocateCriteria` dell'oggetto, impostare gli identificatori si sta cercando e richiamare il `CreateWatcher` metodo nella sessione, fornendo il `AnchorLocateCriteria`.
