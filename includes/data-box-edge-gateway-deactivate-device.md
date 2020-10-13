---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79128872"
---
Per ripristinare il dispositivo, è necessario cancellare in modo sicuro tutti i dati presenti nel disco dati e nel disco di avvio del dispositivo. 

Usare il `Reset-HcsAppliance` cmdlet per eliminare i dischi dati e il disco di avvio o solo i dischi dati. Le `ClearData` `BootDisk` Opzioni e consentono di rimuovere rispettivamente i dischi dati e il disco di avvio.

Il `BootDisk` Commuter Cancella il disco di avvio e rende inutilizzabile il dispositivo. Deve essere usata solo quando è necessario restituire il dispositivo a Microsoft. Per ulteriori informazioni, vedere [la pagina relativa alla restituzione del dispositivo a Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Se si usa il ripristino del dispositivo nell'interfaccia utente Web locale, vengono cancellati in modo sicuro i dati solo nei dischi dati, mentre il disco di avvio rimane intatto. Il disco di avvio contiene la configurazione del dispositivo.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Al prompt dei comandi digitare:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Nell'esempio seguente viene illustrato come utilizzare questo cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
