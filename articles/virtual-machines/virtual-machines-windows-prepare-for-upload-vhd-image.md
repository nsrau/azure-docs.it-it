<properties
    pageTitle="Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure | Microsoft Azure"
    description="Procedure consigliate per preparare un disco rigido virtuale di Windows prima del caricamento in Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>


# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure
Per caricare una macchina virtuale di Windows da una posizione locale ad Azure, è necessario preparare correttamente il disco rigido virtuale (VHD). Esistono alcune procedure consigliate da svolgere prima di caricare un disco VHD in Azure. Questo articolo descrive come preparare un disco rigido virtuale di Windows da caricare in Microsoft Azure e illustra anche [come e quando usare Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Preparare il disco virtuale

>[AZURE.NOTE] 
> Azure supporta solo [macchine virtuali di prima generazione](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) nel formato di file VHD. Il formato VHDX più recente non è supportato in Azure. 
>
> Il disco VHD deve essere a dimensione fissa, non dinamica. Se richiesto, consultare le istruzioni riportate di seguito che descrivono nel dettaglio la conversione da dischi VHDX o dinamici. La dimensione massima consentita per il disco rigido virtuale è 1023 GB.


Assicurarsi che il disco VHD di Windows funzioni correttamente sul server locale. Risolvere qualsiasi errore nella macchina virtuale prima di provare a convertire o caricare il disco in Azure.

Se è necessario convertire il disco virtuale in un formato richiesto per Azure, utilizzare uno dei metodi descritti nelle prossime sezioni. Eseguire il backup della VM prima di eseguire qualsiasi processo di conversione del disco virtuale o Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Conversione tramite la console di gestione di Hyper-V
- Aprire la console di gestione di Hyper-V e selezionare il computer locale a sinistra. Nel menu superiore fare clic su **Azioni** > **Modifica disco**.
    - Nella schermata **Percorso disco rigido virtuale** , selezionare il disco virtuale.
    - Selezionare **Converti** nella schermata successiva
        - Se è necessaria la conversione dal formato VHDX, selezionare **VHD** e fare clic su **Avanti**
        - Se è necessaria la conversione dal disco dinamico, selezionare **A dimensione fissa** e fare clic su **Avanti**

    - Individuare e selezionare **Percorso per il nuovo file VHD**.
    - Fare clic su **Fine** per chiudere.

### <a name="convert-using-powershell"></a>Conversione tramite PowerShell
È possibile convertire un disco virtuale utilizzando il [cmdlet Convert-VHD di PowerShell](http://technet.microsoft.com/library/hh848454.aspx). Nell'esempio seguente, viene eseguita una conversione dal formato VHDX al formato VHD e da un tipo di file con dimensione dinamica a un file con dimensione fissa:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Conversione dal formato VMware VMDK
Se si ha un'immagine di VM Windows in [formato di file VMDK](https://en.wikipedia.org/wiki/VMDK), convertirla in formato VHD usando [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Per altre informazioni, leggere l'argomento del blog relativo a [come convertire un file VMDK VMWare in un file VHD Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) .

## <a name="prepare-windows-configuration-for-upload"></a>Preparare la configurazione di Windows per il caricamento

> [AZURE.NOTE] Eseguire tutti i comandi seguenti con [privilegi amministrativi](https://technet.microsoft.com/library/cc947813.aspx).

1. Rimuovere qualsiasi route statica persistente nella tabella di routing:

    - Per visualizzare la tabella di routing, eseguire `route print`.
    - Controllare le sezioni **Route persistenti** sezioni. Se è presente una route persistente, utilizzare il comando [Elimina route](https://technet.microsoft.com/library/cc739598.apx) per rimuoverla.

2. Rimuovere il proxy WinHTTP:

    ```
    netsh winhttp reset proxy
    ```

3. Configurare il criterio SAN disco su [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):

    ```
    diskpart san policy=onlineall
    ```

4. Utilizzare l'ora UTC (Coordinated Universal Time) per Windows e impostare il tipo di avvio del servizio ora di Windows (w32time) su **Automatico**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Configurare i servizi di Windows
5. Assicurarsi che ciascuno dei seguenti servizi di Windows sia impostato sui **valori predefiniti di Windows**, configurati con le impostazioni di avvio indicate nel seguente elenco. È possibile eseguire questi comandi per reimpostare le impostazioni di avvio:

    ```
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


## <a name="configure-remote-desktop-configuration"></a>Impostare la configurazione del desktop remoto
6. Rimuovere eventuali certificati autofirmati associati al listener del protocollo RDP, se presenti:

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Per ulteriori informazioni sulla configurazione dei certificati per il listener RDP, vedere [Configurazioni dei del listener in Windows Server ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Configurare i valori [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) per il servizio RDP:

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Configurare la modalità di autenticazione per il servizio RDP:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Abilitare il servizio RDP aggiungendo le seguenti sottochiavi al registro:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Configurare le regole del firewall di Windows
10. Consentire WinRM attraverso i tre profili firewall (Dominio, Privato e Pubblico) e abilitare il servizio remoto di PowerShell:

    ```
    Enable-PSRemoting -force
    ```

11. Verificare che le seguenti regole del firewall del sistema operativo ospite siano applicate:

    - In ingresso

    ```
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

    - In ingresso e in uscita

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - In uscita

    ```
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


## <a name="additional-windows-configuration-steps"></a>Procedura di configurazione di Windows aggiuntiva
12. Eseguire `winmgmt /verifyrepository` per confermare che il repository di Strumentazione gestione Windows (WMI) sia coerente. Se il repository è danneggiato, vedere [questo post di blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Assicurarsi che le impostazioni dei dati di configurazione di avvio (BCD) corrispondano a quanto segue:

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Rimuovere qualsiasi filtro TDI (Transport Driver Interface) aggiuntivo, ad esempio il software che analizza i pacchetti TCP.
15. Per verificare l'integrità e la coerenza del disco, eseguire il comando `CHKDSK /f` .
16. Disinstallare qualsiasi altro software di terze parti e i driver relativi a componenti fisici o altre tecnologie di virtualizzazione.
17. Assicurarsi che un'applicazione di terze parti non stia utilizzandola porta 3389. Questa porta viene utilizzata per il servizio RDP in Azure. È possibile usare il comando `netstat -anob` per verificare le porte usate dalle applicazioni.
18. Se il disco VHD di Windows che si desidera caricare un controller di dominio, attenersi alla [procedura aggiuntiva](https://support.microsoft.com/kb/2904015) per preparare il disco.
19. Riavviare la macchina virtuale per assicurarsi che Windows sia ancora integro e che possa essere raggiunto utilizzando la connessione RDP.
20. Reimpostare la password dell'amministratore locale corrente e accertarsi di poter utilizzare questo account per accedere a Windows mediante la connessione RDP.  Questa autorizzazione di accesso è controllata dall'oggetto criteri "Consenti accesso tramite Servizi Desktop remoto", che si trova in "Configurazione computer\Impostazioni di Windows\Impostazioni protezione\Criteri locali\Assegnazione diritti utente."


## <a name="install-windows-updates"></a>Installare gli aggiornamenti di Windows
22. Installare gli ultimi aggiornamenti di Windows. Se ciò non è possibile, assicurarsi di aver installato i seguenti aggiornamenti:

    - [KB3137061](https://support.microsoft.com/kb/3137061) - Le macchine virtuali di Microsoft Azure non vengono ripristinate a seguito di un'interruzione di rete e del danneggiamento dei dati

    - [KB3115224](https://support.microsoft.com/kb/3115224) - I miglioramenti dell'affidabilità per le macchine virtuali eseguite in un host di Windows Server 2012 R2 o Windows Server 2012

    - [KB3140410](https://support.microsoft.com/kb/3140410) - MS16-031: aggiornamento della sicurezza di Microsoft Windows per gestire l'aumento di livello dei privilegi (8 marzo 2016)

    - [KB3063075](https://support.microsoft.com/kb/3063075) - Molti eventi ID 129 vengono registrati quando si esegue una macchina virtuale di Windows Server 2012 R2 in Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) - Le macchine virtuali di Microsoft Azure non vengono ripristinate a seguito di un'interruzione di rete e del danneggiamento dei dati

    - [KB3114025](https://support.microsoft.com/kb/3114025) - Rallentamento delle prestazioni quando si accede all'archiviazione dei file di Azure da Windows 8.1 o Windows Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) - Hotfix aumenta il limite di 64 K nei buffer RIO per ogni processo del servizio Azure in Windows

    - [KB3004545](https://support.microsoft.com/kb/3004545) - Non è possibile accedere alle macchine virtuali ospitate nei servizi di hosting di Azure tramite una connessione VPN in Windows

    - [KB3082343](https://support.microsoft.com/kb/3082343) - Perdita della connettività VPN cross-premise quando i tunnel VPN site-to-site di Azure utilizzano l'RRAS di Windows Server 2012 R2

    - [KB3140410](https://support.microsoft.com/kb/3140410) - MS16-031: aggiornamento della sicurezza di Microsoft Windows per gestire l'aumento di livello dei privilegi (8 marzo 2016)

    - [KB3146723](https://support.microsoft.com/kb/3146723) - MS16-048: descrizione dell'aggiornamento di sicurezza per CSRSS (12 aprile 2016)
    - [KB2904100](https://support.microsoft.com/kb/2904100) Il sistema si blocca durante il processo di I/O del disco in Windows <a id="step23"></a>
23. Se si desidera creare un'immagine da cui distribuire più macchine, è necessario generalizzare l'immagine eseguendo `sysprep` prima di caricare il disco rigido virtuale in Azure. Non è necessario eseguire `sysprep` per usare un disco rigido virtuale specifico. Per ulteriori informazioni su come creare un'immagine generalizzata, vedere gli articoli seguenti:

    - [Creare un'immagine di VM da una macchina virtuale di Azure esistente tramite il modello di distribuzione di Resource Manager](virtual-machines-windows-capture-image.md)
    - [Creare un'immagine di VM da una macchina virtuale di Azure esistente tramite il modello di distribuzione classico](virtual-machines-windows-classic-capture-image.md)
    - [Supporto di Sysprep per i ruoli del server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Configurazioni aggiuntive suggerite

Le seguenti impostazioni non influenzano il caricamento del disco rigido virtuale. Tuttavia, si consiglia vivamente di configurarle.

- Installare l' [agente delle macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Dopo aver installato l'agente, è possibile abilitare le estensioni delle VM. Le estensioni delle VM implementano la maggior parte delle funzionalità critiche da usare con le macchine virtuali, come la reimpostazione delle password, la configurazione di RDP e molte altre.

- Il log Dump può essere utile per la risoluzione dei problemi di arresto anomalo del sistema Windows. Abilitare la raccolta di log Dump:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Dopo aver creato la macchina virtuale in Azure, configurare il file di paging di dimensioni definite dal sistema sull'unità D:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Passaggi successivi

- [Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager](virtual-machines-windows-upload-image.md)



<!--HONumber=Oct16_HO2-->


