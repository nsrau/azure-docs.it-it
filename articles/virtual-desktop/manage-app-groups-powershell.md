---
title: Gestire i gruppi di app per il desktop virtuale Windows PowerShell-Azure
description: Come gestire i gruppi di app desktop virtuali Windows con PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 531c7a819bf83edff2756fe1e62859bcb8fef459
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615266"
---
# <a name="manage-app-groups-using-powershell"></a>Gestire i gruppi di app con PowerShell

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il gruppo di app predefinito creato per un nuovo pool di host di Desktop virtuale Windows pubblica anche il desktop completo. È inoltre possibile creare uno o più gruppi di applicazioni RemoteApp per il pool di host. Seguire questa esercitazione per creare un gruppo di app RemoteApp e pubblicare singole app del menu **Start** .

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di app RemoteApp.
> * Concedere l'accesso ai programmi RemoteApp.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che siano state seguite le istruzioni riportate in [configurare il modulo PowerShell](powershell-module.md) per configurare il modulo di PowerShell e accedere all'account Azure.

## <a name="create-a-remoteapp-group"></a>Creare un gruppo di app RemoteApp

Per creare un gruppo RemoteApp con PowerShell:

1. Eseguire il cmdlet di PowerShell seguente per creare un nuovo gruppo vuoto di app RemoteApp.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (Facoltativo) Per verificare se il gruppo di app è stato creato, è possibile eseguire il cmdlet seguente per visualizzare un elenco di tutto i gruppi di app per il pool di host.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Eseguire il cmdlet seguente per ottenere un elenco di app del menu **Start** nell'immagine della macchina virtuale del pool host. Prendere nota dei valori di **FilePath**, **IconPath**, **IconIndex** e di altre informazioni importanti per l'applicazione da pubblicare.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more 
   ```

   L'output dovrebbe mostrare tutte le voci del menu Start in un formato simile al seguente:

   ```powershell
   AppAlias            : access
   CommandLineArgument : 
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE 
   FriendlyName        : 
   IconIndex           : 0 
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe 
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access 
   Name                : 0301RAG/Access 
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   
   AppAlias            : charactermap 
   CommandLineArgument : 
   FilePath            : C:\windows\system32\charmap.exe 
   FriendlyName        : 
   IconIndex           : 0 
   IconPath            : C:\windows\system32\charmap.exe 
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map 
   Name                : 0301RAG/Character Map 
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems 
   ```
   
4. Eseguire il cmdlet seguente per installare l'applicazione basata su `AppAlias`. `AppAlias` diventa visibile quando si esegue l'output del passaggio 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require> 
   ```

5. (Facoltativo) Eseguire il cmdlet seguente per pubblicare un nuovo programma RemoteApp nel gruppo di applicazioni creato nel passaggio 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require> 
   ```

6. Per verificare se l'app è stata pubblicata, eseguire il cmdlet seguente.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Ripetere i passaggi da 1 a 5 per ogni applicazione da pubblicare in questo gruppo di app.
8. Eseguire il cmdlet seguente per concedere agli utenti l'accesso ai programmi RemoteApp del gruppo di app.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Passaggi successivi

Se si è arrivati a questa Guida introduttiva delle esercitazioni, vedere [creare un pool di host per convalidare gli aggiornamenti del servizio](create-validation-host-pool.md). È possibile usare un pool di host di convalida per monitorare gli aggiornamenti del servizio prima di distribuirli nell'ambiente di produzione.