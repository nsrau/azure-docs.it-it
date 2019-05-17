---
title: Gestire gruppi di app per l'anteprima di Desktop virtuale Windows - Azure
description: Descrive come configurare i tenant dell'anteprima di Desktop virtuale Windows in Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f0cdd28be8c6e7390aa26fdc2dfbf32ec5542c2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233916"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Esercitazione: Gestire gruppi di app per l'anteprima di Desktop virtuale Windows

Il gruppo di app predefinito creato per un nuovo pool di host dell'anteprima di Desktop virtuale Windows pubblica anche il desktop completo. È inoltre possibile creare uno o più gruppi di applicazioni RemoteApp per il pool di host. Seguire questa esercitazione per creare un gruppo di app RemoteApp e pubblicare singole app nel menu Start.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di app RemoteApp.
> * Concedere l'accesso alle app RemoteApp.

Prima di iniziare, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

## <a name="create-a-remoteapp-group"></a>Creare un gruppo di app RemoteApp

1. Eseguire il cmdlet di PowerShell seguente per creare un nuovo gruppo vuoto di app RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Facoltativo) Per verificare se il gruppo di app è stato creato, è possibile eseguire il cmdlet seguente per visualizzare un elenco di tutto i gruppi di app per il pool di host.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Eseguire il cmdlet seguente per ottenere un elenco di app del menu Start nell'immagine di macchina virtuale del pool di host. Prendere nota dei valori di **FilePath**, **IconPath**, **IconIndex** e di altre informazioni importanti per l'applicazione da pubblicare.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Eseguire il cmdlet seguente per installare l'applicazione basata sul relativo alias. L'alias dell'applicazione diventa visibile quando si esegue l'output del passaggio 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Facoltativo) Eseguire il cmdlet seguente per pubblicare una nuova app RemoteApp nel gruppo di applicazioni creato nel passaggio 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Per verificare se l'app è stata pubblicata, eseguire il cmdlet seguente.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Ripetere i passaggi da 1 a 5 per ogni applicazione da pubblicare in questo gruppo di app.
8. Eseguire il cmdlet seguente per concedere agli utenti l'accesso alle app RemoteApp del gruppo.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare gruppi di app, popolarli con RemoteApps e assegnare utenti. Per altre informazioni su come accedere a Desktop virtuale Windows, continuare con le procedure per connettersi a Desktop virtuale Windows.

- [Connettersi al client di Desktop remoto in Windows 7 e Windows 10](connect-windows-7-and-10.md)
- [Connettersi al client Web dell'anteprima di Desktop virtuale Windows](connect-web.md)
