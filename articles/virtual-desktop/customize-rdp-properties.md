---
title: Personalizzare le proprietà RDP con PowerShell-Azure
description: Come personalizzare le proprietà RDP per desktop virtuale Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 8679f5dcd01a6cc660561097fc607fabdfdbba71
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607006"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizzare le proprietà Remote Desktop Protocol per un pool host

La personalizzazione delle proprietà di Remote Desktop Protocol (RDP) di un pool host, ad esempio l'esperienza a più monitor e il reindirizzamento audio, ti permette di offrire un'esperienza ottimale agli utenti in base alle esigenze. È possibile personalizzare le proprietà RDP nel desktop virtuale di Windows usando il parametro **-CustomRdpProperty** nel cmdlet **set-RdsHostPool** .

Per un elenco completo delle proprietà supportate e dei relativi valori predefiniti, vedere [Desktop remoto impostazioni del file RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) .

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Aggiungere o modificare una singola proprietà RDP personalizzata

Per aggiungere o modificare una singola proprietà RDP personalizzata, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Aggiungere o modificare più proprietà RDP personalizzate

Per aggiungere o modificare più proprietà RDP personalizzate, eseguire i cmdlet di PowerShell seguenti fornendo le proprietà RDP personalizzate come stringa con valori delimitati da punto e virgola:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Reimposta tutte le proprietà RDP personalizzate

È possibile reimpostare i valori predefiniti per le singole proprietà RDP personalizzate seguendo le istruzioni in [aggiungere o modificare una singola proprietà RDP](#add-or-edit-a-single-custom-rdp-property)personalizzata oppure è possibile reimpostare tutte le proprietà RDP personalizzate per un pool host eseguendo il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state personalizzate le proprietà RDP per un determinato pool di host, è possibile accedere a un client desktop virtuale di Windows per testarle come parte di una sessione utente. A tale scopo, continuare con le procedure per connettersi a desktop virtuali Windows:

- [Connessione da Windows 10 e Windows 7](connect-windows-7-and-10.md)
- [Connettersi da un Web browser](connect-web.md)
