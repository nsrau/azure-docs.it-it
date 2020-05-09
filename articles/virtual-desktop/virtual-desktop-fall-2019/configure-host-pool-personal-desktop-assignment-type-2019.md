---
title: Tipo di assegnazione desktop personale desktop virtuale Windows-Azure
description: Come configurare il tipo di assegnazione per un pool host desktop personale desktop virtuale di Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2541e9e10103d66c6c2fb6978c3029d61b813eab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614967"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurare il tipo di assegnazione del pool di host personal desktop

>[!IMPORTANT]
>Questo contenuto si applica alla versione 2019, che non supporta Azure Resource Manager oggetti desktop virtuali di Windows. Se si sta tentando di gestire Azure Resource Manager oggetti desktop virtuali Windows introdotti nell'aggiornamento di Spring 2020, vedere [questo articolo](../configure-host-pool-personal-desktop-assignment-type.md).

È possibile configurare il tipo di assegnazione del pool di host desktop personali per modificare l'ambiente di desktop virtuale Windows in base alle proprie esigenze. In questo argomento verrà illustrato come configurare l'assegnazione automatica o diretta per gli utenti.

>[!NOTE]
> Le istruzioni riportate in questo articolo sono valide solo per i pool host desktop personali, non per i pool di host in pool, perché gli utenti in pool di host non sono assegnati a host di sessione specifici.

## <a name="configure-automatic-assignment"></a>Configura assegnazione automatica

Assegnazione automatica è il tipo di assegnazione predefinito per i nuovi pool host desktop personali creati nell'ambiente desktop virtuale di Windows. L'assegnazione automatica di utenti non richiede un host sessione specifico.

Per assegnare automaticamente gli utenti, assegnarli prima al pool di host desktop personali in modo che possano visualizzare il desktop nel feed. Quando un utente assegnato avvia il desktop nel proprio feed, dimostrerà un host sessione disponibile se non è già connesso al pool host, che completa il processo di assegnazione.

Prima di iniziare, [scaricare e importare il modulo PowerShell per desktop virtuale di Windows](/powershell/windows-virtual-desktop/overview/) , se non è già stato fatto. 

> [!NOTE]
> Prima di seguire queste istruzioni, verificare di aver installato Windows Virtual Desktop PowerShell Module versione 1.0.1534.2001 o successiva.

Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Per configurare un pool di host per assegnare automaticamente gli utenti alle macchine virtuali, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Per assegnare un utente al pool di host desktop personali, eseguire il cmdlet PowerShell seguente:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configurare l'assegnazione diretta

Diversamente dall'assegnazione automatica, quando si usa l'assegnazione diretta, è necessario assegnare l'utente sia al pool di host desktop personale che a un host sessione specifico prima di potersi connettere al desktop personale. Se l'utente viene assegnato a un pool di host senza assegnazione host sessione, non sarà in grado di accedere alle risorse.

Per configurare un pool host in modo da richiedere l'assegnazione diretta degli utenti agli host di sessione, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Per assegnare un utente al pool di host desktop personali, eseguire il cmdlet PowerShell seguente:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Per assegnare un utente a un host sessione specifico, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato il tipo di assegnazione desktop personale, è possibile accedere a un client desktop virtuale Windows per testarlo come parte di una sessione utente. Queste due procedure successive indicano come connettersi a una sessione usando il client di propria scelta:

- [Connettersi con il client desktop di Windows](../connect-windows-7-and-10.md)
- [Connettersi con il client Web](connect-web-2019.md)
