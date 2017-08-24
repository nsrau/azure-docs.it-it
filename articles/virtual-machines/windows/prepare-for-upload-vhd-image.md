---
title: Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure | Microsoft Docs
description: Come preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 91c443f6d9998f15f4b1b1eaad33f0a319fcf4d4
ms.contentlocale: it-it
ms.lasthandoff: 08/02/2017

---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure
Prima di caricare una macchina virtuale di Windows dall'ambiente locale a Microsoft Azure, è necessario preparare il disco rigido virtuale, VHD o VHDX. Azure supporta solo macchine virtuali di prima generazione nel formato di file VHD e con un disco a dimensione fissa. La dimensione massima consentita per il disco rigido virtuale è 1023 GB. È possibile convertire una VM di prima generazione dal file system VHDX a VHD e da un disco a espansione dinamica a un disco a dimensione fissa. Non è tuttavia possibile modificare la generazione di una macchina virtuale. Per altre informazioni, vedere [Should I create a generation 1 or 2 VM in Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) (Creare una macchina virtuale di prima o seconda generazione in Hyper-V).

Per altre informazioni sui criteri di supporto per le VM di Azure, vedere [Supporto di software server Microsoft per le macchine virtuali Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Le istruzioni illustrate in questo articolo si applicano alla versione a 64 bit di Windows Server 2008 R2 e alle versioni successive del sistema operativo Windows Server. Per informazioni sull'esecuzione di sistemi operativi a 32 bit in Azure, vedere [Support for 32-bit operating systems in Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines) (Supporto per sistemi operativi a 32 bit in macchine virtuali di Azure).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Convertire il disco virtuale in formato VHD e disco di dimensioni prestabilite 
Se è necessario convertire il disco virtuale in un formato richiesto per Azure, usare uno dei metodi descritti in questa sezione. Eseguire il backup della macchina virtuale prima di convertire il disco virtuale e assicurarsi che il disco rigido virtuale di Windows funzioni correttamente nel server locale. Risolvere qualsiasi errore nella macchina virtuale prima di provare a convertire o caricare il disco in Azure.

Dopo avere convertito il disco, creare una macchina virtuale che utilizzi il disco convertito. Avviare e accedere alla macchina virtuale per terminare la preparazione della macchina virtuale per il caricamento.

### <a name="convert-disk-using-hyper-v-manager"></a>Convertire il disco tramite la console di gestione di Hyper-V
1. Aprire la console di gestione di Hyper-V e selezionare il computer locale a sinistra. Nel menu sopra l'elenco di computer fare clic su **Azione** > **Modifica disco**.
2. Nella schermata **Percorso disco rigido virtuale** trovare e selezionare il disco virtuale.
3. Nella schermata **Scelta azione** selezionare **Converti** e **Avanti**.
4. Se è necessaria la conversione dal formato VHDX, selezionare **VHD** e quindi fare clic su **Avanti**
5. Se è necessaria la conversione dal disco a espansione dinamica, selezionare **A dimensione fissa** e fare clic su **Avanti**
6. Trovare e selezionare un percorso in cui salvare il nuovo file VHD.
7. Fare clic su **Fine**.

>[!NOTE]
>I comandi riportati in questo articolo devono essere eseguiti in una sessione di PowerShell con privilegi elevati.

### <a name="convert-disk-by-using-powershell"></a>Convertire il disco tramite PowerShell
È possibile convertire un disco virtuale usando il comando [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) di Windows PowerShell. Selezionare **Esegui come amministratore** quando si avvia PowerShell. 

Nell'esempio seguente viene eseguita la conversione da VHDX a VHD e da un disco a espansione dinamica a un disco a dimensione fissa:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
In questo comando sostituire il valore di "-Path" con il percorso del disco rigido virtuale che si vuole convertire e il valore di "-DestinationPath" con il nuovo percorso e il nome del disco convertito.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Conversione dal formato VMware VMDK
Se si ha un'immagine di VM Windows in [formato di file VMDK](https://en.wikipedia.org/wiki/VMDK), convertirla in formato VHD usando [Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497). Per altre informazioni, vedere l'articolo del blog [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Come convertire un file VMDK VMWare in un file VHD Hyper-V).

## <a name="set-windows-configurations-for-azure"></a>Impostare le configurazioni di Windows per Azure

Nella macchina virtuale che si prevede di caricare in Azure eseguire tutti questi comandi in una [finestra del prompt dei comandi con privilegi elevati](https://technet.microsoft.com/library/cc947813.aspx):

1. Rimuovere qualsiasi route statica persistente nella tabella di routing:
   
   * Per visualizzare la tabella di route, eseguire `route print` al prompt dei comandi.
   * Controllare le sezioni **Route persistenti** sezioni. Se è presente una route persistente, utilizzare il comando [Elimina route](https://technet.microsoft.com/library/cc739598.apx) per rimuoverla.
2. Rimuovere il proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Impostare il criterio SAN disco su [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```PowerShell
    diskpart 
    ```
    Nella finestra del prompt dei comandi aperta eseguire questi comandi:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Impostare l'ora UTC (Coordinated Universal Time) per Windows e impostare il tipo di avvio del servizio ora di Windows (w32time) su **Automatico**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. Impostare il profilo di potenza su **Prestazioni elevate**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Verificare i servizi di Windows
Assicurarsi che ciascuno dei seguenti servizi di Windows sia impostato sui **valori predefiniti di Windows**, Si tratta del numero minimo di servizi che devono essere configurati perché la macchina virtuale abbia connettività. Eseguire i comandi seguenti per reimpostare le impostazioni di avvio:
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>Aggiornare le impostazioni del registro di Desktop remoto
Assicurarsi che le impostazioni seguenti siano configurate correttamente per la connessione Desktop remoto:

>[!Note] 
>Si potrebbe ricevere un messaggio di errore quando si esegue il comando **Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;nome oggetto&gt; &lt;valore&gt;** in questi passaggi. È possibile ignorare questo messaggio di errore. Significa solo che il dominio non esegue il push della configurazione tramite un oggetto Criteri di gruppo.
>
>

1. Remote Desktop Protocol (RDP) è abilitato:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. La porta RDP è impostata correttamente. La porta predefinita è 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    Quando si distribuisce una VM, le regole predefinite vengono create per la porta 3389. Se si vuole modificare il numero di porta, eseguire l'operazione dopo aver distribuito la VM in Azure.

3. Il listener è in ascolto in ogni interfaccia di rete:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Configurare la modalità Autenticazione a livello di rete per le connessioni RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. Impostare il valore keep-alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. Ristabilire la connessione:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. Limitare il numero di connessioni simultanee:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Rimuovere eventuali certificati autofirmati associati al listener del protocollo RDP, se presenti:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    Sarà così possibile connettersi all'inizio, quando si distribuisce la VM. Questa operazione può anche essere eseguita dopo aver distribuito la VM in Azure, se necessario.

9. Se la VM farà parte di un dominio, verificare tutte le impostazioni seguenti per assicurarsi che le impostazioni precedenti non vengano ripristinate. I criteri da verificare sono i seguenti:
    
    - RDP è abilitato:

         Configurazione computer\Criteri\Impostazioni di Windows \Modelli amministrativi\ Componenti\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni:
         
         **Consenti la connessione remota tramite Servizi Desktop remoto** 

    - Criteri di gruppo NLA:

        Impostazioni\Modelli amministrativi\ Componenti\Servizi Desktop remoto\Host sessione Desktop remoto\Sicurezza: 
        
        **Richiedi autenticazione utente tramite Autenticazione a livello di rete per le connessioni remote**
    
    - Impostazioni Keep-alive:

        Configurazione computer\Criteri\Impostazioni di Windows \Modelli amministrativi\ Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni: 
        
        **Configura intervallo keep-alive della connessione**

    - Impostazioni di riconnessione:

        Configurazione computer\Criteri\Impostazioni di Windows \Modelli amministrativi\ Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni: 
        
        **Riconnessione automatica**

    - Limitare il numero di impostazioni di connessione:

        Configurazione computer\Criteri\Impostazioni di Windows \Modelli amministrativi\ Componenti di Windows\Servizi Desktop remoto\Host sessione Desktop remoto\Connessioni: 
        
        **Limita il numero di connessioni**

## <a name="configure-windows-firewall-rules"></a>Configurare le regole del firewall di Windows
1. Attivare Windows Firewall nei tre profili (Dominio, Standard e Pubblico):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. Eseguire questo comando in PowerShell per consentire WinRM attraverso i tre profili firewall (Dominio, Privato e Pubblico) e abilitare il servizio remoto di PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. Abilitare le regole del firewall seguenti per consentire il traffico RDP 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. Abilitare la regola Condivisione file e stampanti in modo che la VM possa rispondere a un comando ping all'interno della rete virtuale:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Se la VM farà parte di un dominio, verificare le impostazioni seguenti per assicurarsi che le impostazioni precedenti non vengano ripristinate. I criteri AD da verificare sono i seguenti:

    - Abilitare i profili di Windows Firewall

        Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall: **proteggi tutte le connessioni di rete**

       Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall: **proteggi tutte le connessioni di rete**

    - Abilitare RDP 

        Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall: **consenti eccezioni per Desktop remoto in ingresso**

        Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall: **consenti eccezioni per Desktop remoto in ingresso**

    - Abilitare ICMP V4

        Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo di dominio\Windows Firewall: **consenti eccezioni ICMP**

        Configurazione computer\Criteri\Impostazioni di Windows\Modelli amministrativi\Rete\Connessione di rete\Windows Firewall\Profilo standard\Windows Firewall: **consenti eccezioni ICMP**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Verificare che la macchina virtuale sia integra, sicura e accessibile con RDP 
1. Per verificare l'integrità e la coerenza del disco, eseguire un controllo disco al successivo riavvio della VM:

    ```PowerShell
    Chkdsk /f
    ```
    Assicurarsi che il report indichi un disco integro e pulito.

2. Configurare le impostazioni dei dati di configurazione di avvio. Eseguire i seguenti comandi in una finestra CMD con privilegi elevati:
   
   ```PowerShell
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Verificare che il repository di Strumentazione gestione Windows sia coerente. A tale scopo, usare il comando seguente:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se il repository è danneggiato, vedere [WMI: Repository Corruption, or Not?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not) (WMI: il repository è davvero danneggiato?)

4. Assicurarsi che un'altra applicazione non stia usando la porta 3389. Questa porta viene utilizzata per il servizio RDP in Azure. È possibile eseguire **netstat -anob** per verificare quali porte sono in uso nella macchina virtuale:

    ```PowerShell
    netstat -anob
    ```

5. Se il disco VHD di Windows che si vuole caricare è un controller di dominio, seguire questa procedura:

    R. Seguire [questi passaggi aggiuntivi](https://support.microsoft.com/kb/2904015) per preparare il disco.

    B. È necessario conoscere la password DSRM, nel caso in cui si debba avviare la VM in modalità DSRM in un determinato momento. Vedere questo collegamento per impostare la [password DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

6. L'account predefinito Administrator e la password devono essere noti all'utente. È opportuno reimpostare la password dell'amministratore locale corrente e accertarsi di poter usare questo account per accedere a Windows tramite la connessione RDP. Questa autorizzazione di accesso è controllata dall'oggetto Criteri di gruppo "Consenti accesso tramite Servizi Desktop remoto", È possibile visualizzare questo oggetto in Editor criteri di gruppo locali in:

    Configurazione computer\Impostazioni di Windows\Impostazioni di sicurezza\Criteri locali\Assegnazione diritti utente

7. Controllare i seguenti criteri AD per assicurarsi che non si stia bloccando l'accesso a RDP né tramite RDP né dalla rete:

    - Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti utente\Impedisci accesso a questo computer dalla rete

    - Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti utente\Impedisci accesso tramite Servizi Desktop remoto


8. Riavviare la VM per assicurarsi che Windows sia ancora integro e che possa essere raggiunto con la connessione RDP. A questo punto può essere opportuno creare una VM nell'ambiente Hyper-V locale per assicurarsi che la macchina virtuale si avvi completamente e quindi testare se è raggiungibile con la connessione RDP.

9. Rimuovere qualsiasi filtro TDI (Transport Driver Interface) aggiuntivo, ad esempio il software che analizza i pacchetti TCP o altri firewall. Questa operazione può anche essere eseguita dopo aver distribuito la VM in Azure, se necessario.

10. Disinstallare qualsiasi altro software di terze parti e i driver relativi a componenti fisici o altre tecnologie di virtualizzazione.

### <a name="install-windows-updates"></a>Installare gli aggiornamenti di Windows
La configurazione ideale è **avere la versione più recente del livello di patch del computer**. Se ciò non è possibile, assicurarsi di aver installato i seguenti aggiornamenti:

|                       |                   |           |                                       Versione minima del file x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| Componente               | Binary            | Windows 7 e Windows Server 2008 R2 | Windows 8 e Windows Server 2012             | Windows 8.1 e Windows Server 2012 R2 | Windows 10 e Windows Server 2016 RS1 | Windows 10 RS2             |
| Archiviazione                 | disk.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061           | -                                    | -                          |
|                         | storport.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.332             |
|                         | ntfs.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 - KB3125574         | 6.2.9200.16384 - KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614           | 10.0.14393.953 - KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384           | -                                    | 10.0.15063.0               |
|                         | partmgr.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726           | 10.0.14393.1066 - KB4022715          | 10.0.15063.447             |
|                         | Msdsm.sys         | 6.1.7601.23403 - KB3125574         | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726           | -                                    | -                          |
|                         | Mpio.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726           | 10.0.14393.1198 - KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614           | 10.0.14393.576 - KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614           | 10.0.14393.206 - KB4022715           | -                          |
| Rete                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 - KB4022715          | 10.0.15063.250 - KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726           | 10.0.14393.479 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb.sys        | 6.1.7601.23816 - KB4022722         | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | tcpip.sys         | 6.1.7601.23761 - KB4022722         | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.447             |
|                         | http.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726           | 10.0.14393.251 - KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 - KB4022719         | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.138             |
| Core                    | ntoskrnl.exe      | 6.1.7601.23807 - KB4022719         | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.483             |
| Servizi Desktop remoto | rdpcorets.dll     | 6.2.9200.21506 - KB4022719         | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.0               |
|                         | termsrv.dll       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850           | 10.0.14393.0 - KB4022715             | 10.0.15063.0               |
|                         | termdd.sys        | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | win32k.sys        | 6.1.7601.23807 - KB4022719         | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726           | 10.0.14393.594 - KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | rdpwd.sys         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
| Sicurezza                | A causa di WannaCrypt | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### Quando usare sysprep <a id="step23"></a>    

Sysprep è un processo che può essere eseguito in un'installazione di Windows e che reimposta l'installazione del sistema e fornisce un'esperienza predefinita rimuovendo tutti i dati personali e reimpostando diversi componenti. Questo processo viene generalmente eseguito se si vuole creare un modello dal quale si possono distribuire diverse altre macchine virtuali che hanno una configurazione specifica. Il modello è denominato **immagine generalizzata**.

Se si vuole invece creare una sola macchina virtuale da un disco, non è necessario usare Sysprep. In questo caso si può semplicemente creare la macchina virtuale da ciò che viene definita una **immagine specializzata**.

Per altre informazioni sulla creazione di una VM da un disco specializzato, vedere:

- [Creare una macchina virtuale da un disco specializzato](create-vm-specialized.md)
- [Creare una macchina virtuale da un disco rigido virtuale specializzato](https://azure.microsoft.com/resources/templates/201-vm-specialized-vhd/)

Se si vuole creare un'immagine generalizzata, è necessario eseguire sysprep. Per altre informazioni su Sysprep, vedere [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx) (Introduzione all'uso di Sysprep). 

Non tutti i ruoli o le applicazioni installate in un computer basato su Windows supportano questa generalizzazione. Prima di eseguire questa procedura, vedere l'articolo seguente per verificare che il ruolo del computer sia supportato da Sysprep. Per altre informazioni, vedere [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Supporto Sysprep per i ruoli server).

### <a name="steps-to-generalize-a-vhd"></a>Procedura per generalizzare un disco rigido virtuale

>[!NOTE]
> Dopo l'esecuzione di sysprep.exe come specificato nei passaggi seguenti, disattivare la macchina virtuale e non riattivarla prima di averne creato un'immagine da Azure.

1. Accedere alla VM Windows.
2. Eseguire il **prompt dei comandi** come amministratore. 
3. Passare a **%windir%\system32\sysprep** ed eseguire **sysprep.exe**.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.

    ![Utilità preparazione sistema](media/prepare-for-upload-vhd-image/syspre.png)
4. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
5. Fare clic su **OK**.
6. Al termine dell'esecuzione di Sysprep, arrestare la VM. Non usare **Riavvia** per arrestare la macchina virtuale.
7. A questo punto il disco rigido virtuale è pronto per essere caricato. Per altre informazioni su come creare una VM da un disco generalizzato, vedere [Caricare un disco rigido virtuale generalizzato in Azure e creare una nuova macchina virtuale](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Completare le configurazioni consigliate
Le seguenti impostazioni non influenzano il caricamento del disco rigido virtuale. È tuttavia fortemente consigliabile configurarle.

* Installare l'[agente di macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sarà quindi possibile abilitare le estensioni delle VM. Le estensioni delle VM implementano la maggior parte delle funzionalità critiche da usare con le macchine virtuali, come la reimpostazione delle password, la configurazione di RDP e così via. Per altre informazioni, vedere:

    - [Agente ed estensioni delle VM - Parte 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Agente ed estensioni delle VM - Parte 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)
* Il log Dump può essere utile per la risoluzione dei problemi di arresto anomalo del sistema Windows. Abilitare la raccolta di log Dump:
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    Se si ricevono errori durante i passaggi procedurali di questo articolo, significa che le chiavi del Registro di sistema esistono già. In questo caso, usare invece i comandi seguenti:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  Dopo aver creato la VM in Azure, è consigliabile inserire il file di paging nel volume della "unità temporale" per migliorare le prestazioni. A tale scopo, procedere come segue:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
Se è presente un disco dati collegato alla macchina virtuale, la lettera del volume dell'unità temporale è in genere "D". Questa designazione può differire a seconda del numero di unità disponibili e delle impostazioni.

## <a name="next-steps"></a>Passaggi successivi
* [Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager](upload-generalized-managed.md)


