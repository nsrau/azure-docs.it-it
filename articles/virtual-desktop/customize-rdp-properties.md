---
title: Personalizzare le proprietà RDP con PowerShell - Azure
description: Come personalizzare il protocollo RDP le proprietà per Windows Desktop virtuali con i cmdlet di PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: 21d0b45b974f4bc806b26423b7deaef96e4bf350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082651"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizzare le proprietà di Remote Desktop Protocol per un pool di host

Personalizzazione delle proprietà di Remote Desktop Protocol (RDP) del pool un host, ad esempio esperienza multimonitor e reindirizzamento audio, ti permette di offrire un'esperienza ottimale per gli utenti in base alle esigenze. È possibile personalizzare le proprietà RDP di Desktop virtuale Windows usando il **- CustomRdpProperty** parametro nel **Set-RdsHostPool** cmdlet.

Visualizzare [impostazioni del file RDP di Desktop remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) per un elenco completo delle proprietà supportate e i relativi valori predefiniti.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Aggiungere o modificare una singola proprietà RDP personalizzate

Per aggiungere o modificare una singola proprietà RDP personalizzate, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziato.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Aggiungere o modificare più proprietà RDP personalizzate

Per aggiungere o modificare più proprietà RDP personalizzate, eseguire i seguenti cmdlet di PowerShell, fornendo le proprietà RDP personalizzate come una stringa delimitata da punti e virgola:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziato.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Reimposta tutte le proprietà RDP personalizzate

È possibile reimpostare le singole proprietà RDP personalizzate sui rispettivi valori predefiniti, seguendo le istruzioni in [aggiungere o modificare una singola proprietà RDP personalizzate](#add-or-edit-a-single-custom-rdp-property), oppure è possibile reimpostare tutte le proprietà RDP personalizzate per un pool di host eseguendo le operazioni seguenti Cmdlet di PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Screenshot del cmdlet di PowerShell Get-RDSRemoteApp con nome e FriendlyName evidenziato.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato personalizzato le proprietà RDP per un pool di host specificato, è possibile accedere a un client di Desktop virtuale Windows per verificarne il funzionamento come parte di una sessione utente. A tale scopo, continuare per connettersi alla procedure dettagliate di Desktop virtuali Windows:

- [Connettersi da Windows 10 e Windows 7](connect-windows-7-and-10.md)
- [Connettersi da un Web browser](connect-web.md)
