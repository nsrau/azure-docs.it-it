---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128872"
---
Per ripristinare il dispositivo, è necessario cancellare in modo sicuro tutti i dati presenti nel disco dati e nel disco di avvio del dispositivo. 

Usare il cmdlet `Reset-HcsAppliance` per cancellare i dati presenti nei dischi dati e nel disco di avvio oppure solo nei dischi dati. Le opzioni `ClearData` e `BootDisk` consentono rispettivamente di cancellare i dati nei dischi dati e nel disco di avvio.

L'opzione `BootDisk` consente di cancellare i dati nel disco di avvio e di rendere inutilizzabile il dispositivo. Deve essere usata solo quando è necessario restituire il dispositivo a Microsoft. Per altre informazioni, vedere [Restituire il dispositivo a Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Se si usa il ripristino del dispositivo nell'interfaccia utente Web locale, vengono cancellati in modo sicuro i dati solo nei dischi dati, mentre il disco di avvio rimane intatto. Il disco di avvio contiene la configurazione del dispositivo.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Al prompt dei comandi digitare:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Nell'esempio seguente viene illustrato come usare questo cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
