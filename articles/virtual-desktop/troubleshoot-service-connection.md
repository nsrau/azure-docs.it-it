---
title: Risolvere i problemi di connessione al servizio Desktop virtuale di Windows - AzureTroubleshoot service connection Windows Virtual Desktop - Azure
description: Come risolvere i problemi quando si configurano le connessioni client in un ambiente tenant di Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127453"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connessioni al servizio Desktop virtuale Windows

Utilizzare questo articolo per risolvere i problemi relativi alle connessioni client di Desktop virtuale di Windows.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

È possibile inviare commenti e suggerimenti e discutere il servizio desktop virtuale di Windows con il team del prodotto e altri membri attivi della community della [community Tech di Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>L'utente si connette ma non viene visualizzato nulla (nessun feed)

Un utente può avviare client di Desktop remoto ed è in grado di eseguire l'autenticazione, tuttavia l'utente non vede alcuna icona nel feed di individuazione Web.

Verificare che l'utente che segnala i problemi sia stato assegnato ai gruppi di applicazioni utilizzando questa riga di comando:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Verificare che l'utente stia effettuando l'accesso con le credenziali corrette.

Se viene utilizzato il client Web, verificare che non vi siano problemi di credenziali memorizzate nella cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Le macchine virtuali multisessione di Windows 10 Enterprise non rispondono

Se una macchina virtuale non risponde e non è possibile accedervi tramite RDP, è necessario risolvere i problemi con la funzionalità di diagnostica controllando lo stato dell'host.

Per controllare lo stato dell'host, eseguire questo cmdlet:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Se lo stato `NoHeartBeat`dell'host è , significa che la macchina virtuale non risponde e che l'agente non può comunicare con il servizio Desktop virtuale di Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Ci sono alcune cose che puoi fare per risolvere lo stato di NoHeartBeat.

### <a name="update-fslogix"></a>Aggiornare FSLogixUpdate FSLogix

Se fsLogix non è aggiornato, soprattutto se è la versione 2.9.7205.27375 di frxdrvvt.sys, potrebbe causare un deadlock. Assicurarsi di [aggiornare FSLogix alla versione più recente.](https://go.microsoft.com/fwlink/?linkid=2084562)

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Disabilita BgTaskRegistrationMaintenanceTask

Se l'aggiornamento FSLogix non funziona, il problema potrebbe essere che un componente BiSrv sta esaurendo le risorse di sistema durante un'attività di manutenzione settimanale. Disattivare temporaneamente l'attività di manutenzione disattivando BgTaskRegistrationMaintenanceTask con uno dei due metodi seguenti:

- Vai al menu Start e cerca **Utilità di pianificazione**. Passare alla **libreria** > Utilità di pianificazione**Microsoft** > **Windows** > **BrokerInfrastructure**. Cercare un'attività denominata **BgTaskRegistrationMaintenanceTask**. Quando lo trovi, fai clic con il pulsante destro del mouse e seleziona **Disattiva** dal menu a discesa.
- Aprire un menu della riga di comando come amministratore ed eseguire il comando seguente:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi di Desktop virtuale di Windows e sulle tracce di escalation, vedere [Panoramica della risoluzione dei problemi, feedback e supporto.](troubleshoot-set-up-overview.md)
- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente Windows Virtual Desktop, vedere Creazione di [pool host e tenant](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Windows Virtual Desktop, vedere Configurazione della [macchina virtuale dell'host di sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi all'utilizzo di PowerShell con Windows Virtual Desktop, vedere [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Per eseguire un'esercitazione sulla risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi relativi alle distribuzioni](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)di modelli di Resource Manager .
