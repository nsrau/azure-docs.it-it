---
title: Disabilitare il firewall del sistema operativo guest nella macchina virtuale di Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: a8856bd46f516aa3c64965648d4f23b9ba665b1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505462"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Disabilitare il firewall del sistema operativo guest nella macchina virtuale di Azure

Questo articolo fornisce informazioni di riferimento per le situazioni in cui si sospetta che il firewall del sistema operativo guest filtri il traffico parziale o completo verso una macchina virtuale. Una situazione di questo tipo può verificarsi se sono state apportate deliberatamente modifiche al firewall che hanno causato errori di connessione RDP.

## <a name="solution"></a>Soluzione

La procedura descritta in questo articolo deve essere usata come soluzione alternativa che consenta di concentrarsi sulla risoluzione del problema reale, ossia la corretta configurazione delle regole del firewall. Microsoft consiglia di tenere il componente Windows Firewall abilitato. La modalità di configurazione delle regole del firewall dipende dal livello di accesso alla macchina virtuale necessario.

### <a name="online-solutions"></a>Soluzioni online 

Se la macchina virtuale è online ed è accessibile su un'altra macchina virtuale nella stessa rete virtuale, è possibile apportare le mitigazioni seguenti usando l'altra macchina virtuale.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Mitigazione 1: Funzionalità di estensione di Script o eseguire un comando personalizzato

Se è in funzione un agente di Azure, è possibile usare l'[estensione dello script personalizzata](../extensions/custom-script-windows.md) o la funzionalità [Comandi di esecuzione](../windows/run-command.md) (solo macchine virtuali di Resource Manager) per eseguire in remoto gli script seguenti.

> [!Note]
> * Se il firewall è configurato in locale, eseguire lo script seguente:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Se il firewall è configurato tramite un criterio di Active Directory, è possibile eseguire lo script seguente per l'accesso temporaneo. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Tuttavia, appena il criterio viene applicato di nuovo, si verrà disconnessi dalla sessione remota. La correzione definitiva per questo problema consiste nel modificare il criterio applicato a questo computer.

#### <a name="mitigation-2-remote-powershell"></a>Mitigazione 2: PowerShell remoto

1.  Connettersi a una macchina virtuale che si trova nella stessa rete virtuale della macchina virtuale a cui non è possibile accedere tramite una connessione RDP.

2.  Aprire una finestra della console di PowerShell.

3.  Eseguire i comandi seguenti:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Se il firewall viene impostato tramite un oggetto Criteri di gruppo, questo metodo potrebbe non funzionare perché questo comando modifica solo le voci del Registro di sistema locale. Se è applicato un criterio, eseguirà l'override di questa modifica. 

#### <a name="mitigation-3-pstools-commands"></a>Mitigazione 3: Comandi PSTools

1.  Nella macchina virtuale per la risoluzione dei problemi scaricare [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Aprire un'istanza di CMD, quindi accedere alla macchina virtuale tramite il relativo indirizzo IP interno.

3.  Eseguire i comandi seguenti:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Mitigazione 4: Registro di sistema remoto 

Seguire questa procedura per usare il [Registro di sistema remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  Nella macchina virtuale per la risoluzione dei problemi avviare l'editor del Registro di sistema e quindi passare a **File** > **Connetti a Registro di sistema in rete**.

2.  Aprire il ramo  *COMPUTER DI DESTINAZIONE*\SYSTEM e specificare i valori seguenti:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Riavviare il servizio. Poiché non è possibile eseguire questa operazione tramite il Registro di sistema remoto, è necessario usare la console Servizi.

4.  Aprire un'istanza di  **Services.msc**.

5.  Fare clic su **Servizi (computer locale)** .

6.  Selezionare **Connetti a un altro computer**.

7.  Immettere l' **indirizzo IP privato**  della macchina virtuale interessata dal problema.

8.  Riavviare il criterio firewall locale.

9.  Provare di nuovo a connettersi alla macchina virtuale tramite RDP dal computer locale.

### <a name="offline-solutions"></a>Soluzioni offline 

In una situazione in cui non è possibile accedere alla macchina virtuale con uno di questi metodi, l'uso dell'estensione dello script personalizzata avrà esito negativo e sarà necessario passare in modalità OFFLINE lavorando direttamente sul disco di sistema. A tale scopo, seguire questi passaggi:

1.  [Collegare il disco di sistema a una macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md).

2.  Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.

3.  Verificare che il disco sia contrassegnato come Online nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco di sistema collegato.

4.  Prima di apportare qualsiasi modifica, creare una copia della cartella \windows\system32\config nel caso in cui sia necessario un ripristino dello stato precedente.

5.  Nella macchina virtuale per la risoluzione dei problemi avviare l'editor del Registro di sistema (regedit.exe). 

6.  Per questa procedura di risoluzione dei problemi vengono montati gli hive come BROKENSYSTEM e BROKENSOFTWARE.

7.  Evidenziare la chiave HKEY_LOCAL_MACHINE e quindi scegliere File > Carica Hive.

8.  Individuare il file \windows\system32\config\SYSTEM file nel disco di sistema collegato.

9.  Aprire un'istanza di PowerShell con privilegi elevati e quindi eseguire i comandi seguenti:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Scollegare il disco di sistema e creare di nuovo la macchina virtuale](troubleshoot-recovery-disks-portal-windows.md).

11. Controllare se il problema è stato risolto.
