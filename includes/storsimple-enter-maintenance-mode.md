---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889443"
---
#### <a name="to-enter-maintenance-mode"></a>Per attivare la modalità di manutenzione
1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**.
2. Digitare la password. La password predefinita è **Password1**.
3. Al prompt dei comandi digitare
   
     `Enter-HcsMaintenanceMode`
4. Verrà visualizzato un messaggio di avviso indicante che la modalità di manutenzione interromperà tutte le richieste I/O e la connessione al portale di Azure classico e verrà richiesto di confermare che si desidera procedere. Digitare **Y** per attivare la modalità di manutenzione.
   
    Entrambi i controller verranno riavviati. Una volta completato il riavvio, verrà visualizzato un altro messaggio che indica che il dispositivo è in modalità di manutenzione.

