---
title: Risoluzione dei problemi di connessione al servizio desktop virtuale Windows-Azure
description: Come risolvere i problemi quando si configurano le connessioni client in un ambiente tenant di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612640"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connessioni al servizio desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usare questo articolo per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows.

## <a name="provide-feedback"></a>Inviare feedback

È possibile inviare commenti e suggerimenti e discutere il servizio desktop virtuale di Windows con il team del prodotto e altri membri della community attiva presso la [community di tecnologia desktop virtuale di Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>L'utente si connette ma non viene visualizzato nulla (nessun feed)

Un utente può avviare Desktop remoto client ed è in grado di eseguire l'autenticazione, tuttavia l'utente non visualizza alcuna icona nel feed di individuazione Web.

Verificare che l'utente che ha segnalato i problemi sia stato assegnato ai gruppi di applicazioni utilizzando la riga di comando seguente:

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

Verificare che l'utente abbia effettuato l'accesso con le credenziali corrette.

Se il client Web viene utilizzato, verificare che non siano presenti problemi di credenziali memorizzate nella cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Le macchine virtuali con più sessioni Enterprise di Windows 10 non rispondono

Se una macchina virtuale non risponde e non è possibile accedervi tramite RDP, sarà necessario risolverla con la funzionalità di diagnostica controllando lo stato dell'host.

Per controllare lo stato dell'host, eseguire questo cmdlet:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

Se lo stato dell'host `NoHeartBeat`è, significa che la macchina virtuale non risponde e l'agente non è in grado di comunicare con il servizio desktop virtuale di Windows.

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

Per correggere lo stato noheartbeat, è possibile eseguire alcune operazioni.

### <a name="update-fslogix"></a>Aggiornare FSLogix

Se il FSLogix non è aggiornato, soprattutto se è la versione 2.9.7205.27375 di frxdrvvt. sys, potrebbe causare un deadlock. Assicurarsi di [aggiornare FSLogix alla versione più recente](https://go.microsoft.com/fwlink/?linkid=2084562).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a desktop virtuale Windows e alle tracce di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un ambiente desktop virtuale Windows e di un pool host in un ambiente desktop virtuale Windows, vedere la pagina relativa alla [creazione di ambienti e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per un'esercitazione per la risoluzione dei problemi, vedere [esercitazione: risolvere i problemi relativi alle distribuzioni di modelli gestione risorse](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
