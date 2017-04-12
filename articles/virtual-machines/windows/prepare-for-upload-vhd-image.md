---
title: Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure | Microsoft Docs
description: Come preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: glimoli;genli
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 104063500a33dfe55c56467517a5002c562772df
ms.lasthandoff: 03/31/2017


---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure
Per caricare una macchina virtuale di Windows da una posizione locale ad Azure, è necessario preparare il disco o VHDX. Azure supporta solo macchine virtuali di prima generazione nel formato di file VHD che dispongono di un disco dalle dimensioni prestabilite. La dimensione massima consentita per il disco rigido virtuale è 1023 GB. È possibile convertire una macchina virtuale di prima generazione dal formato VHDX al formato VHD ed espandendo in maniera dinamica in un disco di dimensioni prestabilite. Tuttavia non è possibile modificare la generazione di una macchina virtuale. Per ulteriori informazioni, vedere [Creare una macchina virtuale di prima o seconda generazione in Hyper-V](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Convertire il disco virtuale in formato VHD e disco di dimensioni prestabilite 
Se è necessario convertire il disco virtuale in un formato richiesto per Azure, usare uno dei metodi descritti in questa sezione. Eseguire il backup della macchina virtuale prima di convertire il disco virtuale e assicurarsi che il disco rigido virtuale di Windows funzioni correttamente nel server locale. Risolvere qualsiasi errore nella macchina virtuale prima di provare a convertire o caricare il disco in Azure.

Dopo avere convertito il disco, creare una macchina virtuale che utilizzi il disco convertito. Avviare e accedere alla macchina virtuale per terminare la preparazione della macchina virtuale per il caricamento.

### <a name="convert-disk-using-hyper-v-manager"></a>Convertire il disco tramite la console di gestione di Hyper-V
1. Aprire la console di gestione di Hyper-V e selezionare il computer locale a sinistra. Nel menu superiore fare clic su **Azioni** > **Modifica disco**.
2. Nella schermata **Percorso disco rigido virtuale** , selezionare il disco virtuale.
3. Nella schermata **Scelta azione** selezionare **Converti** e **Avanti**.
4. Se è necessaria la conversione dal formato VHDX, selezionare **VHD** e fare clic su **Avanti**
5. Se è necessaria la conversione dal disco a espansione dinamica, selezionare **A dimensione fissa** e fare clic su **Avanti**
6. Individuare e selezionare un percorso in cui salvare il nuovo file VHD.
7. Fare clic su **Fine** per chiudere.

### <a name="convert-disk-using-powershell"></a>Convertire il disco tramite PowerShell
È possibile convertire un disco virtuale utilizzando il cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) di Windows PowerShell. Selezionare **Esegui come amministratore** quando si avvia PowerShell. Nell'esempio seguente viene illustrato come eseguire la conversione da un file VHDX a un file VHD e da un disco a espansione dinamica a un disco a dimensione fissa:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Sostituire i valori di -Path con il percorso per il disco rigido virtuale che si desidera convertire e -DestinationPath con il nuovo percorso e il nome per il disco convertito.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Conversione dal formato VMware VMDK
Se si ha un'immagine di VM Windows in [formato di file VMDK](https://en.wikipedia.org/wiki/VMDK), convertirla in formato VHD usando [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Per altre informazioni, leggere l'argomento del blog relativo a [come convertire un file VMDK VMWare in un file VHD Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) .

## <a name="set-windows-configurations-for-azure"></a>Impostare le configurazioni di Windows per Azure

Nella macchina virtuale da caricare in Azure eseguire tutti i comandi seguenti nella finestra del prompt dei comandi con [privilegi amministrativi](https://technet.microsoft.com/library/cc947813.aspx).

1. Rimuovere qualsiasi route statica persistente nella tabella di routing:
   
   * Per visualizzare la tabella di route, eseguire `route print` nella finestra del prompt dei comandi.
   * Controllare le sezioni **Route persistenti** sezioni. Se è presente una route persistente, utilizzare il comando [Elimina route](https://technet.microsoft.com/library/cc739598.apx) per rimuoverla.
2. Rimuovere il proxy WinHTTP:
   
    ```CMD
    netsh winhttp reset proxy
    ```
3. Impostare il criterio SAN disco su [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```CMD
    diskpart 
    san policy=onlineall
    exit
    ```
    

4. Impostare l'ora UTC (Coordinated Universal Time) per Windows e impostare il tipo di avvio del servizio ora di Windows (w32time) su **Automatico**:
   
    ```CMD
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```
    ```CMD
    sc config w32time start= auto
    ```

## <a name="set-services-startup-to-windows-default-values"></a>Impostare l'avvio dei servizi sui valori predefiniti di Windows
Assicurarsi che ciascuno dei seguenti servizi di Windows sia impostato sui **valori predefiniti di Windows**, Eseguire i comandi seguenti per reimpostare le impostazioni di avvio:
   
```CMD
sc config bfe start= auto
   
sc config dcomlaunch start= auto
   
sc config dhcp start= auto
   
sc config dnscache start= auto
   
sc config IKEEXT start= auto
   
sc config iphlpsvc start= auto
   
sc config PolicyAgent start= demand
   
sc config LSM start= auto
   
sc config netlogon start= demand
   
sc config netman start= demand
   
sc config NcaSvc start= demand
   
sc config netprofm start= demand
   
sc config NlaSvc start= auto
   
sc config nsi start= auto
   
sc config RpcSs start= auto
   
sc config RpcEptMapper start= auto
   
sc config termService start= demand
   
sc config MpsSvc start= auto
   
sc config WinHttpAutoProxySvc start= demand
   
sc config LanmanWorkstation start= auto
   
sc config RemoteRegistry start= auto
```

## <a name="update-remote-desktop-registry-settings"></a>Aggiornare le impostazioni del registro di Desktop remoto
1. Rimuovere eventuali certificati autofirmati associati al listener del protocollo RDP, se presenti:
   
    ```CMD
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    Per ulteriori informazioni sulla configurazione dei certificati per il listener RDP, vedere [Configurazioni dei del listener in Windows Server ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)
2. Configurare i valori [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) per il servizio RDP:
   
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. Configurare la modalità di autenticazione per il servizio RDP:
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. Abilitare il servizio RDP aggiungendo le seguenti sottochiavi al registro:
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>Configurare le regole del firewall di Windows
1. Eseguire il comando seguente in PowerShell per consentire WinRM attraverso i tre profili firewall (Dominio, Privato e Pubblico) e abilitare il servizio remoto di PowerShell:
   
   ```powershell
   Enable-PSRemoting -force
   ```
2. Eseguire i comandi seguenti nella finestra del prompt dei comandi per assicurarsi che siano applicate le seguenti regole del firewall del sistema operativo guest:
   
   * In ingresso
   
   ```CMD
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * In ingresso e in uscita
   
   ```CMD
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * In uscita
   
   ```CMD
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Verificare che la macchina virtuale sia integra, sicura e accessibile con RDP 
1. Nella finestra del prompt dei comandi eseguire `winmgmt /verifyrepository` per confermare che il repository di Strumentazione gestione Windows (WMI) sia coerente. Se il repository è danneggiato, vedere il post di blog [WMI: il repository è davvero danneggiato?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)
2. Configurare le impostazioni dei dati di configurazione di avvio:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Rimuovere qualsiasi filtro TDI (Transport Driver Interface) aggiuntivo, ad esempio il software che analizza i pacchetti TCP.
4. Per verificare l'integrità e la coerenza del disco, eseguire il comando `CHKDSK /f` nella finestra del prompt dei comandi. Digitare "Y" per pianificare il controllo e riavviare la macchina virtuale.
5. Disinstallare qualsiasi altro software di terze parti e i driver relativi a componenti fisici o altre tecnologie di virtualizzazione.
6. Assicurarsi che un'applicazione di terze parti non stia utilizzandola porta 3389. Questa porta viene utilizzata per il servizio RDP in Azure. È possibile eseguire `netstat -anob` nella finestra del prompt dei comandi per visualizzare le porte usate dalle applicazioni.
7. Se il disco VHD di Windows che si desidera caricare un controller di dominio, attenersi alla [procedura aggiuntiva](https://support.microsoft.com/kb/2904015) per preparare il disco.
8. Riavviare la macchina virtuale per assicurarsi che Windows sia ancora integro e che possa essere raggiunto utilizzando la connessione RDP.
9. Reimpostare la password dell'amministratore locale corrente e accertarsi di poter utilizzare questo account per accedere a Windows mediante la connessione RDP. Questa autorizzazione di accesso è controllata dall'oggetto Criteri di gruppo "Consenti accesso tramite Servizi Desktop remoto", che si trova nell'editor Criteri di gruppo locali in "Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti utente."

## <a name="install-windows-updates"></a>Installare gli aggiornamenti di Windows
Installare gli ultimi aggiornamenti di Windows. Se ciò non è possibile, assicurarsi di aver installato i seguenti aggiornamenti:
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) - Le macchine virtuali di Microsoft Azure non vengono ripristinate a seguito di un'interruzione di rete e del danneggiamento dei dati
   * [KB3115224](https://support.microsoft.com/kb/3115224) - I miglioramenti dell'affidabilità per le macchine virtuali eseguite in un host di Windows Server 2012 R2 o Windows Server 2012
   * [KB3140410](https://support.microsoft.com/kb/3140410) - MS16-031: aggiornamento della sicurezza di Microsoft Windows per gestire l'aumento di livello dei privilegi (8 marzo 2016)
   * [KB3063075](https://support.microsoft.com/kb/3063075) - Molti eventi ID 129 vengono registrati quando si esegue una macchina virtuale di Windows Server 2012 R2 in Microsoft Azure
   * [KB3137061](https://support.microsoft.com/kb/3137061) - Le macchine virtuali di Microsoft Azure non vengono ripristinate a seguito di un'interruzione di rete e del danneggiamento dei dati
   * [KB3114025](https://support.microsoft.com/kb/3114025) - Rallentamento delle prestazioni quando si accede all'archiviazione dei file di Azure da Windows 8.1 o Windows Server 2012 R2
   * [KB3033930](https://support.microsoft.com/kb/3033930) - Hotfix aumenta il limite di 64 K nei buffer RIO per ogni processo del servizio Azure in Windows
   * [KB3004545](https://support.microsoft.com/kb/3004545) - Non è possibile accedere alle macchine virtuali ospitate nei servizi di hosting di Azure tramite una connessione VPN in Windows
   * [KB3082343](https://support.microsoft.com/kb/3082343) - Perdita della connettività VPN cross-premise quando i tunnel VPN site-to-site di Azure utilizzano l'RRAS di Windows Server 2012 R2
   * [KB3140410](https://support.microsoft.com/kb/3140410) - MS16-031: aggiornamento della sicurezza di Microsoft Windows per gestire l'aumento di livello dei privilegi (8 marzo 2016)
   * [KB3146723](https://support.microsoft.com/kb/3146723) - MS16-048: descrizione dell'aggiornamento di sicurezza per CSRSS (12 aprile 2016)
   * [KB2904100](https://support.microsoft.com/kb/2904100) - Il sistema si blocca durante il processo di I/O del disco in Windows
     
## Eseguire Sysprep <a id="step23"></a>    
Se si desidera creare un'immagine da distribuire in più macchine, è [necessario generalizzare l'immagine eseguendo Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) prima di caricare il disco rigido virtuale in Azure. Non è necessario eseguire Sysprep per usare un disco rigido virtuale specifico. Per altre informazioni, vedere gli articoli seguenti:
   
   * [Generalizzare una macchina virtuale Windows mediante Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [Supporto di Sysprep per i ruoli del server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## <a name="complete-recommended-configurations"></a>Completare le configurazioni consigliate
Le seguenti impostazioni non influenzano il caricamento del disco rigido virtuale. Tuttavia, si consiglia vivamente di configurarle.

* Installare l' [agente delle macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Dopo aver installato l'agente, è possibile abilitare le estensioni delle VM. Le estensioni delle VM implementano la maggior parte delle funzionalità critiche da usare con le macchine virtuali, come la reimpostazione delle password, la configurazione di RDP e molte altre.
* Il log Dump può essere utile per la risoluzione dei problemi di arresto anomalo del sistema Windows. Abilitare la raccolta di log Dump:
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* Dopo aver creato la macchina virtuale in Azure, configurare il file di paging di dimensioni definite dal sistema sull'unità D:
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


