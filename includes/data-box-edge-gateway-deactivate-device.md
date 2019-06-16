---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "67125631"
---
Per reimpostare il dispositivo, è necessario cancellare in modo sicuro tutti i dati sul disco di dati e il disco di avvio del dispositivo. 

Usare il `Reset-HcsAppliance` cmdlet di cancellare i dati sia i dischi dati e il disco di avvio o solo i dischi dati. Il `ClearData` e `BootDisk` commutatori consentono rispettivamente di cancellazione dati i dischi dati e il disco di avvio.

Se si usa il dispositivo reimpostato l'interfaccia utente Web locale, solo i dischi dati vengono cancellati in modo sicuro, ma il disco di avvio non è stato modificato. Il disco di avvio contiene la configurazione del dispositivo.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Al prompt dei comandi digitare:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Nell'esempio seguente viene illustrato come utilizzare questo cmdlet:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
