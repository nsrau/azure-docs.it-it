---
title: Tipo di assegnazione desktop personale desktop virtuale Windows-Azure
description: Come configurare il tipo di assegnazione per un pool host desktop personale desktop virtuale di Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8451dc14a7ed42aa92f9adbd5ad050936949e302
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612419"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurare il tipo di assegnazione del pool di host personal desktop

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile configurare il tipo di assegnazione del pool di host desktop personali per modificare l'ambiente di desktop virtuale Windows in base alle proprie esigenze. In questo argomento verrà illustrato come configurare l'assegnazione automatica o diretta per gli utenti.

>[!NOTE]
> Le istruzioni riportate in questo articolo sono valide solo per i pool host desktop personali, non per i pool di host in pool, perché gli utenti in pool di host non sono assegnati a host di sessione specifici.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già stato scaricato e installato il modulo PowerShell per desktop virtuale di Windows. In caso contrario, seguire le istruzioni riportate in [configurare il modulo PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Configura assegnazione automatica

Assegnazione automatica è il tipo di assegnazione predefinito per i nuovi pool host desktop personali creati nell'ambiente desktop virtuale di Windows. L'assegnazione automatica di utenti non richiede un host sessione specifico.

Per assegnare automaticamente gli utenti, assegnarli prima al pool di host desktop personali in modo che possano visualizzare il desktop nel feed. Quando un utente assegnato avvia il desktop nel proprio feed, dimostrerà un host sessione disponibile se non è già connesso al pool host, che completa il processo di assegnazione.

Per configurare un pool di host per assegnare automaticamente gli utenti alle macchine virtuali, eseguire il cmdlet di PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Per assegnare un utente al pool di host desktop personali, eseguire il cmdlet PowerShell seguente:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Configurare l'assegnazione diretta

Diversamente dall'assegnazione automatica, quando si usa l'assegnazione diretta, è necessario assegnare l'utente sia al pool di host desktop personale che a un host sessione specifico prima di potersi connettere al desktop personale. Se l'utente viene assegnato a un pool di host senza assegnazione host sessione, non sarà in grado di accedere alle risorse.

Per configurare un pool host in modo da richiedere l'assegnazione diretta degli utenti agli host di sessione, eseguire il cmdlet PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Per assegnare un utente al pool di host desktop personali, eseguire il cmdlet PowerShell seguente:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Per assegnare un utente a un host sessione specifico, eseguire il cmdlet PowerShell seguente:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato il tipo di assegnazione desktop personale, è possibile accedere a un client desktop virtuale Windows per testarlo come parte di una sessione utente. Queste due procedure successive indicano come connettersi a una sessione usando il client di propria scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-and-10.md)
- [Connettersi con il client Web](connect-web.md)
- [Connettersi con il client Android](connect-android.md)
- [Connettersi con il client iOS](connect-ios.md)
- [Connettersi con il client macOS](connect-macos.md)