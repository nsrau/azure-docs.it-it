---
title: Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure | Microsoft Docs
description: Informazioni su come preparare un disco rigido virtuale Windows o VHDX per caricarlo in Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: cc942aeb34d17e8dff064c6a21a3c7b2099c742a
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151023"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure

Prima di caricare una macchina virtuale di Windows (VM) da locale ad Azure, è necessario preparare il disco rigido virtuale (VHD o VHDX). Azure supporta la generazione 1 e generazione 2 macchine virtuali che sono nel formato di file di disco rigido virtuale e che dispone di un disco a dimensione fissa. La dimensione massima consentita per il disco rigido virtuale è 1023 GB. 

In una generazione 1 macchina virtuale, è possibile convertire un file system VHDX a VHD. È anche possibile convertire un disco a espansione dinamica a un disco a dimensione fissa. Non è tuttavia possibile modificare la generazione di una macchina virtuale. Per altre informazioni, vedere [è necessario creare una generazione 1 o 2 macchine Virtuali in Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) e [supporto tecnico di Azure per la generazione 2 macchine virtuali (anteprima)](generation-2.md).

Per informazioni sui criteri di supporto per macchine virtuali di Azure, vedere [supporto di software server Microsoft per macchine virtuali di Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Le istruzioni in questo articolo si applicano alla versione a 64 bit di Windows Server 2008 R2 e versioni successive i sistemi operativi Windows Server. Per informazioni sull'esecuzione di un sistema operativo a 32 bit in Azure, vedere [supporto per i sistemi operativi a 32 bit in macchine virtuali di Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Convertire il disco virtuale in una dimensione fissa e a disco rigido virtuale 
Se è necessario convertire il disco virtuale in un formato richiesto per Azure, usare uno dei metodi descritti in questa sezione. Eseguire il backup della macchina virtuale prima di convertire il disco virtuale. Assicurarsi che il disco rigido virtuale di Windows funzioni correttamente nel server locale. Prima di provare a convertire o caricarlo in Azure, quindi risolvere gli eventuali errori all'interno della macchina virtuale stessa.

Dopo avere convertito il disco, creare una VM che usa il disco. Avviare e accedere alla macchina virtuale per completare la preparazione per il caricamento.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Utilizzare Hyper-V Manager per convertire il disco 
1. Aprire la console di gestione di Hyper-V e selezionare il computer locale a sinistra. Nel menu sopra l'elenco di computer, selezionare **azione** > **modifica disco**.
2. Nel **percorso disco rigido virtuale** pagina, selezionare il disco virtuale.
3. Nel **scelta azione** pagina, selezionare **convertire** > **Avanti**.
4. Se è necessaria la conversione dal formato VHDX, selezionare **VHD** > **successivo**.
5. Se è necessario eseguire la conversione da un disco a espansione dinamica, selezionare **a dimensione fissa** > **successivo**.
6. Trovare e selezionare un percorso in cui salvare il nuovo file VHD.
7. Selezionare **Fine**.

> [!NOTE]
> Usare una sessione di PowerShell con privilegi elevata per eseguire i comandi in questo articolo.

### <a name="use-powershell-to-convert-the-disk"></a>Usare PowerShell per convertire il disco 
È possibile convertire un disco virtuale usando il comando [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) di Windows PowerShell. Selezionare **Esegui come amministratore** quando si avvia PowerShell. 

Il comando seguente converte il disco dal formato VHDX in VHD. Inoltre, il comando converte il disco da un disco a espansione dinamica a un disco a dimensione fissa.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

In questo comando, sostituire il valore di `-Path` con il percorso per il disco rigido virtuale che si desidera convertire. Sostituire il valore di `-DestinationPath` con il nuovo percorso e il nome del disco convertito.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Conversione dal formato VMware VMDK
Se si dispone di un'immagine di macchina virtuale Windows [formato di file VMDK](https://en.wikipedia.org/wiki/VMDK), utilizzare il [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) possa essere convertito in formato VHD. Per altre informazioni, vedere [come convertire un VMDK di VMware in Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Impostare le configurazioni di Windows per Azure

Nella macchina virtuale che si intende caricare in Azure, eseguire i comandi seguenti da un [finestra prompt dei comandi con privilegi elevati](https://technet.microsoft.com/library/cc947813.aspx):

1. Rimuovere qualsiasi route statica persistente nella tabella di routing:
   
   * Per visualizzare la tabella di route, eseguire `route print` al prompt dei comandi.
   * Controllare il `Persistence Routes` sezioni. Se è presente una route persistente, Usa il `route delete` comando per rimuoverlo.
2. Rimuovere il proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Se la macchina virtuale deve usare un proxy specifico, aggiungere un'eccezione del proxy per l'indirizzo IP di Azure ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) in modo che la macchina virtuale può connettersi ad Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Impostare il criterio SAN disco su [ `Onlineall` ](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    Nella finestra del prompt dei comandi aperta, digitare i comandi seguenti:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Impostare ora Coordinated Universal Time (UTC) per Windows. Impostare anche il tipo di avvio del servizio ora di Windows (`w32time`) a `Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Impostare il profilo di potenza su ad alte prestazioni:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Assicurarsi che le variabili di ambiente `TEMP` e `TMP` sono impostate sui valori predefiniti:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Verificare i servizi di Windows
Assicurarsi che ciascuno dei seguenti servizi di Windows sia impostato sui valori predefiniti di Windows. Questi servizi sono il valore minimo che deve essere impostato per assicurare la connettività della macchina virtuale. Eseguire i comandi seguenti per reimpostare le impostazioni di avvio:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Aggiornare le impostazioni del Registro di sistema di desktop remoto
Assicurarsi che le impostazioni seguenti siano configurate correttamente per l'accesso remoto:

>[!NOTE] 
>Si potrebbe ricevere un messaggio di errore quando si esegue `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`. È possibile ignorare questo messaggio. Significa solo che il dominio non è eseguire il push di tale configurazione tramite un oggetto Criteri di gruppo.

1. Remote Desktop Protocol (RDP) è abilitato:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. La porta RDP è configurata correttamente. La porta predefinita è 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Quando si distribuisce una VM, le regole predefinite vengono create per la porta 3389. Se si desidera modificare il numero di porta, è possibile farlo dopo aver distribuita la macchina virtuale in Azure.

3. Il listener è in ascolto in ogni interfaccia di rete:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Configurare la modalità di autenticazione a livello di rete (NLA) per le connessioni RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Impostare il valore keep-alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Ristabilire la connessione:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Limitare il numero di connessioni simultanee:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Rimuovere eventuali certificati autofirmati associati al listener del protocollo RDP:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Questo codice garantisce che sia possibile connettersi all'inizio quando si distribuisce la macchina virtuale. Se è necessario esaminare questo in un secondo momento, è possibile farlo dopo aver distribuita la macchina virtuale in Azure.

9. Se la VM farà parte di un dominio, controllare i criteri seguenti per assicurarsi che le impostazioni precedenti non vengono ripristinate. 
    
    | Obiettivo                                     | Policy                                                                                                                                                       | Value                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP è abilitato                           | Configurazione computer\Criteri\Impostazioni di Windows \Modelli amministrativi\Componenti\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni         | Consenti la connessione remota tramite Servizi Desktop remoto                                  |
    | Criteri di gruppo NLA                         | Impostazioni\Modelli amministrativi\Componenti\Servizi Desktop remoto\Host sessione Desktop remoto\Sicurezza                                                    | Richiedi autenticazione utente per l'accesso remoto mediante la funzione NLA |
    | Impostazioni Keep-alive                      | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | Configura intervallo keep-alive della connessione                                                 |
    | Impostazioni di riconnessione                       | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | La riconnessione automatica                                                                   |
    | Numero limitato di impostazioni di connessione | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni | Limita il numero di connessioni                                                              |

## <a name="configure-windows-firewall-rules"></a>Configurare le regole del firewall di Windows
1. Attiva Windows Firewall nei tre profili (dominio, standard e pubblico):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Eseguire il comando seguente in PowerShell per consentire WinRM attraverso i tre profili firewall (dominio, privato e pubblico) e abilitare il servizio remoto PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Abilitare le regole del firewall seguenti per consentire il traffico RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Abilitare la regola per file e stampanti in modo che la VM possa rispondere a un comando ping all'interno della rete virtuale:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Se la VM farà parte di un dominio, controllare i criteri di Azure AD seguenti per assicurarsi che le impostazioni precedenti non vengono ripristinate. 

    | Obiettivo                                 | Policy                                                                                                                                                  | Value                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Abilitare i profili di Windows Firewall | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Proteggi tutte le connessioni di rete         |
    | Abilitare RDP                           | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Consenti eccezioni per Desktop remoto in ingresso |
    |                                      | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall | Consenti eccezioni per Desktop remoto in ingresso |
    | Abilitare ICMP V4                       | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall   | Consenti eccezioni ICMP                   |
    |                                      | Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall | Consenti eccezioni ICMP                   |

## <a name="verify-the-vm"></a>Verificare la VM 

Assicurarsi che la macchina virtuale sia integra, sicura e RDP accessibili: 

1. Per assicurarsi che il disco sia integro e coerente, controllare il disco al successivo riavvio della macchina virtuale:

    ```PowerShell
    Chkdsk /f
    ```
    Assicurarsi che il report indichi un disco integro e pulito.

2. Configurare le impostazioni dei dati di configurazione di avvio. 

    > [!NOTE]
    > Usare una finestra di PowerShell con privilegi elevata per eseguire questi comandi.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. Il log dump può essere utile nella risoluzione dei problemi di arresto anomalo del sistema di Windows. Abilitare la raccolta di log dump:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Verificare che il repository di Strumentazione gestione Windows (WMI) sia coerenza:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se il repository è danneggiato, vedere [WMI: Archivio danneggiato o non](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Assicurarsi che nessun altra applicazione sta utilizzando la porta 3389. Questa porta viene utilizzata per il servizio RDP in Azure. Per visualizzare le porte utilizzate nella macchina virtuale, eseguire `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Per caricare un disco rigido virtuale Windows che è un controller di dominio:

   * Seguire [questi passaggi aggiuntivi](https://support.microsoft.com/kb/2904015) per preparare il disco.

   * Assicurarsi di conoscere la password modalità ripristino servizi Directory (DSRM) nel caso in cui è necessario avviare la macchina virtuale in modalità ripristino servizi directory in un determinato momento. Per altre informazioni, vedere [impostare una password DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Assicurarsi di conoscere l'account predefinito administrator e la password. È possibile reimpostare la password di amministratore locale corrente e verificare che è possibile usare questo account per accedere a Windows tramite la connessione RDP. Questa autorizzazione di accesso è controllata dall'oggetto Criteri di gruppo "Consenti accesso tramite Servizi Desktop remoto". Visualizzare questo oggetto in locale Editor criteri di gruppo di seguito:

    Configurazione computer\Impostazioni di Windows\Impostazioni di sicurezza\Criteri locali\Assegnazione diritti utente

8. Controllare i criteri di Azure AD seguenti per assicurarsi che non siano bloccate l'accesso a RDP tramite RDP o dalla rete:

    - Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti utente\Impedisci accesso a questo computer dalla rete

    - Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti utente\Impedisci accesso tramite Servizi Desktop remoto


9. Controllare i criteri di Azure AD seguenti per assicurarsi che non si elimina uno qualsiasi degli account di accesso necessario:

   - Computer Configurazione computer\Impostazioni Windows\Impostazioni sicurezza\Criteri locali\Assegnazione diritti Assignment\Access questo computer dalla rete

   Il criterio deve elencare i gruppi seguenti:

   - Administrators

   - Operatori di backup

   - Tutti

   - Utenti

10. Riavviare la macchina virtuale per assicurarsi che Windows sia ancora integro e che può essere raggiunto tramite la connessione RDP. A questo punto, si potrebbe voler creare una macchina virtuale di Hyper-V locale per assicurarsi che la macchina virtuale inizia completamente. Quindi verificare che è possibile raggiungere la macchina virtuale tramite RDP.

11. Rimuovere qualsiasi filtro TDI interfaccia TDI (Transport Driver). Ad esempio, rimuovere il software che analizza TCP pacchetti o altri firewall. Se è necessario esaminare questo in un secondo momento, è possibile farlo dopo aver distribuita la macchina virtuale in Azure.

12. Disinstallare qualsiasi altro software di terze parti o driver i componenti correlati a fisici o altre tecnologie di virtualizzazione.

### <a name="install-windows-updates"></a>Installare gli aggiornamenti di Windows
In teoria, è consigliabile mantenere aggiornato il computer di *livello di patch*. Se questo non è possibile, verificare che siano installati gli aggiornamenti seguenti:

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
| Security                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Determinare quando usare Sysprep <a id="step23"></a>    

Utilità preparazione sistema (Sysprep) è un processo è possibile eseguire per ripristinare un'installazione di Windows. Sysprep offre un'esperienza "predefiniti" rimuovendo tutti i dati personali e reimpostando diversi componenti. 

È in genere eseguire Sysprep per creare un modello da cui è possibile distribuire diverse altre macchine virtuali che hanno una configurazione specifica. Il modello viene chiamato un *immagine generalizzata*.

Se si desidera creare solo una macchina virtuale da un disco, non devi utilizzare Sysprep. In alternativa, è possibile creare la macchina virtuale da un *immagine specializzata*. Per informazioni su come creare una macchina virtuale da un disco specializzato, vedere:

- [Creare una macchina virtuale da un disco specializzato](create-vm-specialized.md)
- [Creare una macchina virtuale da un disco rigido virtuale specializzato](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Se si desidera creare un'immagine generalizzata, è necessario eseguire Sysprep. Per altre informazioni, vedere [come usare Sysprep: Un'introduzione](https://technet.microsoft.com/library/bb457073.aspx). 

Non tutti i ruoli o applicazione installata in un computer basato su Windows supporta immagini generalizzate. Pertanto, prima di eseguire questa procedura, verificare che Sysprep supporta il ruolo del computer. Per altre informazioni, vedere [Sysprep support for server roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Supporto di Sysprep per i ruoli server).

### <a name="generalize-a-vhd"></a>Generalizzare un disco rigido virtuale

>[!NOTE]
> Dopo aver eseguito `sysprep.exe` nei passaggi seguenti, spegnere la macchina virtuale. Non riaccendere il computer finché non si crea un'immagine da quest'ultimo in Azure.

1. Accedere alla VM Windows.
1. Eseguire il **prompt dei comandi** come amministratore. 
1. Passare alla directory `%windir%\system32\sysprep`. Quindi eseguire `sysprep.exe`.
1. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.

    ![Utilità preparazione sistema](media/prepare-for-upload-vhd-image/syspre.png)
1. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
1. Selezionare **OK**.
1. Al termine, Sysprep arresta la macchina virtuale. Non usare **riavviare** per arrestare la macchina virtuale.

A questo punto il disco rigido virtuale è pronto per essere caricato. Per altre informazioni su come creare una macchina virtuale da un disco generalizzato, vedere [caricare un disco rigido virtuale generalizzato e usarlo per creare una nuova macchina virtuale in Azure](sa-upload-generalized.md).


>[!NOTE]
> Una classe personalizzata *Unattend. XML* file non è supportato. Anche se è supportata la `additionalUnattendContent` proprietà, che fornisce solo un supporto limitato per l'aggiunta [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) opzioni nel *Unattend. XML* file che il provisioning di Azure Agent utilizza. È possibile usare, ad esempio, [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) aggiungere FirstLogonCommands e LogonCommands. Per altre informazioni, vedere [additionalUnattendContent FirstLogonCommands esempio](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Completare le configurazioni consigliate
Le seguenti impostazioni non influiscono sul caricamento di VHD. È tuttavia fortemente consigliabile configurarle.

* Installare il [agente di macchine virtuali di Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sarà quindi possibile abilitare le estensioni delle VM. Le estensioni delle VM implementano la maggior parte delle funzionalità critiche che è possibile usare con le macchine virtuali. È necessario le estensioni, ad esempio, per reimpostare le password o configurare RDP. Per altre informazioni, vedere [panoramica dell'agente di macchine virtuali di Azure](../extensions/agent-windows.md).
* Dopo aver creato la macchina virtuale in Azure, è consigliabile archiviare il file di paging nel *volume unità temporale* per migliorare le prestazioni. È possibile impostare il posizionamento di file come indicato di seguito:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Se un disco dati è collegato alla macchina virtuale, in genere è la lettera di volume dell'unità temporale *1!d*. Questa designazione può differire a seconda delle impostazioni e il numero di unità disponibili.

## <a name="next-steps"></a>Passaggi successivi
* [Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager](upload-generalized-managed.md)
* [Risolvere i problemi di attivazione della macchina virtuale Windows di Azure](troubleshoot-activation-problems.md)

