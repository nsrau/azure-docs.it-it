---
title: Risoluzione dei problemi di connessione del servizio Desktop virtuale Windows - Azure
description: Come risolvere i problemi quando si configurano le connessioni client in un ambiente tenant di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 875f39653c5240e3a1b571b531eb2bb08c4811d0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289853"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connessioni del servizio Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica al desktop virtuale di Windows con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa desktop virtuale di Windows (classico) senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Usare questo articolo per risolvere i problemi relativi alle connessioni client di Desktop virtuale Windows.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

È possibile inviare commenti e discutere del servizio Desktop virtuale Windows con il team del prodotto e gli altri membri attivi della community nella [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>L'utente si connette ma non viene visualizzato nulla (nessun feed)

Un utente può avviare i client Desktop remoto ed è in grado di eseguire l'autenticazione, tuttavia non visualizza alcuna icona nel feed di individuazione Web.

1. Verificare che l'utente che segnala i problemi sia stato assegnato ai gruppi di applicazioni utilizzando la riga di comando seguente:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Verificare che l'utente abbia effettuato l'accesso con le credenziali corrette.

3. Se viene utilizzato il client Web, verificare che non siano presenti problemi di credenziali memorizzate nella cache.

4. Se l'utente fa parte di un gruppo di utenti Azure Active Directory (AD), assicurarsi che il gruppo di utenti sia un gruppo di sicurezza anziché un gruppo di distribuzione. Desktop virtuale Windows non supporta i gruppi di distribuzione Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a Desktop virtuale Windows e alle tracce di escalation, consultare [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un ambiente Desktop virtuale Windows e di un pool di host in un ambiente Desktop virtuale Windows, consultare [Creazione di ambiente e pool di host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi all'uso di PowerShell con Desktop virtuale di Windows, consultare [PowerShell con Desktop virtuale Windows](troubleshoot-powershell.md).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
