---
title: Personalizzare le proprietà RDP con il desktop virtuale di Windows PowerShell (versione classica)-Azure
description: Come personalizzare le proprietà RDP per desktop virtuale Windows (classico) con i cmdlet di PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1df576e3316a529ec38c4a53960ff817d129ffd8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002249"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Personalizzare le proprietà Remote Desktop Protocol per un pool di host di desktop virtuali Windows (classico)

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se occorre gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows, vedere [questo articolo](../customize-rdp-properties.md).

La personalizzazione delle proprietà di Remote Desktop Protocol (RDP) di un pool host, ad esempio l'esperienza a più monitor e il reindirizzamento audio, ti permette di offrire un'esperienza ottimale agli utenti in base alle esigenze. È possibile personalizzare le proprietà RDP nel desktop virtuale di Windows usando il parametro **-CustomRdpProperty** nel cmdlet **set-RdsHostPool** .

Per un elenco completo delle proprietà supportate e dei relativi valori predefiniti, vedere [impostazioni del file RDP supportate](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Proprietà RDP predefinite

Per impostazione predefinita, i file RDP pubblicati contengono le proprietà seguenti:

|Proprietà di RDP | Desktop | RemoteApps |
|---|---| --- |
| Modalità multimonitor | Abilitato | N/D |
| Reindirizzamento unità abilitati | Unità, appunti, stampanti, porte COM, dispositivi USB e smart card| Unità, appunti e stampanti |
| Modalità audio remoto | Riproduci localmente | Riproduci localmente |

Le proprietà personalizzate definite per il pool host sostituiranno queste impostazioni predefinite.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Aggiungere o modificare una singola proprietà RDP personalizzata

Per aggiungere o modificare una singola proprietà RDP personalizzata, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Aggiungere o modificare più proprietà RDP personalizzate

Per aggiungere o modificare più proprietà RDP personalizzate, eseguire i cmdlet di PowerShell seguenti fornendo le proprietà RDP personalizzate come stringa con valori delimitati da punto e virgola:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Reimposta tutte le proprietà RDP personalizzate

È possibile reimpostare i valori predefiniti per le singole proprietà RDP personalizzate seguendo le istruzioni in [aggiungere o modificare una singola proprietà RDP](#add-or-edit-a-single-custom-rdp-property)personalizzata oppure è possibile reimpostare tutte le proprietà RDP personalizzate per un pool host eseguendo il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con Name e FriendlyName evidenziato.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state personalizzate le proprietà RDP per un determinato pool di host, è possibile accedere a un client desktop virtuale di Windows per testarle come parte di una sessione utente. Le prossime due procedure spiegano come connettersi a una sessione usando il client di propria scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-10-2019.md)
- [Connettersi con il client Web](connect-web-2019.md)
