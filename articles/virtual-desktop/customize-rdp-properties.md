---
title: Personalizzare le proprietà RDP con PowerShell-Azure
description: Come personalizzare le proprietà RDP per desktop virtuale Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
ms.openlocfilehash: 43110036c685cd17ba912766dd8ec19aa274e7c1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459528"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizzare le proprietà Remote Desktop Protocol per un pool host

La personalizzazione delle proprietà di Remote Desktop Protocol (RDP) di un pool host, ad esempio l'esperienza a più monitor e il reindirizzamento audio, ti permette di offrire un'esperienza ottimale agli utenti in base alle esigenze. È possibile personalizzare le proprietà RDP nel desktop virtuale di Windows usando il parametro **-CustomRdpProperty** nel cmdlet **set-RdsHostPool** .

Per un elenco completo delle proprietà supportate e dei relativi valori predefiniti, vedere [impostazioni del file RDP supportate](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Proprietà RDP predefinite

Per impostazione predefinita, i file RDP pubblicati contengono le proprietà seguenti:

|Proprietà di RDP | Desktop | RemoteApp |
|---|---| --- |
| Modalità multimonitor | Attivato | N/D |
| Reindirizzamento unità abilitati | Unità, appunti, stampanti, porte COM, dispositivi USB e smart card| Unità, appunti e stampanti |
| Modalità audio remoto | Riproduci localmente | Riproduci localmente |

Le proprietà personalizzate definite per il pool host sostituiranno queste impostazioni predefinite.

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

Ora che sono state personalizzate le proprietà RDP per un determinato pool di host, è possibile accedere a un client desktop virtuale di Windows per testarle come parte di una sessione utente. Queste due procedure successive indicano come connettersi a una sessione usando il client di propria scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-and-10.md)
- [Connettersi con il client Web](connect-web.md)
