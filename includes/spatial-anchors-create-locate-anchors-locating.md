---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907741"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Individuare un punto di ancoraggio cloud spaziali

Per individuare gli ancoraggi spaziali cloud, è necessario conoscere i relativi identificatori. Gli ID di ancoraggio possono essere archiviati nel servizio back-end dell'applicazione e accessibile a tutti i dispositivi che possono eseguire correttamente l'autenticazione ad esso. Per un esempio di questo, vedere [esercitazione: Condividere punti di ancoraggio spaziale tra dispositivi](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Creare un'istanza di un oggetto AnchorLocateCriteria, impostare gli identificatori si sta cercando e richiamare il metodo CreateWatcher nella sessione, fornendo il AnchorLocateCriteria.
