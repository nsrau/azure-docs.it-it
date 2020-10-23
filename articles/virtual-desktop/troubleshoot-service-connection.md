---
title: Risoluzione dei problemi di connessione del servizio Desktop virtuale Windows - Azure
description: Come risolvere i problemi durante la configurazione delle connessioni ai servizi in un ambiente tenant di desktop virtuali Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9eb99ae7af88e77fa597fa92ff8e6278c307e6
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108950"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connessioni del servizio Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

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

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>L'utente perde il feed esistente e non viene visualizzata alcuna risorsa remota (nessun feed)

Questo errore viene in genere visualizzato dopo che un utente ha spostato la sottoscrizione da un tenant Azure AD a un altro. Di conseguenza, il servizio perde la traccia delle assegnazioni degli utenti, dal momento che sono ancora legate alla vecchia Azure AD tenant.

Per risolvere il problema, è sufficiente riassegnare gli utenti ai gruppi di app.

Questo problema può verificarsi anche se un provider CSP ha creato la sottoscrizione e quindi trasferita al cliente. Per risolvere il problema, ripetere la registrazione del provider di risorse.

1. Accedere al portale di Azure.
2. Passare a **sottoscrizione**, quindi selezionare la sottoscrizione.
3. Nel menu sul lato sinistro della pagina selezionare **provider di risorse**.
4. Trovare e selezionare **Microsoft. DesktopVirtualization**, quindi selezionare **Re-Register**.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a Desktop virtuale Windows e alle tracce di escalation, consultare [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un ambiente Desktop virtuale Windows e di un pool di host in un ambiente Desktop virtuale Windows, consultare [Creazione di ambiente e pool di host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi all'uso di PowerShell con Desktop virtuale di Windows, consultare [PowerShell con Desktop virtuale Windows](troubleshoot-powershell.md).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
