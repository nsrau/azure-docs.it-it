---
title: Tipo di assegnazione desktop personale di Desktop virtuale Windows - Azure
description: Come configurare il tipo di assegnazione per un pool di host di desktop personali di Desktop virtuale Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158ac92a930b53e02ee81570c62711ca27dc4ae8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200393"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurare il tipo di assegnazione del pool di host di desktop personale

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile configurare il tipo di assegnazione del pool di host di desktop personali nell’ambiente Desktop virtuale Windows per soddisfare al meglio le proprie esigenze. Questo argomento illustra come configurare l'assegnazione automatica o diretta per gli utenti.

>[!NOTE]
> Le istruzioni riportate in questo articolo sono valide solo per i pool di host di desktop personali, non per i pool di host in pool, in quanto gli utenti in questi ultimi tipi di pool non sono assegnati a host di sessione specifici.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già stato scaricato e installato il modulo PowerShell per desktop virtuale di Windows. In caso contrario, seguire le istruzioni riportate in [configurare il modulo PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Configurare l’assegnazione automatica

L’assegnazione automatica è il tipo di assegnazione predefinito per i nuovi pool di host di desktop personali creati nell'ambiente Desktop virtuale Windows. L'assegnazione automatica di utenti non richiede un host di sessione specifico.

Per assegnare gli utenti automaticamente, occorre per prima cosa assegnarli al pool di host di desktop personali in modo che possano vedere il desktop nel proprio feed. Quando avvia il desktop nel proprio feed, l’utente assegnato reclama un host di sessione disponibile se non è già connesso al pool di host e completa così il processo di assegnazione.

Per configurare un pool di host per assegnare automaticamente gli utenti a macchine virtuali, eseguire il cmdlet di PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Per assegnare un utente al pool di host di desktop personali, eseguire il cmdlet di PowerShell seguente:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Configurare l'assegnazione diretta

Diversamente dall'assegnazione automatica, quando si usa l'assegnazione diretta, è necessario assegnare l'utente sia al pool di host di desktop personali sia a un host di sessione specifico perché questi possa connettersi al proprio desktop personale. Se lo si assegna solo a un pool di host senza assegnarlo a un host di sessione, l’utente non sarà in grado di accedere alle risorse.

Per configurare un pool di host per l'assegnazione diretta degli utenti agli host di sessione, eseguire il cmdlet di PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Per assegnare un utente al pool di host di desktop personali, eseguire il cmdlet di PowerShell seguente:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Per assegnare un utente a un host di sessione specifico, eseguire il cmdlet di PowerShell seguente:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il tipo di assegnazione del desktop personale, è possibile accedere a un client Desktop virtuale Windows per testarlo nell’ambito di una sessione utente. Le prossime due procedure spiegano come connettersi a una sessione usando il client di propria scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-and-10.md)
- [Connettersi con il client Web](connect-web.md)
- [Connettersi con il client Android](connect-android.md)
- [Connettersi con il client iOS](connect-ios.md)
- [Connettersi con il client macOS](connect-macos.md)