---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161195"
---
Se si verificano problemi del dispositivo, è possibile creare un pacchetto di supporto dai registri di sistema. Il supporto tecnico Microsoft Usa questo pacchetto per risolvere i problemi. Seguire questi passaggi per creare un pacchetto di supporto:

1. [Connettersi all'interfaccia di PowerShell del dispositivo](#connect-to-the-powershell-interface).
2. Usare il `Get-HcsNodeSupportPackage` comando per creare un pacchetto di supporto. L'utilizzo del cmdlet è come segue:

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

    Il cmdlet raccoglie i log dal dispositivo e copia dei log in una rete specificata o una condivisione locale.

    I parametri usati sono come segue:

    - `-Path` -Specificare la rete o il percorso locale per copiare il pacchetto di supporto per. (obbligatorio)
    - `-Credential` -Specificare le credenziali per accedere al percorso protetto.
    - `-Zip` -Specificare per generare un file con estensione zip.
    - `-Include` -Specificare se includere i componenti da includere nel pacchetto di supporto. Se non specificato, `Default` presuppone.
    - `-IncludeArchived` -Specificare se includere i log archiviati nel pacchetto di supporto.
    - `-IncludePeriodicStats` -Specificare se includere log stat periodiche nel pacchetto di supporto.

    
