---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180606"
---
Se si verificano problemi del dispositivo, è possibile creare un pacchetto di supporto dai log di sistema. Il supporto tecnico Microsoft utilizza questo pacchetto per risolvere i problemi. Seguire questi passaggi per creare un pacchetto di supporto:Follow these steps to create a support package:

1. [Connettersi all'interfaccia di PowerShell del dispositivo.](#connect-to-the-powershell-interface)
2. Usare `Get-HcsNodeSupportPackage` il comando per creare un pacchetto di supporto. L'utilizzo del cmdlet è il seguente:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Il cmdlet raccoglie i log dal dispositivo e copia tali registri in una rete specificata o in una condivisione locale specificata.

    I parametri utilizzati sono i seguenti:

    - `-Path`- Specificare la rete o il percorso locale in cui copiare il pacchetto di supporto. (obbligatorio)
    - `-Credential`- Specificare le credenziali per accedere al percorso protetto.
    - `-Zip`- Specificare per generare un file zip.
    - `-Include`- Specificare di includere i componenti da includere nel pacchetto di supporto. Se non `Default` specificato, viene utilizzato.
    - `-IncludeArchived`- Specificare di includere i log archiviati nel pacchetto di supporto.- Specify to include archived logs in the support package.
    - `-IncludePeriodicStats`- Specificare di includere log delle statistiche periodiche nel pacchetto di supporto.- Specify to include periodic stat logs in the support package.

    
