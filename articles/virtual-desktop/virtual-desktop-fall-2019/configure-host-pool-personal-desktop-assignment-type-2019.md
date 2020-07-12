---
title: Tipo di assegnazione desktop personale di Desktop virtuale Windows - Azure
description: Come configurare il tipo di assegnazione per un pool di host di desktop personali di Desktop virtuale Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6f4ee0a6ba028a6359a83f5130aca7c6f05ba08b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259792"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurare il tipo di assegnazione del pool di host di desktop personale

>[!IMPORTANT]
>Questo contenuto si applica alla versione Autunno 2019 che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si sta tentando di gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows introdotti nell'aggiornamento di Primavera 2020, vedere [questo articolo](../configure-host-pool-personal-desktop-assignment-type.md).

È possibile configurare il tipo di assegnazione del pool di host di desktop personali nell’ambiente Desktop virtuale Windows per soddisfare al meglio le proprie esigenze. Questo argomento illustra come configurare l'assegnazione automatica o diretta per gli utenti.

>[!NOTE]
> Le istruzioni riportate in questo articolo sono valide solo per i pool di host di desktop personali, non per i pool di host in pool, in quanto gli utenti in questi ultimi tipi di pool non sono assegnati a host di sessione specifici.

## <a name="configure-automatic-assignment"></a>Configurare l’assegnazione automatica

L’assegnazione automatica è il tipo di assegnazione predefinito per i nuovi pool di host di desktop personali creati nell'ambiente Desktop virtuale Windows. L'assegnazione automatica di utenti non richiede un host di sessione specifico.

Per assegnare gli utenti automaticamente, occorre per prima cosa assegnarli al pool di host di desktop personali in modo che possano vedere il desktop nel proprio feed. Quando avvia il desktop nel proprio feed, l’utente assegnato reclama un host di sessione disponibile se non è già connesso al pool di host e completa così il processo di assegnazione.

Prima di iniziare, [scaricare e importare il modulo PowerShell di Desktop virtuale Windows](/powershell/windows-virtual-desktop/overview/), se non è già stato fatto.

> [!NOTE]
> Prima di seguire queste istruzioni, verificare di aver installato il modulo PowerShell di Desktop virtuale Windows versione 1.0.1534.2001 o successiva.

Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Per configurare un pool di host per assegnare automaticamente gli utenti a macchine virtuali, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Per assegnare un utente al pool di host di desktop personali, eseguire il cmdlet di PowerShell seguente:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configurare l'assegnazione diretta

Diversamente dall'assegnazione automatica, quando si usa l'assegnazione diretta, è necessario assegnare l'utente sia al pool di host di desktop personali sia a un host di sessione specifico perché questi possa connettersi al proprio desktop personale. Se lo si assegna solo a un pool di host senza assegnarlo a un host di sessione, l’utente non sarà in grado di accedere alle risorse.

Per configurare un pool di host per l'assegnazione diretta degli utenti agli host di sessione, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Per assegnare un utente al pool di host di desktop personali, eseguire il cmdlet di PowerShell seguente:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Per assegnare un utente a un host di sessione specifico, eseguire il cmdlet di PowerShell seguente:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>Rimuovere un'assegnazione di utente

Può essere necessario rimuovere l'assegnazione di un utente nei casi in cui l'utente non necessita più del desktop personale, l'utente ha lasciato l’azienda o si vuole riutilizzare il desktop per un altro utente.

Attualmente, l'unico modo per rimuovere l'assegnazione di un utente a un desktop personale consiste nel rimuovere completamente l'host di sessione. Per rimuovere l'host di sessione, eseguire il cmdlet seguente:

```powershell
Remove-RdsSessionHost
```

Se è necessario aggiungere di nuovo l'host di sessione al pool di host di desktop personali, disinstallare Desktop virtuale Windows nel computer, quindi seguire la procedura descritta in[Creare un pool di host con PowerShell](create-host-pools-powershell-2019.md) per registrare nuovamente l'host di sessione.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il tipo di assegnazione del desktop personale, è possibile accedere a un client Desktop virtuale Windows per testarlo nell’ambito di una sessione utente. Le prossime due procedure spiegano come connettersi a una sessione usando il client di propria scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-10-2019.md)
- [Connettersi con il client Web](connect-web-2019.md)
