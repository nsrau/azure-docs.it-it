---
title: Personalizzare le proprietà RDP con PowerShell - AzureCustomize RDP Properties with PowerShell - Azure
description: Come personalizzare le proprietà RDP per Windows Virtual Desktop con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128072"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizzare le proprietà di Remote Desktop Protocol per un pool host

La personalizzazione delle proprietà RDP (Remote Desktop Protocol) di un pool host, ad esempio l'esperienza con più monitor e il reindirizzamento audio, consente di offrire agli utenti un'esperienza ottimale in base alle proprie esigenze. È possibile personalizzare le proprietà RDP in Windows Virtual Desktop utilizzando il parametro **-CustomRdpProperty** nel cmdlet **Set-RdsHostPool.**

Vedere [le impostazioni dei file RDP supportati](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) per un elenco completo delle proprietà supportate e dei relativi valori predefiniti.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Proprietà RDP predefinite

Per impostazione predefinita, i file RDP pubblicati contengono le seguenti proprietà:

|Proprietà di RDP | Desktop | RemoteApps |
|---|---| --- |
| Modalità multi-monitor | Attivato | N/D |
| Reindirizzamenti dell'unità abilitati | Unità, appunti, stampanti, porte COM, dispositivi USB e smart card| Unità, Appunti e stampanti |
| Modalità audio remota | Gioca in locale | Gioca in locale |

Tutte le proprietà personalizzate definite per il pool host eseguiranno l'override di queste impostazioni predefinite.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Aggiungere o modificare una singola proprietà RDP personalizzataAdd or edit a single custom RDP property

Per aggiungere o modificare una singola proprietà RDP personalizzata, eseguire il seguente cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Schermata del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziati.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Aggiungere o modificare più proprietà RDP personalizzate

Per aggiungere o modificare più proprietà RDP personalizzate, eseguire i cmdlet di PowerShell seguenti fornendo le proprietà RDP personalizzate come stringa delimitata da punto e virgola:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Schermata del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziati.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Reimpostare tutte le proprietà RDP personalizzate

È possibile ripristinare i valori predefiniti delle singole proprietà RDP personalizzate seguendo le istruzioni in [Aggiungere o modificare una singola proprietà RDP personalizzata](#add-or-edit-a-single-custom-rdp-property)oppure reimpostare tutte le proprietà RDP personalizzate per un pool host eseguendo il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Schermata del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziati.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver personalizzato le proprietà RDP per un determinato pool host, è possibile accedere a un client Windows Virtual Desktop per testarle come parte di una sessione utente. I prossimi due how-toti ti diranno come connetterti a una sessione utilizzando il client di tua scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-and-10.md)
- [Connettersi con il client Web](connect-web.md)
