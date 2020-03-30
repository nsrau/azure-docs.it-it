---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128872"
---
Per ripristinare il dispositivo, è necessario cancellare in modo sicuro tutti i dati sul disco dati e sul disco di avvio del dispositivo. 

Utilizzare `Reset-HcsAppliance` il cmdlet per eliminare sia i dischi dati che il disco di avvio o solo i dischi dati. Gli `ClearData` `BootDisk` interruttori e consentono di cancellare rispettivamente i dischi dati e il disco di avvio.

L'interruttore `BootDisk` cancella il disco di avvio e rende il dispositivo inutilizzabile. Deve essere utilizzato solo quando il dispositivo deve essere restituito a Microsoft. Per ulteriori informazioni, vedere [Restituire il dispositivo a Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Se si utilizza la reimpostazione del dispositivo nell'interfaccia utente Web locale, solo i dischi dati vengono cancellati in modo sicuro, ma il disco di avvio viene mantenuto intatto. Il disco di avvio contiene la configurazione del dispositivo.

1. [Connettersi all'interfaccia](#connect-to-the-powershell-interface)di PowerShell .
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
