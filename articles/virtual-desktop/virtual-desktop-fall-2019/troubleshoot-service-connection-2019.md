---
title: Risoluzione dei problemi di connessione al servizio Desktop virtuale Windows - Azure
description: Come risolvere i problemi quando si configurano le connessioni client in un ambiente tenant di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 356506224a0273eeea65f0f901fbc79c338498d2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743610"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connessioni al servizio Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica alla versione Autunno 2019 che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si sta tentando di gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows introdotti nell'aggiornamento di Primavera 2020, vedere [questo articolo](../troubleshoot-service-connection.md).

Usare questo articolo per risolvere i problemi relativi alle connessioni client di Desktop virtuale Windows.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

È possibile inviare commenti e discutere del servizio Desktop virtuale Windows con il team del prodotto e gli altri membri attivi della community nella [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>L'utente si connette ma non viene visualizzato nulla (nessun feed)

Un utente può avviare i client Desktop remoto ed è in grado di eseguire l'autenticazione, tuttavia non visualizza alcuna icona nel feed di individuazione Web.

Verificare che l'utente che segnala i problemi sia stato assegnato ai gruppi di applicazioni usando la riga di comando seguente:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Verificare che l'utente abbia effettuato l'accesso con le credenziali corrette.

Se viene usato il client Web, verificare che non siano presenti problemi di credenziali memorizzate nella cache.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a Desktop virtuale Windows e alle tracce di escalation, consultare [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview-2019.md).
- Per risolvere i problemi durante la creazione di un tenant e di un pool di host in un ambiente di Desktop virtuale Windows, vedere [Creazione di pool di host e tenant](troubleshoot-set-up-issues-2019.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration-2019.md).
- Per risolvere i problemi relativi all'uso di PowerShell con Desktop virtuale di Windows, consultare [PowerShell con Desktop virtuale Windows](troubleshoot-powershell-2019.md).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
