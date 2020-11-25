---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c1b56cfb85595b8a17dc18f69a0b162d504c04ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027977"
---
Per ripristinare il dispositivo, è necessario cancellare in modo sicuro tutti i dati presenti nel disco dati e nel disco di avvio del dispositivo. 

Usare il `Reset-HcsAppliance` cmdlet per eliminare i dischi dati e il disco di avvio o solo i dischi dati. Le `ClearData` `BootDisk` Opzioni e consentono di rimuovere rispettivamente i dischi dati e il disco di avvio.

Il `BootDisk` Commuter Cancella il disco di avvio e rende inutilizzabile il dispositivo. Deve essere usata solo quando è necessario restituire il dispositivo a Microsoft. Per ulteriori informazioni, vedere [la pagina relativa alla restituzione del dispositivo a Microsoft](../articles/databox-online/azure-stack-edge-return-device.md).

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