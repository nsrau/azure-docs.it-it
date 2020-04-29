---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67180606"
---
Se si verificano problemi relativi ai dispositivi, è possibile creare un pacchetto per il supporto dai registri di sistema. Supporto tecnico Microsoft utilizza questo pacchetto per risolvere i problemi. Per creare un pacchetto per il supporto, attenersi alla procedura seguente:

1. [Connettersi all'interfaccia di PowerShell del dispositivo](#connect-to-the-powershell-interface).
2. Usare il `Get-HcsNodeSupportPackage` comando per creare un pacchetto per il supporto. L'utilizzo del cmdlet è il seguente:

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

    Il cmdlet raccoglie i log dal dispositivo e li copia in una rete o in una condivisione locale specificata.

    I parametri utilizzati sono i seguenti:

    - `-Path`-Specificare la rete o il percorso locale in cui copiare il pacchetto di supporto. (obbligatorio)
    - `-Credential`-Specificare le credenziali per accedere al percorso protetto.
    - `-Zip`-Specificare per generare un file zip.
    - `-Include`-Specificare per includere i componenti da includere nel pacchetto per il supporto. Se non specificato, `Default` viene utilizzato.
    - `-IncludeArchived`-Specificare per includere i log archiviati nel pacchetto per il supporto.
    - `-IncludePeriodicStats`-Specificare per includere i log stat periodici nel pacchetto per il supporto.

    
