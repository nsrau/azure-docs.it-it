---
title: Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure | Microsoft Docs
description: Informazioni su come preparare un disco rigido virtuale Windows o VHDX per caricarlo in Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: d184201c21c31336e31dcba9884d84f6cc224ff8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924828"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure

Prima di caricare una macchina virtuale (VM) Windows dall'ambiente locale ad Azure, è necessario preparare il disco rigido virtuale (VHD o VHDX). Azure supporta macchine virtuali sia di prima che di seconda generazione in formato di file VHD con un disco a dimensione fissa. La dimensione massima consentita per il disco rigido virtuale è 1023 GB. 

In una macchina virtuale di prima generazione è possibile convertire un file system VHDX nel disco rigido virtuale. È anche possibile convertire un disco a espansione dinamica in un disco a dimensione fissa. Non è tuttavia possibile modificare la generazione di una macchina virtuale. Per altre informazioni, vedere la pagina relativa alla [creazione di una macchina virtuale di generazione 1 o 2 in Hyper-V e il](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) [supporto di Azure per le macchine virtuali di seconda generazione (anteprima)](generation-2.md).

Per informazioni sui criteri di supporto per le macchine virtuali di Azure, vedere [supporto del software server Microsoft per macchine virtuali di Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Le istruzioni riportate in questo articolo si applicano a:
>1. Versione a 64 bit dei sistemi operativi Windows Server 2008 R2 e versioni successive. Per informazioni sull'esecuzione di un sistema operativo a 32 bit in Azure, vedere [supporto per i sistemi operativi a 32 bit nelle VM di Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Se si usa uno strumento di ripristino di emergenza per eseguire la migrazione del carico di lavoro, ad esempio Azure Site Recovery o Azure Migrate, questo processo è ancora necessario e seguito nel sistema operativo guest per preparare l'immagine prima della migrazione.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Convertire il disco virtuale in una dimensione fissa e in un disco rigido virtuale

Se è necessario convertire il disco virtuale nel formato richiesto per Azure, usare uno dei metodi descritti in questa sezione:

1. Eseguire il backup della VM prima di eseguire il processo di conversione del disco virtuale.

1. Verificare che il disco rigido virtuale di Windows funzioni correttamente nel server locale. Risolvere qualsiasi errore nella macchina virtuale prima di provare a convertire o caricare il disco in Azure.

1. Per quanto riguarda le dimensioni del disco rigido virtuale:

   1. Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si esegue la conversione da un disco non elaborato al disco rigido virtuale, è necessario assicurarsi che le dimensioni del disco non elaborato siano un multiplo di 1 MB prima della conversione. Le frazioni di un megabyte provocheranno errori durante la creazione di immagini dal disco rigido virtuale caricato.

   2. La dimensione massima consentita per il VHD del sistema operativo è 2 TB.


Dopo aver convertito il disco, creare una macchina virtuale che usa il disco. Avviare e accedere alla macchina virtuale per completare la preparazione per il caricamento.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Usare la console di gestione di Hyper-V per convertire il disco 
1. Aprire la console di gestione di Hyper-V e selezionare il computer locale a sinistra. Nel menu sopra l'elenco computer selezionare **azione** > **modifica disco**.
2. Nella pagina **Individua disco rigido virtuale** selezionare il disco virtuale.
3. Nella pagina **Scegli azione** selezionare **Converti** > **Avanti**.
4. Se è necessario eseguire la conversione da VHDX, selezionare **VHD** > **Avanti**.
5. Se è necessario eseguire la conversione da un disco a espansione dinamica, selezionare **dimensioni fisse** > **Avanti**.
6. Trovare e selezionare un percorso in cui salvare il nuovo file VHD.
7. Selezionare **Fine**.

> [!NOTE]
> Usare una sessione di PowerShell con privilegi elevati per eseguire i comandi in questo articolo.

### <a name="use-powershell-to-convert-the-disk"></a>Usare PowerShell per convertire il disco 
È possibile convertire un disco virtuale usando il comando [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) di Windows PowerShell. Selezionare **Esegui come amministratore** quando si avvia PowerShell. 

Il comando di esempio seguente converte il disco da VHDX a VHD. Il comando converte inoltre il disco da un disco a espansione dinamica a un disco a dimensione fissa.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

In questo comando sostituire il valore di `-Path` con il percorso del disco rigido virtuale che si desidera convertire. Sostituire il valore di `-DestinationPath` con il nuovo percorso e il nome del disco convertito.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Conversione dal formato VMware VMDK
Se si dispone di un'immagine di macchina virtuale Windows nel [formato file VMDK](https://en.wikipedia.org/wiki/VMDK), utilizzare [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) per convertirla in formato VHD. Per ulteriori informazioni, vedere [come convertire un file VMDK VMware in un disco rigido virtuale Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Impostare le configurazioni di Windows per Azure

Nella macchina virtuale che si prevede di caricare in Azure, eseguire i comandi seguenti da una [finestra del prompt dei comandi con privilegi elevati](https://technet.microsoft.com/library/cc947813.aspx):

1. Rimuovere qualsiasi route statica persistente nella tabella di routing:
   
   * Per visualizzare la tabella di route, eseguire `route print` al prompt dei comandi.
   * Controllare le sezioni `Persistence Routes`. Se è presente una route persistente, utilizzare il comando `route delete` per rimuoverla.
2. Rimuovere il proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Se la macchina virtuale deve usare un proxy specifico, aggiungere un'eccezione proxy all'indirizzo IP di Azure ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) in modo che la macchina virtuale possa connettersi ad Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Impostare i criteri SAN del disco su [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    Nella finestra del prompt dei comandi Apri digitare i comandi seguenti:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Imposta l'ora UTC (Coordinated Universal Time) per Windows. Impostare anche il tipo di avvio del servizio ora di Windows (`w32time`) su `Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Impostare il profilo di alimentazione su prestazioni elevate:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Verificare che le variabili di ambiente `TEMP` e `TMP` siano impostate sui rispettivi valori predefiniti:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Verificare i servizi di Windows
Verificare che tutti i servizi Windows seguenti siano impostati sui valori predefiniti di Windows. Questi servizi sono i minimi che è necessario configurare per garantire la connettività della macchina virtuale. Eseguire i comandi seguenti per reimpostare le impostazioni di avvio:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```

## <a name="update-remote-desktop-registry-settings"></a>Aggiornare le impostazioni del registro di sistema desktop remoto
Verificare che le impostazioni seguenti siano configurate correttamente per l'accesso remoto:

>[!NOTE] 
>È possibile che venga visualizzato un messaggio di errore quando si esegue `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`. Questo messaggio può essere ignorato in modo sicuro. Significa solo che il dominio non esegue il push della configurazione tramite un oggetto Criteri di gruppo.

1. Remote Desktop Protocol (RDP) è abilitato:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. La porta RDP è configurata correttamente. La porta predefinita è 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Quando si distribuisce una VM, le regole predefinite vengono create per la porta 3389. Se si vuole modificare il numero di porta, eseguire questa operazione dopo la distribuzione della macchina virtuale in Azure.

3. Il listener è in ascolto in ogni interfaccia di rete:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Configurare la modalità di autenticazione a livello di rete (NLA) per le connessioni RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Impostare il valore keep-alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Ristabilire la connessione:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Limitare il numero di connessioni simultanee:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Rimuovere tutti i certificati autofirmati associati al listener RDP:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Questo codice garantisce che sia possibile connettersi all'inizio quando si distribuisce la macchina virtuale. Se è necessario esaminarlo in un secondo momento, è possibile farlo dopo la distribuzione della macchina virtuale in Azure.

9. Se la VM farà parte di un dominio, verificare i criteri seguenti per assicurarsi che le impostazioni precedenti non vengano ripristinate. 
    
    | Obiettivo                                     | Policy                                                                                                                                                       | Value                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP è abilitato                           | Configurazione computer\Criteri\Impostazioni di Windows \Modelli amministrativi\Componenti\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni         | Consenti la connessione remota tramite Servizi Desktop remoto                                  |
    | Criteri di gruppo NLA                         | Impostazioni\Modelli amministrativi\Componenti\Servizi Desktop remoto\Host sessione Desktop remoto\Sicurezza                                                    | Richiedere l'autenticazione utente per l'accesso remoto usando NLA |
    | Impostazioni Keep-Alive                      | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | Configura intervallo keep-alive della connessione                                                 |
    | Impostazioni di riconnessione                       | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | Riconnetti automaticamente                                                                   |
    | Numero limitato di impostazioni di connessione | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | Limita il numero di connessioni                                                              |

## <a name="configure-windows-firewall-rules"></a>Configurare le regole del firewall di Windows
1. Attivare Windows Firewall nei tre profili (dominio, standard e pubblico):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Eseguire il comando seguente in PowerShell per consentire WinRM attraverso i tre profili firewall (dominio, privato e pubblico) e abilitare il servizio remoto di PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Abilitare le regole del firewall seguenti per consentire il traffico RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Abilitare la regola per la condivisione di file e stampanti, in modo che la macchina virtuale possa rispondere a un comando ping all'interno della rete virtuale:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Se la VM farà parte di un dominio, controllare i criteri di Azure AD seguenti per assicurarsi che le impostazioni precedenti non vengano ripristinate. 

    | Obiettivo                                 | Policy                                                                                                                                                  | Value                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Abilitare i profili di Windows Firewall | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Proteggi tutte le connessioni di rete         |
    | Abilitare RDP                           | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Consenti eccezioni per Desktop remoto in ingresso |
    |                                      | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall | Consenti eccezioni per Desktop remoto in ingresso |
    | Abilitare ICMP V4                       | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Consenti eccezioni ICMP                   |
    |                                      | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall | Consenti eccezioni ICMP                   |

## <a name="verify-the-vm"></a>Verificare la VM 

Assicurarsi che la macchina virtuale sia integra, sicura e accessibile tramite RDP: 

1. Per assicurarsi che il disco sia integro e coerente, controllare il disco al riavvio successivo della macchina virtuale:

    ```PowerShell
    Chkdsk /f
    ```
    Verificare che il report mostri un disco pulito e integro.

2. Configurare le impostazioni dei dati di configurazione di avvio. 

    > [!NOTE]
    > Usare una finestra di PowerShell con privilegi elevati per eseguire questi comandi.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Il log dump può essere utile per la risoluzione dei problemi di arresto anomalo di Windows. Abilitare la raccolta dei log di dump:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Verificare che il repository di Strumentazione gestione Windows (WMI) sia coerente:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se il repository è danneggiato, vedere [WMI: danneggiamento del repository](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Verificare che nessun'altra applicazione usi la porta 3389. Questa porta viene utilizzata per il servizio RDP in Azure. Per vedere quali porte vengono usate nella macchina virtuale, eseguire `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Per caricare un disco rigido virtuale Windows che è un controller di dominio:

   * Seguire [questi passaggi aggiuntivi](https://support.microsoft.com/kb/2904015) per preparare il disco.

   * Assicurarsi di essere a conoscenza della password della modalità ripristino servizi directory nel caso in cui sia necessario avviare la VM in modalità ripristino servizi directory in un determinato momento. Per ulteriori informazioni, vedere [impostare una password per la modalità ripristino servizi directory](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Assicurarsi di avere familiarità con l'account amministratore predefinito e la password. Potrebbe essere necessario reimpostare la password dell'amministratore locale corrente e verificare che sia possibile utilizzare questo account per accedere a Windows tramite la connessione RDP. Questa autorizzazione di accesso è controllata dall'oggetto Criteri di gruppo "Consenti accesso tramite Servizi Desktop remoto". Visualizzare questo oggetto nel Editor Criteri di gruppo locali:

    Configurazione computer\Impostazioni di Windows\Impostazioni di sicurezza\Criteri locali\Assegnazione diritti utente

8. Controllare i seguenti criteri di Azure AD per assicurarsi che l'accesso RDP non sia bloccato tramite RDP o dalla rete:

    - Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti utente\Impedisci accesso a questo computer dalla rete

    - Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti utente\Impedisci accesso tramite Servizi Desktop remoto


9. Controllare i seguenti criteri di Azure AD per assicurarsi di non rimuovere gli account di accesso necessari:

   - Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti Assignment\Access questo computer dalla rete

   Il criterio dovrebbe elencare i gruppi seguenti:

   - Amministratori

   - Operatori di backup

   - Tutti

   - Utenti

10. Riavviare la VM per assicurarsi che Windows sia ancora integro e che possa essere raggiunto tramite la connessione RDP. A questo punto, è possibile creare una macchina virtuale in Hyper-V locale per assicurarsi che la macchina virtuale venga avviata completamente. Eseguire quindi il test per assicurarsi che sia possibile raggiungere la macchina virtuale tramite RDP.

11. Rimuovere eventuali filtri di Transport Driver Interface aggiuntivi (TDI). Rimuovere, ad esempio, il software che analizza i pacchetti TCP o i firewall aggiuntivi. Se è necessario esaminarlo in un secondo momento, è possibile farlo dopo la distribuzione della macchina virtuale in Azure.

12. Disinstallare qualsiasi altro software o driver di terze parti correlato a componenti fisici o qualsiasi altra tecnologia di virtualizzazione.

### <a name="install-windows-updates"></a>Installare gli aggiornamenti di Windows
Idealmente, è consigliabile lasciare il computer aggiornato a *livello di patch*. Se ciò non è possibile, verificare che siano installati i seguenti aggiornamenti:

| Componente               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Archiviazione                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Rete                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Servizi Desktop remoto | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Sicurezza                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> Per evitare un riavvio accidentale durante il provisioning della macchina virtuale, è consigliabile assicurarsi che tutte le installazioni di Windows Update siano completate e che non siano presenti aggiornamenti in sospeso. Un modo per eseguire questa operazione consiste nell'installare tutti i possibili aggiornamenti di Windows e riavviare una volta prima di eseguire il comando Sysprep.

### Determinare quando utilizzare Sysprep<a id="step23"></a>    

System Preparation Tool (Sysprep) è un processo che è possibile eseguire per reimpostare un'installazione di Windows. Sysprep fornisce un'esperienza "predefinita" rimuovendo tutti i dati personali e reimpostando diversi componenti. 

In genere si esegue Sysprep per creare un modello da cui è possibile distribuire diverse altre VM con una configurazione specifica. Il modello è denominato *immagine generalizzata*.

Se si vuole creare una sola macchina virtuale da un disco, non è necessario usare Sysprep. È invece possibile creare la macchina virtuale da un' *immagine specializzata*. Per informazioni su come creare una macchina virtuale da un disco specializzato, vedere:

- [Creare una macchina virtuale da un disco specializzato](create-vm-specialized.md)
- [Creare una macchina virtuale da un disco rigido virtuale specializzato](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Se si desidera creare un'immagine generalizzata, è necessario eseguire Sysprep. Per ulteriori informazioni, vedere [How to use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx). 

Non tutti i ruoli o le applicazioni installate in un computer basato su Windows supportano immagini generalizzate. Quindi, prima di eseguire questa procedura, assicurarsi che Sysprep supporti il ruolo del computer. Per altre informazioni, vedere [Sysprep support for server roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Supporto di Sysprep per i ruoli server).

### <a name="generalize-a-vhd"></a>Generalizzare un disco rigido virtuale

>[!NOTE]
> Dopo aver eseguito `sysprep.exe` nei passaggi seguenti, spegnere la macchina virtuale. Non riattivarlo fino a quando non si crea un'immagine da esso in Azure.

1. Accedere alla VM Windows.
1. Eseguire il **prompt dei comandi** come amministratore. 
1. Modificare la directory in `%windir%\system32\sysprep`. Quindi eseguire `sysprep.exe`.
1. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.

    ![Utilità preparazione sistema](media/prepare-for-upload-vhd-image/syspre.png)
1. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
1. Selezionare **OK**.
1. Quando Sysprep termina, arresta la macchina virtuale. Non usare **Riavvia** per arrestare la macchina virtuale.

A questo punto il disco rigido virtuale è pronto per essere caricato. Per altre informazioni su come creare una macchina virtuale da un disco generalizzato, vedere [caricare un disco rigido virtuale generalizzato e usarlo per creare una nuova macchina virtuale in Azure](sa-upload-generalized.md).


>[!NOTE]
> Un file *Unattend. XML* personalizzato non è supportato. Sebbene supportiamo la proprietà `additionalUnattendContent`, che fornisce solo il supporto limitato per l'aggiunta di opzioni [Microsoft-Windows-Shell-Setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) al file *Unattend. XML* usato dall'agente di provisioning di Azure. È possibile usare, ad esempio, [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) per aggiungere FirstLogonCommands e LogonCommands. Per altre informazioni, vedere [esempio di AdditionalUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Completare le configurazioni consigliate
Le impostazioni seguenti non influiscono sul caricamento del disco rigido virtuale. È tuttavia fortemente consigliabile configurarle.

* Installare l' [agente di macchine virtuali di Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sarà quindi possibile abilitare le estensioni delle VM. Le estensioni VM implementano la maggior parte delle funzionalità critiche che è possibile usare con le macchine virtuali. Sono necessarie le estensioni, ad esempio, per reimpostare le password o configurare RDP. Per altre informazioni, vedere [Panoramica dell'agente di macchine virtuali di Azure](../extensions/agent-windows.md).
* Dopo aver creato la macchina virtuale in Azure, è consigliabile inserire il file di paging nel *volume dell'unità temporale* per migliorare le prestazioni. È possibile configurare il posizionamento dei file come indicato di seguito:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Se un disco dati è collegato alla macchina virtuale, la lettera del volume dell'unità temporale è in genere *D*. Questa designazione potrebbe essere diversa, a seconda delle impostazioni e del numero di unità disponibili.
  * Si consiglia di disabilitare i blocchi di script che potrebbero essere forniti da software antivirus. Potrebbero interferire e bloccare gli script degli agenti di provisioning di Windows eseguiti quando si distribuisce una nuova macchina virtuale dall'immagine.
  
## <a name="next-steps"></a>Passaggi successivi
* [Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager](upload-generalized-managed.md)
* [Risolvere i problemi di attivazione della macchina virtuale Windows di Azure](troubleshoot-activation-problems.md)

