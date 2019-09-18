---
title: Si verifica un errore interno quando si esegue una connessione RDP a Macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come risolvere gli errori interni di RDP in Microsoft Azure.| Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: dac941b621c8df6b5c242bb5d0e0d5cdd1f864a9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057957"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Si verifica un errore interno quando si prova a connettersi a una macchina virtuale di Azure tramite Desktop remoto

Questo articolo descrive un errore che può verificarsi quando si tenta di connettersi a una macchina virtuale Windows in Microsoft Azure.
> [!NOTE]
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'utilizzo del modello di distribuzione Resource Manager, che Microsoft consiglia di usare per le nuove distribuzioni in sostituzione del modello di distribuzione classica.

## <a name="symptoms"></a>Sintomi

Non è possibile connettersi a una macchina virtuale di Azure usando il protocollo Remote Desktop Protocol (RDP). La connessione si blocca nella sezione “Configurazione remota”, oppure viene visualizzato il seguente messaggio di errore:

- Errore interno di RDP
- Si è verificato un errore interno
- Non è possibile connettere questo computer al computer remoto. Provare a eseguire di nuovo la connessione. Se il problema persiste, contattare il proprietario del computer remoto o l'amministratore di rete


## <a name="cause"></a>Causa

Questo problema può verificarsi a causa dei motivi seguenti:

- Non è possibile accedere alle chiavi di crittografia RSA locali.
- Il protocollo TLS è disabilitato.
- Il certificato è danneggiato o scaduto.

## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

Per risolvere questo problema, usare la console seriale o [riparare la macchina virtuale in modalità offline](#repair-the-vm-offline) collegando il disco del sistema operativo della macchina virtuale a una macchina virtuale di ripristino.


### <a name="use-serial-control"></a>Usare il controllo seriale

Connettersi alla [console seriale e aprire un'istanza di PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se la console seriale non è abilitata nella macchina virtuale, andare alla sezione [Riparare la macchina virtuale in modalità offline](#repair-the-vm-offline).

#### <a name="step-1-check-the-rdp-port"></a>Passaggio 1: Verificare la porta RDP

1. In un'istanza di PowerShell, usare il [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) per verificare se la porta 8080 è usata da altre applicazioni:

        Netstat -anob |more
2. Se Termservice.exe utilizza la porta 8080, andare al passaggio 2. Se un altro servizio o applicazione diversa da Termservice.exe utilizza la porta 8080, seguire questa procedura:

    1. Arrestare il servizio per l'applicazione che usa il servizio 3389:

            Stop-Service -Name <ServiceName> -Force

    2. Avviare il servizio terminal:

            Start-Service -Name Termservice

2. Se l'applicazione non può essere arrestata o se questo metodo non è applicabile all'utente, modificare la porta per RDP:

    1. Modificare la porta:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Impostare il firewall per la nuova porta:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Aggiornare il gruppo di sicurezza di rete per la nuova porta](../../virtual-network/security-overview.md) nella porta per RDP del portale di Azure.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Passaggio 2: Impostare le autorizzazioni corrette per il certificato autofirmato RDP

1.  In un'istanza di PowerShell, eseguire i comandi seguenti uno alla volta per rinnovare il certificato autofirmato RDP:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Se non è possibile rinnovare il certificato con questo metodo, provare a rinnovare il certificato autofirmato RDP in modalità remota:

    1. Da una macchina virtuale che disponga di connettività alla macchina virtuale che presenta problemi, digitare **mmc** nella casella **Eseguire** per aprire Microsoft Management Console.
    2. Nel menu **File** selezionare **Aggiungi/Rimuovi snap-in**, selezionare **Certificati** e quindi selezionare **Aggiungi**.
    3. Selezionare **Account computer**, selezionare **Altro computer** e quindi aggiungere l'indirizzo IP della macchina virtuale che presenta problemi.
    4. Passare alla cartella **Remote Desktop\Certificates**, fare clic con il pulsante destro del mouse sul certificato e quindi selezionare **Elimina**.
    5. In un'istanza di PowerShell dalla Console seriale, riavviare il servizio di configurazione di Desktop remoto:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Reimpostare l'autorizzazione per la cartella MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Riavviare la macchina virtuale e quindi tentare di avviare una connessione Desktop remoto alla macchina virtuale. Se l'errore persiste ancora, passare al passaggio successivo.

Passaggio 3: Abilitare tutte le versioni TLS supportate

Il client RDP usa TLS 1.0 come protocollo predefinito. Tuttavia, ciò può essere modificato in TLS 1.1, che è diventato il nuovo standard. Se TLS 1.1 è disabilitato nella macchina virtuale, la connessione avrà esito negativo.
1.  In un'istanza CMD, abilitare il protocollo TLS:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Per impedire che i criteri AD sovrascrivano le modifiche, interrompere temporaneamente l'aggiornamento dei criteri di gruppo:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Riavviare la macchina virtuale per applicare le modifiche. Se il problema viene risolto, eseguire il comando seguente per abilitare nuovamente i criteri di gruppo:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Se la modifica viene ripristinata, significa che è presente un criterio di Active Directory nel dominio aziendale. È necessario modificare tale criterio per evitare che questo problema si ripeta.

### <a name="repair-the-vm-offline"></a>Riparare la macchina virtuale in modalità offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Collegare il disco del sistema operativo alla macchina virtuale di ripristino

1. [Collegare il disco del sistema operativo alla macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md).
2. Dopo aver collegato il disco del sistema operativo alla macchina virtuale di ripristino, verificare che il disco sia contrassegnato come **Online** nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco del sistema operativo collegato.
3. Avviare una connessione Desktop remoto alla macchina virtuale di ripristino.

#### <a name="enable-dump-log-and-serial-console"></a>Abilitare il log Dump e la console seriale

Per abilitare il log Dump e la console seriale, eseguire lo script seguente.

1. Aprire una sessione del prompt dei comandi con privilegi elevati (**Esegui come amministratore**).
2. Eseguire lo script seguente:

    In questo script si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la specifica macchina virtuale.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Reimpostare l'autorizzazione per la cartella MachineKeys

1. Aprire una sessione del prompt dei comandi con privilegi elevati (**Esegui come amministratore**).
2. Eseguire lo script seguente. In questo script si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la specifica macchina virtuale.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Abilitare tutte le versioni TLS supportate

1.  Aprire una sessione del prompt dei comandi con privilegi elevati (**Esegui come amministratore**) e quindi eseguire i comandi seguenti. Lo script seguente presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la specifica macchina virtuale.
2.  Verificare quale protocollo TLS è abilitato:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Se la chiave non esiste o il relativo valore è **0**, abilitare il protocollo eseguendo gli script seguenti:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Abilitare NLA:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Scollegare il disco del sistema operativo e ricreare la macchina virtuale](../windows/troubleshoot-recovery-disks-portal.md), quindi controllare se il problema è risolto.





