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
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127453"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connessioni al servizio desktop virtuale Windows

Usare questo articolo per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

È possibile inviare commenti e suggerimenti e discutere il servizio desktop virtuale di Windows con il team del prodotto e altri membri della community attiva presso la [community di tecnologia desktop virtuale di Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>L'utente si connette ma non viene visualizzato nulla (nessun feed)

Un utente può avviare Desktop remoto client ed è in grado di eseguire l'autenticazione, tuttavia l'utente non visualizza alcuna icona nel feed di individuazione Web.

Verificare che l'utente che ha segnalato i problemi sia stato assegnato ai gruppi di applicazioni utilizzando la riga di comando seguente:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Verificare che l'utente abbia effettuato l'accesso con le credenziali corrette.

Se il client Web viene utilizzato, verificare che non siano presenti problemi di credenziali memorizzate nella cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Le macchine virtuali con più sessioni Enterprise di Windows 10 non rispondono

Se una macchina virtuale non risponde e non è possibile accedervi tramite RDP, sarà necessario risolverla con la funzionalità di diagnostica controllando lo stato dell'host.

Per controllare lo stato dell'host, eseguire questo cmdlet:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Se lo stato dell'host è `NoHeartBeat`, significa che la macchina virtuale non risponde e l'agente non è in grado di comunicare con il servizio desktop virtuale di Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Per correggere lo stato noheartbeat, è possibile eseguire alcune operazioni.

### <a name="update-fslogix"></a>Aggiornare FSLogix

Se il FSLogix non è aggiornato, soprattutto se è la versione 2.9.7205.27375 di frxdrvvt. sys, potrebbe causare un deadlock. Assicurarsi di [aggiornare FSLogix alla versione più recente](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Disabilitare BgTaskRegistrationMaintenanceTask

Se l'aggiornamento di FSLogix non funziona, il problema potrebbe essere dovuto al esaurimento delle risorse di sistema da parte di un componente BiSrv durante un'attività di manutenzione settimanale. Disabilitare temporaneamente l'attività di manutenzione disabilitando BgTaskRegistrationMaintenanceTask con uno dei due metodi seguenti:

- Passare al menu Start e cercare **utilità di pianificazione**. Passare a **utilità di pianificazione Library** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Cercare un'attività denominata **BgTaskRegistrationMaintenanceTask**. Quando viene individuato, fare clic con il pulsante destro del mouse su di esso e scegliere **Disabilita** dal menu a discesa.
- Aprire un menu della riga di comando come amministratore ed eseguire il comando seguente:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a desktop virtuale Windows e alle tracce di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente desktop virtuale Windows, vedere [creazione di tenant e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per un'esercitazione per la risoluzione dei problemi, vedere [esercitazione: risolvere i problemi relativi alle distribuzioni di modelli gestione risorse](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
