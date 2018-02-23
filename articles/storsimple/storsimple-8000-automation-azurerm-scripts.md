---
title: Usare gli script di Azure Resource Manager per gestire i dispositivi StorSimple | Microsoft Docs
description: Informazioni su come usare gli script di Azure Resource Manager per automatizzare i processi di StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: f4456200d6f497a87424f12a23034dbff00c75aa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Usare gli script basati su Azure Resource Manager SDK per gestire i dispositivi StorSimple

Questo articolo descrive come usare gli script basati su Azure Resource Manager SDK per gestire il dispositivo StorSimple serie 8000. È incluso anche uno script di esempio per illustrare i passaggi per configurare l'ambiente per l'esecuzione di questi script.

Questo articolo si applica ai dispositivi StorSimple serie 8000 in esecuzione solo nel portale di Azure.

## <a name="sample-scripts"></a>Script di esempio

Gli script di esempio seguenti sono disponibili per l'automazione di diversi processi di StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabella degli script di esempio basati su Azure Resource Manager SDK

| Script di Azure Resource Manager                    | DESCRIZIONE                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Questo script consente di autorizzare il dispositivo StorSimple a modificare la chiave di crittografia dei dati del servizio.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Questo script crea un'appliance cloud StorSimple 8010 o 8020. L'appliance cloud può essere quindi configurata e registrata con il servizio Gestore dati StorSimple.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Questo script crea o modifica i volumi di StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Questo script elenca tutti i backup per un dispositivo registrato con il servizio Gestione dispositivi StorSimple.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Questo script ottiene tutti i criteri di backup per il dispositivo StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Questo script ottiene tutti i processi di StorSimple in esecuzione nel servizio Gestione dispositivi StorSimple.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Questo script esegue l'analisi del server di aggiornamento e consente di verificare se sono disponibili aggiornamenti da installare nel dispositivo StorSimple.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Questo script installa gli aggiornamenti disponibili nel dispositivo StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Questo script avvia uno snapshot cloud manuale ed elimina gli snapshot cloud antecedenti rispetto ai giorni di conservazione specificati.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Questo script di PowerShell di runbook di Automazione di Azure riporta lo stato di tutti i processi di backup.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Questo script elimina un singolo oggetto di backup.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Questo script avvia un backup manuale nel dispositivo StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Questo script aggiorna la chiave di crittografia dei dati del servizio per tutte le appliance cloud StorSimple 8010/8020 registrate con il servizio Gestione dispositivi StorSimple.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Questo script evidenzia i backup mancanti dopo avere analizzato tutte le pianificazioni associate ai criteri di backup. Verifica inoltre il catalogo di backup con l'elenco dei backup disponibili.             |




## <a name="configure-and-run-a-sample-script"></a>Configurare ed eseguire uno script di esempio

Questa sezione prende in considerazione uno script di esempio e analizza in dettaglio i vari passaggi necessari per eseguire lo script.

### <a name="prerequisites"></a>prerequisiti

Prima di iniziare, assicurarsi di disporre di:

*   Azure PowerShell installato. Per installare i moduli di Azure PowerShell:
    * In un ambiente Windows seguire i passaggi descritti in [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) (Installare e configurare Azure PowerShell). È possibile installare Azure PowerShell nell'host Windows Server per StorSimple, se disponibile.
    * In un ambiente Linux o MacOS seguire i passaggi descritti in [Install and configure Azure PowerShell on MacOS or Linux](https://docs.microsoft.com/powershell/azure/install-azurermps-maclinux?view=azurermps-4.4.0) (Installare e configurare Azure PowerShell in MacOS o Linux).

Per altre informazioni sull'uso di Azure PowerShell, vedere [Get started with using Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.4.0) (Iniziare a usare Azure PowerShell).

### <a name="run-azure-powershell-script"></a>Eseguire lo script di Azure PowerShell

Lo script usato in questo esempio elenca tutti i processi in un dispositivo StorSimple, inclusi i processi che hanno avuto esito positivo, che non sono riusciti o che sono in corso. Eseguire la procedura seguente per scaricare ed eseguire lo script.

1. Avviare Azure PowerShell. Creare una nuova cartella e cambiare directory impostandola sulla nuova cartella.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Scaricare l'interfaccia della riga di comando NuGet](http://www.nuget.org/downloads) nella cartella creata nel passaggio precedente. Esistono varie versioni di _nuget.exe_. Scegliere la versione corrispondente all'SDK in uso. Ogni collegamento per il download punta direttamente a un file con estensione _exe_. Assicurarsi di fare clic con il pulsante destro del mouse e di salvare il file nel computer anziché eseguirlo dal browser.

    È anche possibile eseguire il comando seguente per scaricare e archiviare lo script nella stessa cartella creata in precedenza.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Scaricare l'SDK dipendente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Scaricare lo script dal progetto GitHub di esempio.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Eseguire lo script. Quando viene richiesta l'autenticazione, specificare le credenziali di Azure. Questo script dovrebbe restituire un elenco filtrato di tutti i processi nel dispositivo StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Output di esempio

L'output seguente viene visualizzato quando viene eseguito lo script di esempio. L'output contiene tutti i processi eseguiti in un dispositivo registrato avviati il 25 settembre 2017 e completati entro il 2 ottobre 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Passaggi successivi

[Usare il servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).