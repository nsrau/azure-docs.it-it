---
title: Gestire gruppi di app per l'anteprima di Desktop virtuale Windows - Azure
description: Descrive come configurare i tenant dell'anteprima di Desktop virtuale Windows in Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 2baabe6837d25cce5e9f5d9e1071af8417fe5f4d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401863"
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

2. (Facoltativo) Per verificare se il gruppo di applicazioni è stato creato, è possibile eseguire il cmdlet seguente per visualizzare un elenco di tutto i gruppi di applicazioni per il pool di host.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Eseguire il cmdlet seguente per ottenere un elenco di app del menu Start nell'immagine di macchina virtuale del pool di host. Prendere nota dei valori di **FilePath**, **IconPath**, **IconIndex** e di altre informazioni importanti per l'applicazione da pubblicare.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Eseguire il cmdlet seguente per pubblicare una nuova app RemoteApp nel gruppo di applicazioni creato nel passaggio 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

5. (Facoltativo) Eseguire il cmdlet seguente per installare l'applicazione basata sul relativo alias. L'alias dell'applicazione diventa visibile quando si esegue l'output del passaggio 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -AppAlias <appalias>
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

Dopo aver creato i gruppi di app, creare entità servizio e assegnare i ruoli agli utenti. Per informazioni, vedere l'esercitazione su come creare entità servizio e assegnazioni di ruolo con PowerShell.

> [!div class="nextstepaction"]
> [Creare entità servizio e assegnazioni di ruolo con PowerShell](create-service-principal-role-powershell.md)
