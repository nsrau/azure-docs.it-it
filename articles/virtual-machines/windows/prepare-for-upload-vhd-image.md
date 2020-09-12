---
title: Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure
description: Informazioni su come preparare un disco rigido virtuale Windows o VHDX per caricarlo in Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: 642a1937f44a608ebf235c20da060972788046a0
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321736"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure

Prima di caricare una macchina virtuale (VM) Windows dall'ambiente locale ad Azure, è necessario preparare il disco rigido virtuale (VHD o VHDX). Azure supporta macchine virtuali sia di prima che di seconda generazione in formato di file VHD con un disco a dimensione fissa. La dimensione massima consentita per il VHD del sistema operativo in una macchina virtuale di prima generazione è 2 TB.

È possibile convertire un file VHDX in VHD, convertire un disco a espansione dinamica in un disco di dimensioni fisse, ma non è possibile modificare la generazione di una macchina virtuale. Per altre informazioni, vedere la pagina relativa alla [creazione di una macchina virtuale di generazione 1 o 2 in Hyper-V](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) e [il supporto per le macchine virtuali di seconda generazione in Azure](generation-2.md).

Per informazioni sui criteri di supporto per le macchine virtuali di Azure, vedere [supporto del software server Microsoft per macchine virtuali di Azure](https://support.microsoft.com/help/2721672/).

> [!NOTE]
> Le istruzioni riportate in questo articolo si applicano a:
>
> - Versione a 64 bit dei sistemi operativi Windows Server 2008 R2 e versioni successive. Per informazioni sull'esecuzione di un sistema operativo a 32 bit in Azure, vedere [supporto per i sistemi operativi a 32 bit nelle VM di Azure](https://support.microsoft.com/help/4021388/).
> - Se si usa uno strumento di ripristino di emergenza per eseguire la migrazione del carico di lavoro, ad esempio Azure Site Recovery o Azure Migrate, questo processo è ancora necessario sul sistema operativo guest per preparare l'immagine prima della migrazione.

## <a name="system-file-checker"></a>Controllo file di sistema

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>Eseguire l'utilità controllo file di sistema Windows prima di generalizzare l'immagine del sistema operativo

Il controllo file di sistema (SFC) viene utilizzato per verificare e sostituire i file di sistema di Windows.

> [!IMPORTANT]
> Usare una sessione di PowerShell con privilegi elevati per eseguire gli esempi in questo articolo.

Eseguire il comando SFC:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

Al termine dell'analisi SFC, installare gli aggiornamenti di Windows e riavviare il computer.

## <a name="set-windows-configurations-for-azure"></a>Impostare le configurazioni di Windows per Azure

> [!NOTE]
> La piattaforma Azure monta un file ISO nel DVD-ROM quando viene creata una macchina virtuale Windows da un'immagine generalizzata. Per questo motivo, è necessario abilitare il DVD-ROM nel sistema operativo nell'immagine generalizzata. Se è disabilitata, la macchina virtuale Windows rimarrà bloccata a livello di configurazione.

1. Rimuovere eventuali route permanenti statiche nella tabella di routing:

   - Per visualizzare la tabella di routing, eseguire `route.exe print` .
   - Controllare la sezione **Route di persistenza** . Se è presente una route persistente, utilizzare il `route.exe delete` comando per rimuoverla.

1. Rimuovere il proxy WinHTTP:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Se la macchina virtuale deve usare un proxy specifico, aggiungere un'eccezione proxy per l'indirizzo IP di Azure ([168.63.129.16](/azure/virtual-network/what-is-ip-address-168-63-129-16)) in modo che la macchina virtuale possa connettersi ad Azure:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. Apri DiskPart:

   ```powershell
   diskpart.exe
   ```

   Impostare i criteri SAN del disco su [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) :

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Imposta l'ora UTC (Coordinated Universal Time) per Windows. Inoltre, impostare il tipo di avvio del servizio ora di Windows **W32Time** su **automatico**:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Impostare il profilo di alimentazione su prestazioni elevate:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Verificare che le variabili di ambiente **Temp** e **tmp** siano impostate sui valori predefiniti seguenti:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>Verificare i servizi di Windows

Verificare che tutti i servizi Windows seguenti siano impostati sul valore predefinito di Windows. Questi servizi sono i minimi che devono essere configurati per garantire la connettività della macchina virtuale. Per impostare le impostazioni di avvio, eseguire l'esempio seguente:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Aggiornare le impostazioni del registro di sistema desktop remoto

Verificare che le impostazioni seguenti siano configurate correttamente per l'accesso remoto:

> [!NOTE]
> Se durante l'esecuzione viene visualizzato un messaggio di errore `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` , è possibile ignorarlo. Significa che il dominio non imposta la configurazione tramite un oggetto Criteri di gruppo.

1. Remote Desktop Protocol (RDP) è abilitato:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. La porta RDP è configurata correttamente con la porta predefinita 3389:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Quando si distribuisce una macchina virtuale, vengono create le regole predefinite per la porta 3389. Per modificare il numero di porta, eseguire questa operazione dopo la distribuzione della macchina virtuale in Azure.

1. Il listener è in ascolto su ogni interfaccia di rete:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Configurare la modalità di autenticazione a livello di rete (NLA) per le connessioni RDP:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Impostare il valore keep-alive:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Impostare le opzioni di riconnessione:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Limitare il numero di connessioni simultanee:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Rimuovere tutti i certificati autofirmati associati al listener RDP:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Questo codice garantisce che sia possibile connettersi quando si distribuisce la macchina virtuale. È anche possibile esaminare queste impostazioni dopo che la macchina virtuale è stata distribuita in Azure.

1. Se la macchina virtuale fa parte di un dominio, verificare i criteri seguenti per assicurarsi che le impostazioni precedenti non vengano ripristinate.

    |                 Obiettivo                  |                                                                            Criteri                                                                            |                           Valore                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP è abilitato                        | Configurazione computer\Criteri\Impostazioni di Windows \Modelli amministrativi\Componenti\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni         | Consenti la connessione remota tramite Servizi Desktop remoto    |
    | Criteri di gruppo NLA                      | Impostazioni\Modelli amministrativi\Componenti\Servizi Desktop remoto\Host sessione Desktop remoto\Sicurezza                                                    | Richiedere l'autenticazione utente per l'accesso remoto usando NLA |
    | Impostazioni Keep-Alive                   | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | Configura intervallo keep-alive della connessione                   |
    | Impostazioni di riconnessione                    | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | Riconnetti automaticamente                                    |
    | Numero limitato di impostazioni di connessione | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | Limita il numero di connessioni                                |

## <a name="configure-windows-firewall-rules"></a>Configurare le regole del firewall di Windows

1. Attivare Windows Firewall nei tre profili (dominio, standard e pubblico):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Eseguire l'esempio seguente per consentire WinRM attraverso i tre profili firewall (dominio, privato e pubblico) e abilitare il servizio remoto di PowerShell:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Abilitare le regole del firewall seguenti per consentire il traffico RDP:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Abilitare la regola per la condivisione di file e stampanti in modo che la macchina virtuale possa rispondere alle richieste di ping all'interno della rete virtuale:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Creare una regola per la rete della piattaforma Azure:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Se la macchina virtuale fa parte di un dominio, controllare i criteri di Azure AD seguenti per assicurarsi che le impostazioni precedenti non vengano ripristinate.

    |                 Obiettivo                 |                                                                         Criteri                                                                          |                  Valore                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Abilitare i profili di Windows Firewall | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Proteggi tutte le connessioni di rete         |
    | Abilitare RDP                           | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Consenti eccezioni per Desktop remoto in ingresso |
    |                                      | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall | Consenti eccezioni per Desktop remoto in ingresso |
    | Abilitare ICMP V4                       | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Consenti eccezioni ICMP                   |
    |                                      | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall | Consenti eccezioni ICMP                   |

## <a name="verify-the-vm"></a>Verificare la VM

Assicurarsi che la macchina virtuale sia integra, sicura e accessibile tramite RDP:

1. Per assicurarsi che il disco sia integro e coerente, controllare il disco al riavvio successivo della macchina virtuale:

   ```powershell
   chkdsk.exe /f
   ```

   Verificare che il report mostri un disco pulito e integro.

1. Configurare le impostazioni dei dati di configurazione di avvio.

   ```powershell
   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Il log dump può essere utile per la risoluzione dei problemi di arresto anomalo di Windows. Abilitare la raccolta dei log di dump:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Verificare che il repository di Strumentazione gestione Windows (WMI) sia coerente:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Se il repository è danneggiato, vedere [WMI: danneggiamento del repository](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. Verificare che nessun'altra applicazione usi la porta 3389. Questa porta viene utilizzata per il servizio RDP in Azure. Per visualizzare le porte usate nella macchina virtuale, eseguire `netstat.exe -anob` :

   ```powershell
   netstat.exe -anob
   ```

1. Per caricare un disco rigido virtuale Windows che è un controller di dominio:

   - Seguire [questi passaggi aggiuntivi](https://support.microsoft.com/kb/2904015) per preparare il disco.

   - Assicurarsi di essere a conoscenza della password della modalità ripristino servizi directory, nel caso in cui sia necessario avviare la VM in modalità ripristino servizi directory. Per ulteriori informazioni, vedere [impostare una password per la modalità ripristino servizi directory](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11)).

1. Assicurarsi di avere familiarità con l'account amministratore predefinito e la password. Potrebbe essere necessario reimpostare la password dell'amministratore locale corrente e verificare che sia possibile utilizzare questo account per accedere a Windows tramite la connessione RDP. Questa autorizzazione di accesso è controllata dall'oggetto Criteri di gruppo "Consenti accesso tramite Servizi Desktop remoto". Visualizzare questo oggetto nel Editor Criteri di gruppo locali:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Controllare i seguenti criteri di Azure AD per assicurarsi che non stiano bloccando l'accesso RDP:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Controllare i seguenti criteri di Azure AD per assicurarsi che non vengano rimossi gli account di accesso necessari:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   Il criterio dovrebbe elencare i gruppi seguenti:

   - Administrators

   - Backup Operators

   - Tutti

   - Utenti

1. Riavviare la VM per assicurarsi che Windows sia ancora integro e che possa essere raggiunto tramite la connessione RDP. A questo punto, è consigliabile creare una macchina virtuale nel server Hyper-V locale per assicurarsi che la macchina virtuale venga avviata completamente. Eseguire quindi il test per assicurarsi che sia possibile raggiungere la macchina virtuale tramite RDP.

1. Rimuovere eventuali filtri di Transport Driver Interface aggiuntivi (TDI). Rimuovere, ad esempio, il software che analizza i pacchetti TCP o i firewall aggiuntivi.

1. Disinstallare qualsiasi altro software o driver di terze parti correlato a componenti fisici o qualsiasi altra tecnologia di virtualizzazione.

### <a name="install-windows-updates"></a>Installare aggiornamenti di Windows

Idealmente, è consigliabile lasciare aggiornato il computer al *livello di patch*, se ciò non è possibile, verificare che siano installati i seguenti aggiornamenti. Per ottenere gli aggiornamenti più recenti, vedere le pagine relative alla cronologia di Windows Update: [Windows 10 e Windows server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 e Windows Server 2012 R2](https://support.microsoft.com/help/4009470) e [Windows 7 SP1 e Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Componente        |     Binary     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Archiviazione                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| Rete                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Servizi Desktop remoto | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Sicurezza                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> Per evitare un riavvio accidentale durante il provisioning della macchina virtuale, è consigliabile assicurarsi che tutte le installazioni di Windows Update siano completate e che non siano presenti aggiornamenti in sospeso. Un modo per eseguire questa operazione consiste nell'installare tutti i possibili aggiornamenti di Windows e riavviare una volta prima di eseguire il `sysprep.exe` comando.

## <a name="determine-when-to-use-sysprep"></a>Determinare quando utilizzare Sysprep

System Preparation Tool ( `sysprep.exe` ) è un processo che è possibile eseguire per reimpostare un'installazione di Windows.
Sysprep fornisce un'esperienza "predefinita" rimuovendo tutti i dati personali e reimpostando diversi componenti.

In genere si esegue `sysprep.exe` per creare un modello da cui è possibile distribuire diverse altre VM con una configurazione specifica. Il modello è denominato *immagine generalizzata*.

Per creare una sola macchina virtuale da un disco, non è necessario usare Sysprep. È invece possibile creare la macchina virtuale da un' *immagine specializzata*. Per informazioni su come creare una macchina virtuale da un disco specializzato, vedere:

- [Creare una macchina virtuale da un disco specializzato](create-vm-specialized.md)
- [Creare una macchina virtuale da un disco rigido virtuale specializzato](/azure/virtual-machines/windows/create-vm-specialized-portal)

Per creare un'immagine generalizzata, è necessario eseguire Sysprep. Per ulteriori informazioni, vedere [How to use Sysprep: An Introduction](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Non tutti i ruoli o le applicazioni installate in un computer basato su Windows supportano immagini generalizzate. Prima di utilizzare questa procedura, assicurarsi che Sysprep supporti il ruolo del computer. Per ulteriori informazioni, vedere [supporto Sysprep per i ruoli del server](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

In particolare, Sysprep richiede che le unità vengano decrittografate completamente prima dell'esecuzione. Se è stata abilitata la crittografia nella macchina virtuale, disabilitarla prima di eseguire Sysprep.


### <a name="generalize-a-vhd"></a>Generalizzare un disco rigido virtuale

>[!NOTE]
> Dopo aver eseguito `sysprep.exe` i passaggi seguenti, spegnere la macchina virtuale. Non riattivarlo fino a quando non si crea un'immagine da esso in Azure.

1. Accedere alla VM Windows.
1. Eseguire una sessione di PowerShell come amministratore.
1. Passare alla directory `%windir%\system32\sysprep` . Quindi eseguire `sysprep.exe`.
1. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Accedi alla configurazione guidata**e verificare che la casella di controllo **generalizza** sia selezionata.

    ![Utilità preparazione sistema](media/prepare-for-upload-vhd-image/syspre.png)
1. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
1. Selezionare **OK**.
1. Quando Sysprep termina, arresta la macchina virtuale. Non usare **Riavvia** per arrestare la macchina virtuale.

A questo punto il disco rigido virtuale è pronto per essere caricato. Per altre informazioni su come creare una macchina virtuale da un disco generalizzato, vedere [caricare un disco rigido virtuale generalizzato e usarlo per creare una nuova macchina virtuale in Azure](sa-upload-generalized.md).

>[!NOTE]
> Un file di *unattend.xml* personalizzato non è supportato. Anche se la proprietà **additionalUnattendContent** è supportata, questo fornisce solo un supporto limitato per l'aggiunta di opzioni [Microsoft-Windows-Shell-setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) al file *unattend.xml* usato dall'agente di provisioning di Azure. È possibile usare, ad esempio, [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) per aggiungere FirstLogonCommands e LogonCommands. Per altre informazioni, vedere [esempio di AdditionalUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>Convertire il disco virtuale in un disco rigido virtuale a dimensione fissa

Usare uno dei metodi descritti in questa sezione per convertire e ridimensionare il disco virtuale nel formato richiesto per Azure:

1. Eseguire il backup della VM prima di eseguire il processo di conversione o ridimensionamento del disco virtuale.

1. Verificare che il disco rigido virtuale di Windows funzioni correttamente nel server locale. Risolvere qualsiasi errore nella macchina virtuale prima di provare a convertire o caricare il disco in Azure.

1. Convertire il disco virtuale nel tipo fixed.

1. Ridimensionare il disco virtuale per soddisfare i requisiti di Azure:

   1. I dischi in Azure devono avere una dimensione virtuale allineata a 1 MiB. Se il disco rigido virtuale è una frazione di 1 MiB, sarà necessario ridimensionare il disco a un multiplo di 1 MiB. I dischi che sono frazioni di MiB generano errori durante la creazione di immagini dal disco rigido virtuale caricato. Per verificare le dimensioni, è possibile usare il cmdlet PowerShell [Get-VHD](/powershell/module/hyper-v/get-vhd) per mostrare "size", che deve essere un multiplo di 1 MIB in Azure e "FileSize", che sarà uguale a "size" più 512 byte per il piè di pagina del disco rigido virtuale.
   
   1. La dimensione massima consentita per il disco rigido virtuale del sistema operativo con una macchina virtuale di prima generazione è 2.048 GiB (2 TiB), 
   1. La dimensione massima per un disco dati è 32.767 GiB (32 TiB).

> [!NOTE]
> - Se si sta preparando un disco del sistema operativo Windows dopo la conversione in un disco fisso e il ridimensionamento, se necessario, creare una VM che usa il disco. Avviare e accedere alla macchina virtuale e continuare con le sezioni di questo articolo per completare la preparazione per il caricamento.  
> - Se si sta preparando un disco dati, è possibile interrompere questa sezione e continuare a caricare il disco.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Usare la console di gestione di Hyper-V per convertire il disco

1. Aprire la console di gestione di Hyper-V e selezionare il computer locale a sinistra. Nel menu sopra l'elenco computer selezionare **azione**  >  **modifica disco**.
1. Nella pagina **Individua disco rigido virtuale** selezionare il disco virtuale.
1. Nella pagina **Scegli azione** selezionare **Converti**  >  **successivo**.
1. Per eseguire la conversione da VHDX, selezionare **VHD**  >  **Avanti**.
1. Per eseguire la conversione da un disco a espansione dinamica, selezionare **dimensioni fisse**  >  **Avanti**.
1. Individuare e selezionare un percorso in cui salvare il nuovo file VHD.
1. Selezionare **Fine**.

### <a name="use-powershell-to-convert-the-disk"></a>Usare PowerShell per convertire il disco

È possibile convertire un disco virtuale usando il cmdlet [Convert-VHD](/powershell/module/hyper-v/convert-vhd) in PowerShell. Per informazioni sull'installazione di questo cmdlet, vedere [installare il ruolo Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

Nell'esempio seguente viene convertito il disco da VHDX a VHD. Converte inoltre il disco da un disco a espansione dinamica a un disco a dimensione fissa.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

In questo esempio, sostituire il valore di **path** con il percorso del disco rigido virtuale che si desidera convertire. Sostituire il valore di **DestinationPath** con il nuovo percorso e il nome del disco convertito.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Usare la console di gestione di Hyper-V per ridimensionare il disco

1. Aprire la console di gestione di Hyper-V e selezionare il computer locale a sinistra. Nel menu sopra l'elenco computer selezionare **azione**  >  **modifica disco**.
1. Nella pagina **Individua disco rigido virtuale** selezionare il disco virtuale.
1. Nella pagina **Scegli azione** selezionare **Espandi**  >  **Avanti**.
1. Nella pagina **Individua disco rigido virtuale** immettere le nuove dimensioni in Gib > **Avanti**.
1. Selezionare **Fine**.

### <a name="use-powershell-to-resize-the-disk"></a>Usare PowerShell per ridimensionare il disco

È possibile ridimensionare un disco virtuale usando il cmdlet [Resize-VHD](/powershell/module/hyper-v/resize-vhd) in PowerShell. Per informazioni sull'installazione di questo cmdlet, vedere [installare il ruolo Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

L'esempio seguente ridimensiona il disco da 100,5 MiB a 101 MiB per soddisfare il requisito di allineamento di Azure.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

In questo esempio, sostituire il valore di **path** con il percorso del disco rigido virtuale che si desidera ridimensionare. Sostituire il valore di **SizeBytes** con le nuove dimensioni in byte per il disco.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Conversione dal formato VMware VMDK

Se si dispone di un'immagine di macchina virtuale Windows nel [formato di file VMDK](https://en.wikipedia.org/wiki/VMDK), è possibile usare [Azure migrate](https://docs.microsoft.com/azure/migrate/server-migrate-overview) per convertire il file VMDK e caricarlo in Azure.

## <a name="complete-the-recommended-configurations"></a>Completare le configurazioni consigliate

Le impostazioni seguenti non influiscono sul caricamento del disco rigido virtuale. È tuttavia fortemente consigliabile configurarle.

- Installare l' [agente di macchine virtuali di Azure](https://go.microsoft.com/fwlink/?LinkID=394789). Sarà quindi possibile abilitare le estensioni delle VM. Le estensioni VM implementano la maggior parte delle funzionalità critiche che è possibile usare con le macchine virtuali. Sono necessarie le estensioni, ad esempio, per reimpostare le password o configurare RDP. Per altre informazioni, vedere [Panoramica dell'agente di macchine virtuali di Azure](../extensions/agent-windows.md).
- Dopo aver creato la macchina virtuale in Azure, è consigliabile inserire il file di paging nel *volume dell'unità temporale* per migliorare le prestazioni. È possibile configurare il posizionamento dei file come indicato di seguito:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Se un disco dati è collegato alla macchina virtuale, la lettera del volume dell'unità temporale è in genere *D*. Questa designazione potrebbe essere diversa, a seconda delle impostazioni e del numero di unità disponibili.

  - Si consiglia di disabilitare i blocchi di script che potrebbero essere forniti dal software antivirus. Potrebbero interferire e bloccare gli script degli agenti di provisioning di Windows eseguiti quando si distribuisce una nuova macchina virtuale dall'immagine.

## <a name="next-steps"></a>Passaggi successivi

- [Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager](upload-generalized-managed.md)
- [Risolvere i problemi di attivazione della macchina virtuale Windows di Azure](troubleshoot-activation-problems.md)