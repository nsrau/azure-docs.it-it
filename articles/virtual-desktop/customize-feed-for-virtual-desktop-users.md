---
title: Personalizzare il feed per gli utenti di desktop virtuali Windows-Azure
description: Come personalizzare il feed per gli utenti di desktop virtuali Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: helohr
ms.openlocfilehash: 40fd863d9ad92b91b8e691fbb7be233e16b82c17
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950741"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizzare il feed per gli utenti di Desktop virtuale Windows

È possibile personalizzare il feed in modo che le risorse RemoteApp e desktop remoto vengano visualizzate in modo riconoscibile per gli utenti.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizzare il nome visualizzato per un RemoteApp

È possibile modificare il nome visualizzato per un RemoteApp pubblicato impostando il nome descrittivo. Per impostazione predefinita, il nome descrittivo corrisponde al nome del programma RemoteApp.

Per recuperare un elenco di RemoteApp pubblicati per un gruppo di app, eseguire il cmdlet di PowerShell seguente:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](media/get-rdsremoteapp.png)

Per assegnare un nome descrittivo a un RemoteApp, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Screenshot del cmdlet di PowerShell set-RDSRemoteApp con nome e nuovo FriendlyName evidenziato.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizzare il nome visualizzato per un Desktop remoto

È possibile modificare il nome visualizzato per un desktop remoto pubblicato impostando un nome descrittivo. Se è stato creato manualmente un pool di host e un gruppo di app desktop tramite PowerShell, il nome descrittivo predefinito è "sessione Desktop". Se è stato creato un pool host e un gruppo di app desktop tramite il modello di Azure Resource Manager GitHub o l'offerta di Azure Marketplace, il nome descrittivo predefinito corrisponde al nome del pool host.

Per recuperare la risorsa Desktop remoto, eseguire il cmdlet PowerShell seguente:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](media/get-rdsremotedesktop.png)

Per assegnare un nome descrittivo alla risorsa Desktop remoto, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Screenshot del cmdlet di PowerShell set-RDSRemoteApp con nome e nuovo FriendlyName evidenziato.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato personalizzato il feed per gli utenti, è possibile accedere a un client desktop virtuale di Windows per testarlo. A tale scopo, continuare con le procedure per connettersi a desktop virtuali Windows:
    
 * [Connettersi da Windows 10 o Windows 7](connect-windows-7-and-10.md)
 * [Connettersi da un Web browser](connect-web.md) 
