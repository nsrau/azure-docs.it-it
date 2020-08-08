---
title: Personalizzare il feed per gli utenti di desktop virtuali Windows (classico)-Azure
description: Come personalizzare i feed per gli utenti di desktop virtuali Windows (classico) con i cmdlet di PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a8c1a5da4f4d46d98e01eeb0bf210567af19a95
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007234"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>Personalizzare il feed per gli utenti di desktop virtuali Windows (classico)

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se occorre gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows, vedere [questo articolo](../customize-feed-for-virtual-desktop-users.md).

È possibile personalizzare il feed in modo che le risorse RemoteApp e desktop remoto vengano visualizzate in modo riconoscibile per gli utenti.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizzare il nome visualizzato per un RemoteApp

È possibile modificare il nome visualizzato per un RemoteApp pubblicato impostando il nome descrittivo. Per impostazione predefinita, il nome descrittivo corrisponde al nome del programma RemoteApp.

Per recuperare un elenco di RemoteApp pubblicati per un gruppo di app, eseguire il cmdlet di PowerShell seguente:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](../media/get-rdsremoteapp.png)

Per assegnare un nome descrittivo a un RemoteApp, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Screenshot del cmdlet di PowerShell set-RDSRemoteApp con nome e nuovo FriendlyName evidenziato.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizzare il nome visualizzato per un Desktop remoto

È possibile modificare il nome visualizzato per un desktop remoto pubblicato impostando un nome descrittivo. Se è stato creato manualmente un pool di host e un gruppo di app desktop tramite PowerShell, il nome descrittivo predefinito è "sessione Desktop". Se è stato creato un pool host e un gruppo di app desktop tramite il modello di Azure Resource Manager GitHub o l'offerta di Azure Marketplace, il nome descrittivo predefinito corrisponde al nome del pool host.

Per recuperare la risorsa Desktop remoto, eseguire il cmdlet PowerShell seguente:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](../media/get-rdsremotedesktop.png)

Per assegnare un nome descrittivo alla risorsa Desktop remoto, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Screenshot del cmdlet di PowerShell set-RDSRemoteApp con nome e nuovo FriendlyName evidenziato.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato personalizzato il feed per gli utenti, è possibile accedere a un client desktop virtuale di Windows per testarlo. A tale scopo, continuare con le procedure per connettersi a desktop virtuali Windows:

 * [Connettersi da Windows 10 o Windows 7](connect-windows-7-10-2019.md)
 * [Connettersi da un Web browser](connect-web-2019.md)
