---
title: Tipo di assegnazione desktop personale di Windows Virtual Desktop - Azure
description: Come configurare il tipo di assegnazione per un pool di host desktop personale di Windows Virtual Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128293"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurare il tipo di assegnazione del pool host personale del desktop

È possibile configurare il tipo di assegnazione del pool host desktop personale per regolare l'ambiente di Windows Virtual Desktop in base alle proprie esigenze. In questo argomento verrà illustrato come configurare l'assegnazione automatica o diretta per gli utenti.

>[!NOTE]
> Le istruzioni contenute in questo articolo si applicano solo ai pool di host desktop personali, non ai pool host in pool, poiché gli utenti nei pool host non sono assegnati a host di sessione specifici.

## <a name="configure-automatic-assignment"></a>Configurare l'assegnazione automatica

L'assegnazione automatica è il tipo di assegnazione predefinito per i nuovi pool host desktop personali creati nell'ambiente Windows Virtual Desktop. L'assegnazione automatica degli utenti non richiede un host di sessione specifico.

Per assegnare automaticamente gli utenti, assegnarli innanzitutto al pool host desktop personale in modo che possano visualizzare il desktop nel feed. Quando un utente assegnato avvia il desktop nel feed, richiederà un host di sessione disponibile se non è già connesso al pool host, che completa il processo di assegnazione.

Prima di iniziare, [scaricare e importare il modulo PowerShell](/powershell/windows-virtual-desktop/overview/) di Windows Virtual Desktop, se non è già stato fatto. 

> [!NOTE]
> Assicurarsi di aver installato il modulo PowerShell di Windows Virtual Desktop versione 1.0.1534.2001 o successiva prima di seguire queste istruzioni.

Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Per configurare un pool host per assegnare automaticamente gli utenti alle macchine virtuali, eseguire il cmdlet PowerShell seguente:To configure a host pool to automatically assign users to VMs, run the following PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Per assegnare un utente al pool host desktop personale, eseguire il seguente cmdlet PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configurare l'assegnazione diretta

A differenza dell'assegnazione automatica, quando si utilizza l'assegnazione diretta, è necessario assegnare l'utente sia al pool host desktop personale che a un host di sessione specifico prima di potersi connettere al desktop personale. Se l'utente viene assegnato solo a un pool host senza un'assegnazione host di sessione, non sarà in grado di accedere alle risorse.

Per configurare un pool host per richiedere l'assegnazione diretta degli utenti agli host di sessione, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Per assegnare un utente al pool host desktop personale, eseguire il seguente cmdlet PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Per assegnare un utente a un host di sessione specifico, eseguire il seguente cmdlet PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il tipo di assegnazione desktop personale, è possibile accedere a un client Windows Virtual Desktop per testarlo come parte di una sessione utente. I prossimi due how-toti ti diranno come connetterti a una sessione utilizzando il client di tua scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-and-10.md)
- [Connettersi con il client Web](connect-web.md)
