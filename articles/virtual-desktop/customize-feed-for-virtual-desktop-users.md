---
title: Personalizzare feed per gli utenti di Desktop virtuale Windows - Azure
description: Come personalizzare feed per gli utenti di Desktop virtuale Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 5fe2a8b8ee5870ff7986ca2d91739f82a5128882
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618953"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizzare il feed per gli utenti di Desktop virtuale Windows

Pertanto la connessione RemoteApp e risorse di desktop remote vengono visualizzati in un modo facilmente riconoscibile per gli utenti, è possibile personalizzare il feed.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizzare il nome visualizzato per RemoteApp

È possibile modificare il nome visualizzato per RemoteApp pubblicato impostando il nome descrittivo. Per impostazione predefinita, il nome descrittivo è identico al nome del programma RemoteApp.

Per recuperare un elenco di RemoteApps pubblicato per un gruppo di app, eseguire il cmdlet di PowerShell seguente:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziato.](media/get-rdsremoteapp.png)

Per assegnare un nome descrittivo a RemoteApp, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Screenshot del cmdlet di PowerShell Set-RDSRemoteApp con nome e il nuovo FriendlyName evidenziato.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizzare il nome visualizzato per un Desktop remoto

È possibile modificare il nome visualizzato per un desktop remoto pubblicato impostando un nome descrittivo. Se è stato creato manualmente un host del pool e il gruppo di app desktop tramite PowerShell, il nome predefinito è "Sessione Desktop". Se è stato creato un host del pool e il gruppo di app desktop tramite il modello di GitHub Azure Resource Manager o l'offerta di Azure Marketplace, il nome predefinito è lo stesso pool di host.

Per recuperare le risorse di desktop remoto, eseguire il cmdlet di PowerShell seguente:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziato.](media/get-rdsremotedesktop.png)

Per assegnare un nome descrittivo per le risorse di desktop remoto, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Screenshot del cmdlet di PowerShell Set-RDSRemoteApp con nome e il nuovo FriendlyName evidenziato.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato personalizzato il feed per gli utenti, è possibile accedere a un client di Desktop virtuale Windows per testarlo. A tale scopo, continuare per connettersi alla procedure dettagliate di Desktop virtuali Windows:
    
 * [Connettersi da Windows 10 o Windows 7](connect-windows-7-and-10.md)
 * [Connettersi da un Web browser](connect-web.md) 
