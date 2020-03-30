---
title: Personalizzare il feed per gli utenti di Desktop virtuale di Windows - AzureCustomize feed for Windows Virtual Desktop users - Azure
description: Come personalizzare il feed per gli utenti di Desktop virtuale di Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128092"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizzare il feed per gli utenti di Desktop virtuale Windows

È possibile personalizzare il feed in modo che le risorse RemoteApp e desktop remoto vengano visualizzate in modo riconoscibile per gli utenti.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizzare il nome visualizzato per un RemoteApp

È possibile modificare il nome visualizzato per una RemoteApp pubblicata impostando il nome descrittivo. Per impostazione predefinita, il nome descrittivo corrisponde al nome del programma RemoteApp.

Per recuperare un elenco di RemoteApps pubblicate per un gruppo di app, eseguire il cmdlet PowerShell seguente:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Schermata del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziati.](media/get-rdsremoteapp.png)

Per assegnare un nome descrittivo a una RemoteApp, eseguire il seguente cmdlet PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Schermata del cmdlet di PowerShell Set-RDSRemoteApp con nome e nuovo FriendlyName evidenziati.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizzare il nome visualizzato per un desktop remoto

È possibile modificare il nome visualizzato per un desktop remoto pubblicato impostando un nome descrittivo. Se è stato creato manualmente un pool host e un gruppo di app desktop tramite PowerShell, il nome descrittivo predefinito è "Session Desktop". Se è stato creato un pool host e un gruppo di app desktop tramite il modello GitHub Azure Resource Manager o l'offerta di Azure Marketplace, il nome descrittivo predefinito corrisponde al nome del pool host.

Per recuperare la risorsa desktop remoto, eseguire il seguente cmdlet PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Schermata del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziati.](media/get-rdsremotedesktop.png)

Per assegnare un nome descrittivo alla risorsa desktop remoto, eseguire il seguente cmdlet PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Schermata del cmdlet di PowerShell Set-RDSRemoteApp con nome e nuovo FriendlyName evidenziati.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver personalizzato il feed per gli utenti, è possibile accedere a un client Windows Virtual Desktop per testarlo. A tale scopo, passare alle modalità di gestione delle modalità di connessione a Windows Desktop virtuale:
    
 * [Connettersi da Windows 10 o Windows 7](connect-windows-7-and-10.md)
 * [Connettersi da un Web browser](connect-web.md) 
