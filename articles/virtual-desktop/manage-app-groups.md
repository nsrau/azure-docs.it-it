---
title: Gestire gruppi di app per Desktop virtuale Windows - Azure
description: Descrive come configurare i tenant di Desktop virtuale Windows in Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: e158c0a6090493bec0169c144f030300de921516
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679446"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Esercitazione: Gestire gruppi di app per Desktop virtuale Windows

Il gruppo di app predefinito creato per un nuovo pool di host di Desktop virtuale Windows pubblica anche il desktop completo. È inoltre possibile creare uno o più gruppi di applicazioni RemoteApp per il pool di host. Seguire questa esercitazione per creare un gruppo di app RemoteApp e pubblicare singole app nel menu **Start**.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di app RemoteApp.
> * Concedere l'accesso ai programmi RemoteApp.

Prima di iniziare, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Creare un gruppo di app RemoteApp

1. Eseguire il cmdlet di PowerShell seguente per creare un nuovo gruppo vuoto di app RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Facoltativo) Per verificare se il gruppo di app è stato creato, è possibile eseguire il cmdlet seguente per visualizzare un elenco di tutto i gruppi di app per il pool di host.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Eseguire il cmdlet seguente per ottenere un elenco di app del menu **Start** nell'immagine di macchina virtuale del pool di host. Prendere nota dei valori di **FilePath**, **IconPath**, **IconIndex** e di altre informazioni importanti per l'applicazione da pubblicare.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Eseguire il cmdlet seguente per installare l'applicazione basata su `AppAlias`. `AppAlias` diventa visibile quando si esegue l'output del passaggio 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Facoltativo) Eseguire il cmdlet seguente per pubblicare un nuovo programma RemoteApp nel gruppo di applicazioni creato nel passaggio 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Per verificare se l'app è stata pubblicata, eseguire il cmdlet seguente.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Ripetere i passaggi da 1 a 5 per ogni applicazione da pubblicare in questo gruppo di app.
8. Eseguire il cmdlet seguente per concedere agli utenti l'accesso ai programmi RemoteApp del gruppo di app.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un gruppo di app, popolarlo con programmi RemoteApp e assegnare utenti. Per informazioni su come creare un pool di host di convalida, vedere l'esercitazione seguente. È possibile usare un pool di host di convalida per monitorare gli aggiornamenti del servizio prima di distribuirli nell'ambiente di produzione.

> [!div class="nextstepaction"]
> [Creare un pool di host per convalidare gli aggiornamenti dei servizi](./create-validation-host-pool.md)
