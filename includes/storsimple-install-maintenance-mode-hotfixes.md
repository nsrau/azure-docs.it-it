---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67180177"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Per installare gli aggiornamenti rapidi in modalità di manutenzione tramite Windows PowerShell per StorSimple
> [!IMPORTANT]
> In modalità di manutenzione, è necessario applicare l’aggiornamento rapido prima in un controller e quindi nell’altro controller.
> 
> 

1. Attivare la modalità di manutenzione per il dispositivo. Per istruzioni su come attivare la modalità di manutenzione, vedere [Passaggio 2: Attivare la modalità di manutenzione](../articles/storsimple/storsimple-update-device.md#step2).
2. Per applicare l’aggiornamento rapido, digitare:
   
     `Start-HcsHotfix` 
3. Quando richiesto, specificare il percorso della cartella condivisa di rete che contiene i file dell'aggiornamento rapido.
4. Verrà richiesto di confermare. Digitare **S** per procedere all'installazione degli hotfix.
5. Dopo aver applicato l'aggiornamento rapido in un controller, accedere all'altro controller. Applicare l'aggiornamento rapido come è stato fatto per il precedente controller.
6. Dopo aver applicato gli aggiornamenti rapidi, uscire dalla modalità di manutenzione. Per istruzioni, vedere [Passaggio 4: Uscire dalla modalità di manutenzione](../articles/storsimple/storsimple-update-device.md#step4).

