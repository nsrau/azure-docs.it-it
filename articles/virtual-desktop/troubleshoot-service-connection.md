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
ms.openlocfilehash: 56a31caeefb3589527fdbbac118fa3a544a0d1a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208894"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connessioni del servizio Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
