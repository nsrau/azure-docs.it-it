---
title: Tipo di assegnazione desktop personale di Desktop virtuale Windows - Azure
description: Come configurare l'assegnazione automatica o diretta per un pool host desktop personale desktop virtuale di Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 17df23a16bec91e5c333bc760143eac250b62699
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292626"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurare il tipo di assegnazione del pool di host di desktop personale

>[!IMPORTANT]
>Questo contenuto si applica al desktop virtuale di Windows con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa desktop virtuale di Windows (classico) senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).

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

Per assegnare direttamente un utente a un host sessione nel portale di Azure:

1. Accedere al portale di Azure all'indirizzo <https://portal.azure.com>.
2. Immettere **desktop virtuale Windows** nella barra di ricerca.
3. In **Servizi**selezionare **desktop virtuale di Windows**.
4. Nella pagina desktop virtuale di Windows, passare al menu a sinistra della finestra e selezionare **pool host**.
5. Selezionare il nome del pool host che si desidera aggiornare.
6. Passare quindi al menu sul lato sinistro della finestra e selezionare **gruppi di applicazioni**.
7. Selezionare il nome del gruppo di app desktop da modificare, quindi selezionare **assegnazioni** nel menu sul lato sinistro della finestra.
8. Selezionare **+ Aggiungi**, quindi selezionare gli utenti o i gruppi di utenti in cui si vuole pubblicare il gruppo di app desktop.
9. Selezionare **assegna macchina virtuale** nella barra informazioni per assegnare un host sessione a un utente.
10. Selezionare l'host di sessione che si vuole assegnare all'utente e quindi selezionare **assegna**.
11. Selezionare l'utente a cui si desidera assegnare l'host della sessione dall'elenco degli utenti disponibili.
12. Al termine, selezionare **Seleziona**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il tipo di assegnazione del desktop personale, è possibile accedere a un client Desktop virtuale Windows per testarlo nell’ambito di una sessione utente. Le prossime due procedure spiegano come connettersi a una sessione usando il client di propria scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-10.md)
- [Connettersi con il client Web](connect-web.md)
- [Connettersi con il client Android](connect-android.md)
- [Connettersi con il client iOS](connect-ios.md)
- [Connettersi con il client macOS](connect-macos.md)