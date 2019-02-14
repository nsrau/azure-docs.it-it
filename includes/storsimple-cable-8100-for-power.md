---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889776"
---
#### <a name="to-cable-for-power"></a>Per cablare il dispositivo per l'alimentazione
1. Assicurarsi che gli interruttori di alimentazione di ciascuno dei moduli PCM siano in posizione OFF.
2. Collegare i cavi di alimentazione a tutti i moduli PCM nell’enclosure principale.
3. Collegare i cavi di alimentazione alle unità PDU (Power Distribution Unit) rack come illustrato nella seguente figura. Verificare che i due moduli PCM usino fonti di alimentazione separate.
   
   > [!IMPORTANT]
   > Per garantire la disponibilità elevata del sistema, si consiglia vivamente di attenersi allo schema di cablaggio dell'alimentazione illustrato nella seguente figura. 
   > 
   > 
   
    ![Cablare il dispositivo 2U per l'alimentazione](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Cablaggio di alimentazione su un dispositivo 8100**
   
   | Etichetta | DESCRIZIONE |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Controller 1 |
   | 3 |Controller 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. Accendere il sistema girando l'interruttore di alimentazione di ciascun modulo PCM su ON.

