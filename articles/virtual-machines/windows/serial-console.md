---
title: Console seriale per macchine virtuali di Azure | Microsoft Docs
description: Console seriale bidirezionale per macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: d5d855cac9f09f92798d955dda3d66ab6b631091
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Console seriale per macchine virtuali (anteprima) 


La console seriale per macchine virtuali in Azure fornisce l'accesso a una console basata su testo per macchine virtuali Linux e Windows. Questa connessione seriale viene stabilita con la porta seriale COM1 della macchina virtuale, fornisce l'accesso alla macchina virtuale e non è correlata allo stato del sistema operativo o della rete della macchina virtuale. L'accesso alla console seriale per una macchina virtuale attualmente può essere eseguito solo tramite il portale di Azure ed è consentito solo agli utenti che hanno l'accesso Collaboratore macchine virtuali o superiore alla VM. 

> [!Note] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure]. (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Questo servizio è attualmente in **anteprima pubblica** e l'accesso alla console seriale per le macchine virtuali è disponibile per le aree di Azure globali. La console seriale per il momento non è disponibile per il cloud Azure per enti pubblici, Azure Germania e Azure Cina.

 

## <a name="prerequisites"></a>prerequisiti 

* La macchina virtuale DEVE avere la [diagnostica di avvio](boot-diagnostics.md) abilitata 
* L'account che usa la console seriale deve avere il [ruolo Collaboratore](../../role-based-access-control/built-in-roles.md) per la VM e l'account di archiviazione della [diagnostica di avvio](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Aprire la console seriale
La console seriale per le macchine virtuali è accessibile solo tramite il [portale di Azure](https://portal.azure.com). Di seguito sono riportati i passaggi per accedere alla console seriale per macchine virtuali tramite il portale 

  1. Aprire il portale di Azure
  2. Nel menu a sinistra selezionare Macchine virtuali.
  3. Fare clic sulla macchina virtuale nell'elenco. Viene visualizzata la pagina Panoramica relativa alla VM.
  4. Scorrere verso il basso fino alla sezione Supporto e risoluzione dei problemi e fare clic sull'opzione Console seriale (anteprima). Verrà aperto un nuovo riquadro con la console seriale e verrà avviata la connessione.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Disabilitare la funzionalità
È possibile disattivare la funzionalità della console seriale per macchine virtuali specifiche disabilitando l'impostazione della diagnostica di avvio per tali macchine virtuali.

## <a name="serial-console-security"></a>Sicurezza della console seriale 

### <a name="access-security"></a>Sicurezza dell'accesso 
L'accesso alla console seriale è limitato agli utenti che hanno l'accesso [Collaboratore macchine virtuali](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) o superiore per la macchina virtuale. Se il tenant di AAD richiede Multi-Factor Authentication, MFA sarà necessario anche per l'accesso alla console seriale perché l'accesso viene stabilito tramite il [portale di Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sicurezza del canale
Tutti i dati inviati in entrambe le direzioni vengono crittografati durante il transito.

### <a name="audit-logs"></a>Log di controllo
Ogni accesso alla console seriale viene attualmente registrato nei log di [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) della macchina virtuale. L'accesso a questi log è di proprietà dell'amministratore della macchina virtuale di Azure, che ne ha anche il controllo.  

>[!CAUTION] 
Anche se non vengono registrate password di accesso per la console, se i comandi eseguiti nella console contengono o visualizzano password, segreti, nomi utente o qualsiasi altra forma di informazioni personali, tali elementi verranno scritti nei log di diagnostica di avvio della macchina virtuale, con qualsiasi altro testo visibile, durante l'implementazione della funzionalità di scorrimento all'indietro della console seriale. Questi log sono circolari e solo gli utenti con autorizzazioni di lettura per l'account di archiviazione di diagnostica possono accedervi, ma è preferibile seguire la procedura consigliata che prevede l'uso di Desktop remoto per tutto ciò che può implicare segreti e/o informazioni personali. 

### <a name="concurrent-usage"></a>Utilizzo simultaneo
Se un utente è connesso alla console seriale e un altro utente ottiene l'accesso alla stessa macchina virtuale, il primo utente verrà disconnesso, mentre il secondo verrà connesso, come se il primo utente si alzasse e lasciasse la console fisica e un nuovo utente ne prendesse il posto.

>[!CAUTION] 
Questo significa che la sessione dell'utente che viene disconnesso non verrà chiusa. Non è ancora possibile imporre la chiusura della sessione in caso di disconnessione (tramite SIGHUP o un meccanismo simile). Nella console SAC è abilitato un timeout automatico per Windows, mentre per Linux è possibile configurare l'impostazione di timeout del terminale. 


## <a name="accessing-serial-console-for-windows"></a>Accesso alla console seriale per Windows 
Per le immagini di Windows Server più recenti in Azure la [console amministrativa speciale](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC, Special Administrative Console) è abilitata per impostazione predefinita. La console SAC è supportata nelle versioni server di Windows, ma non è disponibile nelle versioni client (ad esempio, Windows 10, Windows 8 o Windows 7). Per abilitare la console seriale per le macchine virtuali Windows create usando immagini di febbraio 2018 o precedenti, seguire questa procedura: 

1. Connettersi alla macchina virtuale Windows tramite Desktop remoto
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Riavviare il sistema per abilitare la console SAC

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessario, la console SAC può essere abilitata anche offline: 

1. Collegare alla macchina virtuale esistente il disco di Windows per cui si vuole configurare la console SAC come disco dati. 
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Come capire se la console SAC è abilitata 

Se la console [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) non è abilitata, la console seriale non visualizzerà il prompt SAC. In alcuni casi è possibile che vengano visualizzate informazioni sull'integrità della VM oppure nessuna informazione.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Abilitazione del menu di avvio da visualizzare nella console seriale 

Se è necessario abilitare i prompt del caricatore di avvio di Windows da visualizzare nella console seriale, è possibile aggiungere le opzioni seguenti al caricatore di avvio di Windows.

1. Connettersi alla macchina virtuale Windows tramite Desktop remoto
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Riavviare il sistema per abilitare il menu di avvio

> [!NOTE] 
> A questo punto, il supporto per i tasti funzione non è abilitato. Se sono necessarie opzioni di avvio avanzate, usare bcdedit /set {current} onetimeadvancedoptions on. Per altre informazioni dettagliate, vedere [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)

## <a name="windows-commands---cmd"></a>Comandi di Windows - CMD 

Questa sezione include i comandi di esempio per l'esecuzione di attività comuni negli scenari in cui potrebbe essere necessario usare SAC per accedere alla macchina virtuale, ad esempio quando è necessario risolvere gli errori di connessione RDP.

SAC è stata inclusa in tutte le versioni di Windows, a partire da Windows Server 2003, ma è disabilitata per impostazione predefinita. SAC si basa sul driver del kernel `sacdrv.sys`, sul servizio `Special Administration Console Helper`(`sacsvr`) e sul processo `sacsess.exe`. Per altre informazioni, vedere [Emergency Management Services Tools and Settings](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)) (Strumenti e impostazioni di Servizi di gestione emergenze).

SAC consente di connettersi al sistema operativo in esecuzione tramite la porta seriale. Quando si avvia CMD da SAC, `sacsess.exe` avvia `cmd.exe` nel sistema operativo in esecuzione. Si noterà che in Gestione attività, se si effettua una connessione RDP alla macchina virtuale, nello stesso momento si viene connessi a SAC tramite la funzionalità della console seriale. L'istanza di CMD a cui si accede tramite SAC è la stessa `cmd.exe` usata per la connessione tramite RDP. Gli stessi strumenti e comandi sono tutti disponibili, inclusa la possibilità di avviare PowerShell da tale istanza di CMD. Una delle principali differenze tra SAC e Ambiente ripristino Windows è che SAC consente di gestire il sistema operativo in esecuzione, mentre Ambiente ripristino Windows viene avviato in un altro sistema operativo minimo. Anche se le macchine virtuali di Azure non supportano la possibilità di accedere ad Ambiente ripristino Windows, con la funzionalità della console seriale, le macchine virtuali di Azure possono essere gestite tramite SAC.

Poiché SAC è limitata a un buffer dello schermo pari a 80x24 senza scorrimento all'indietro, aggiungere `| more` ai comandi per visualizzare l'output una pagina alla volta. Usare `<spacebar>` per visualizzare la pagina successiva o `<enter>` per visualizzare la riga successiva.  

`SHIFT+INSERT` è il collegamento per Incolla per la finestra della console seriale.

A causa del buffer dello schermo limitato di SAC, potrebbe essere più semplice digitare i comandi più lunghi in un editor di testo locale e quindi incollarli in SAC.

### <a name="view-and-edit-windows-registry-settings"></a>Visualizzare e modificare le impostazioni del Registro di sistema di Windows
#### <a name="verify-rdp-is-enabled"></a>Verificare che RDP sia abilitato
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

La seconda chiave (in \Policies) esisterà solo se è configurata l'impostazione di Criteri di gruppo pertinente.

#### <a name="enable-rdp"></a>Abilitare RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

La seconda chiave (in \Policies) sarebbe necessaria solo se fosse stata configurata l'impostazione di Criteri di gruppo pertinente. Il valore verrà riscritto al prossimo aggiornamento di Criteri di gruppo se è stato configurato in Criteri di gruppo.

### <a name="manage-windows-services"></a>Gestire i servizi di Windows

#### <a name="view-service-state"></a>Visualizzare lo stato del servizio
`sc query termservice`
####  <a name="view-service-logon-account"></a>Visualizzare l'account di accesso al servizio
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Impostare l'account di accesso al servizio 
`sc config termservice obj= "NT Authority\NetworkService"`

Dopo il segno di uguale è necessario uno spazio.
#### <a name="set-service-start-type"></a>Impostare il tipo di avvio del servizio
`sc config termservice start= demand` 

Dopo il segno di uguale è necessario uno spazio. I valori possibili di avvio includono `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
#### <a name="set-service-dependencies"></a>Impostare le dipendenze del servizio
`sc config termservice depend= RPCSS`

Dopo il segno di uguale è necessario uno spazio.
#### <a name="start-service"></a>Avviare il servizio
`net start termservice`

oppure

`sc start termservice`
#### <a name="stop-service"></a>Arrestare il servizio
`net stop termservice`

oppure

`sc stop termservice`
### <a name="manage-networking-features"></a>Gestire le funzionalità di rete
#### <a name="show-nic-properties"></a>Visualizzare le proprietà della scheda di interfaccia di rete
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>Visualizzare le proprietà dell'IP
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>Visualizzare la configurazione IPSec
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Abilitare la scheda di interfaccia di rete
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Impostare la scheda di interfaccia di rete per usare DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Le macchine virtuali di Azure devono essere sempre configurate nel sistema operativo guest per usare DHCP per poter ottenere un indirizzo IP. L'impostazione dell'IP statico di Azure usa tuttavia DHCP per assegnare l'IP statico alla VM.
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Ping della porta  
Installare il client telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testare la connettività

`telnet bing.com 80`

Per rimuovere il client telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Se limitato ai metodi disponibili in Windows per impostazione predefinita, PowerShell può essere un approccio migliore per testare la connettività di porta. Vedere la sezione di PowerShell seguente per gli esempi.
#### <a name="test-dns-name-resolution"></a>Testare la risoluzione dei nomi DNS
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Visualizzare la regola di Windows Firewall
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Disabilitare Windows Firewall
`netsh advfirewall set allprofiles state off`

Per escludere temporaneamente Windows Firewall, è possibile usare questo comando durante la risoluzione dei problemi. Verrà abilitato al riavvio successivo o quando lo si abilita usando il comando indicato sotto. Non arrestare il servizio Windows Firewall (MPSSVC) o il servizio Base Filtering Engine (BFE) per escludere Windows Firewall. L'arresto di MPSSVC o BFE determinerà il blocco di tutta la connettività.
#### <a name="enable-windows-firewall"></a>Abilitare Windows Firewall
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Gestire utenti e gruppi
#### <a name="create-local-user-account"></a>Creare l'account utente locale
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Aggiungere l'utente locale al gruppo locale
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Verificare che l'account utente sia abilitato
`net user <username> | find /i "active"`

L'account amministratore locale delle VM di Azure create dall'immagine generalizzata verrà rinominato con il nome specificato durante il provisioning delle VM. In genere sarà quindi diverso da `Administrator`.
#### <a name="enable-user-account"></a>Abilitare l'account utente
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Visualizzare le proprietà dell'account utente
`net user <username>`

Righe di esempio rilevanti da un account amministratore locale:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Visualizzare i gruppi locali
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Gestire il registro eventi di Windows
#### <a name="query-event-log-errors"></a>Eseguire query sugli errori nel registro eventi
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Modificare `/c:10` con il numero desiderato di eventi da restituire o rimuoverlo per restituire tutti gli eventi corrispondenti al filtro.
#### <a name="query-event-log-by-event-id"></a>Eseguire query sul registro eventi per ID evento
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Eseguire query sul registro eventi per ID evento e provider
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Eseguire query sul registro eventi per ID evento e provider per le ultime 24 ore
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Usare `604800000` per eseguire la ricerca sugli ultimi 7 giorni invece che sulle ultime 24 ore.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Eseguire query sul registro eventi per ID evento, provider e dati eventi negli ultimi 7 giorni
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Visualizzare o rimuovere le applicazioni installate
#### <a name="list-installed-applications"></a>Elencare le applicazioni installate
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` applica l'ordinamento decrescente in base alla data di installazione per visualizzare le applicazioni installate di recente. Usare `<spacebar>` per passare alla pagina successiva dell'output o `<enter>` per avanzare di una riga.
#### <a name="uninstall-an-application"></a>Disinstallare un'applicazione
`wmic path win32_product where name="<name>" call uninstall`

Sostituire `<name>` con il nome restituito nel comando precedente per l'applicazione che si vuole rimuovere.

### <a name="file-system-management"></a>Gestione del file system
#### <a name="get-file-version"></a>Ottenere la versione del file
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Questo esempio restituisce la versione file del driver della scheda di interfaccia di rete virtuale, ovvero netvsc.sys, netvsc63.sys o netvsc60.sys a seconda della versione di Windows.
#### <a name="scan-for-system-file-corruption"></a>Cercare il danneggiamento del file system
`sfc /scannow`

Vedere anche [Repair a Windows Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image) (Riparare un'immagine Windows).
#### <a name="scan-for-system-file-corruption"></a>Cercare il danneggiamento del file system
`dism /online /cleanup-image /scanhealth`

Vedere anche [Repair a Windows Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image) (Riparare un'immagine Windows).
#### <a name="export-file-permissions-to-text-file"></a>Esportare le autorizzazioni del file in un file di testo
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Salvare le autorizzazioni del file in un file ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Ripristinare le autorizzazioni del file da un file ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Il percorso quando si usa `/restore` deve corrispondere alla cartella padre della cartella specificata per l'uso di `/save`. In questo esempio `\RSA` è l'elemento padre della cartella `\MachineKeys` specificata nell'esempio `/save` precedente.
#### <a name="take-ntfs-ownership-of-a-folder"></a>Acquisire la proprietà NTFS di una cartella
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Concedere le autorizzazioni NTFS in modo ricorsivo a una cartella
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Gestire i dispositivi
#### <a name="remove-non-present-pnp-devices"></a>Rimuovere i dispositivi Plug and Play non presenti
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>Gestire Criteri di gruppo
#### <a name="force-group-policy-update"></a>Forzare l'aggiornamento di Criteri di gruppo
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Attività varie
#### <a name="show-os-version"></a>Visualizzare la versione del sistema operativo
`ver`

oppure 

`wmic os get caption,version,buildnumber /format:list`

oppure 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Visualizzare la data di installazione del sistema operativo
`systeminfo | find /i "original"`

oppure 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Visualizzare l'ora dell'ultimo avvio
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Visualizzare il fuso orario
`systeminfo | find /i "time zone"`

oppure

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Riavviare Windows
`shutdown /r /t 0`

Aggiungendo `/f`, verrà forzata la chiusura delle applicazioni in esecuzione senza avvisare gli utenti.
#### <a name="detect-safe-mode-boot"></a>Rilevare l'avvio in modalità sicura
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Comandi di Windows - PowerShell

Per eseguire PowerShell in SAC, dopo avere raggiunto un prompt CMD, digitare:

`powershell <enter>`

>[!CAUTION]
Rimuovere il modulo PSReadLine dalla sessione di PowerShell prima di eseguire altri comandi di PowerShell. Esiste un problema noto per cui possono essere introdotti caratteri aggiuntivi nel testo incollato dagli Appunti se PSReadLine è in esecuzione in una sessione di PowerShell in SAC.

Controllare prima di tutto se PSReadLine viene caricato. Viene caricato per impostazione predefinita in Windows Server 2016, Windows 10 e versioni successive di Windows. È presente nelle versioni precedenti di Windows solo se è stato installato manualmente. 

Se questo comando torna a un prompt senza output, il modulo non è stato caricato ed è possibile continuare a usare la sessione di PowerShell in SAC come di consueto.

`get-module psreadline`

Se il comando precedente restituisce la versione del modulo PSReadLine, eseguire il comando seguente per scaricarla. Questo comando non elimina né disinstalla il modulo, semplicemente lo scarica dalla sessione corrente di PowerShell.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Visualizzare e modificare le impostazioni del Registro di sistema di Windows
#### <a name="verify-rdp-is-enabled"></a>Verificare che RDP sia abilitato
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

La seconda chiave (in \Policies) esisterà solo se è configurata l'impostazione di Criteri di gruppo pertinente.
#### <a name="enable-rdp"></a>Abilitare RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

La seconda chiave (in \Policies) sarebbe necessaria solo se fosse stata configurata l'impostazione di Criteri di gruppo pertinente. Il valore verrà riscritto al prossimo aggiornamento di Criteri di gruppo se è stato configurato in Criteri di gruppo.
### <a name="manage-windows-services"></a>Gestire i servizi di Windows
#### <a name="view-service-details"></a>Visualizzare i dettagli del servizio
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` può essere usato, ma non include l'account di accesso del servizio. `Get-WmiObject win32-service` invece sì.
#### <a name="set-service-logon-account"></a>Impostare l'account di accesso al servizio
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Quando si usa un account del servizio diverso da `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService` o `LocalSystem`, specificare la password dell'account come ultimo (ottavo) argomento dopo il nome dell'account.
#### <a name="set-service-startup-type"></a>Impostare il tipo di avvio del servizio
`set-service termservice -startuptype Manual`

`Set-service` accetta `Automatic`, `Manual` o `Disabled` come tipo di avvio.
#### <a name="set-service-dependencies"></a>Impostare le dipendenze del servizio
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Avviare il servizio
`start-service termservice`
#### <a name="stop-service"></a>Arrestare il servizio
`stop-service termservice`
### <a name="manage-networking-features"></a>Gestire le funzionalità di rete
#### <a name="show-nic-properties"></a>Visualizzare le proprietà della scheda di interfaccia di rete
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

oppure 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` è disponibile per le versioni del 2012 e successive. Per 2008R2, usare `Get-WmiObject`.
#### <a name="show-ip-properties"></a>Visualizzare le proprietà dell'IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Abilitare la scheda di interfaccia di rete
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

oppure

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` è disponibile per le versioni del 2012 e successive. Per 2008R2, usare `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Impostare la scheda di interfaccia di rete per usare DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject`. Le macchine virtuali di Azure devono essere sempre configurate nel sistema operativo guest per usare DHCP per poter ottenere un indirizzo IP. L'impostazione dell'IP statico di Azure usa tuttavia DHCP per assegnare l'IP alla VM.
#### <a name="ping"></a>Ping
`test-netconnection`

oppure

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` senza parametri proverà a effettuare il ping di `internetbeacon.msedge.net`. È disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject` come nel secondo esempio.
#### <a name="port-ping"></a>Ping della porta
`test-netconnection -ComputerName bing.com -Port 80`

oppure

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Net.Sockets.TcpClient`
#### <a name="test-dns-name-resolution"></a>Testare la risoluzione dei nomi DNS
`resolve-dnsname bing.com` 

oppure 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Visualizzare la regola di Windows Firewall per nome
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Visualizzare la regola di Windows Firewall per porta
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

oppure

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare l'oggetto COM `hnetcfg.fwpolicy2`. 
#### <a name="disable-windows-firewall"></a>Disabilitare Windows Firewall
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `netsh advfirewall` come illustrato nella sezione relativa a CMD precedente.
### <a name="manage-users-and-groups"></a>Gestire utenti e gruppi
#### <a name="create-local-user-account"></a>Creare l'account utente locale
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Verificare che l'account utente sia abilitato
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

oppure 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject`. Questo esempio illustra l'account predefinito Administrator locale, che ha sempre il SID `S-1-5-21-*-500`. L'account amministratore locale delle VM di Azure create dall'immagine generalizzata verrà rinominato con il nome specificato durante il provisioning delle VM. In genere sarà quindi diverso da `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Aggiungere l'utente locale al gruppo locale
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Abilitare l'account utente locale
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Questo esempio abilita l'account predefinito Administrator locale, che ha sempre il SID `S-1-5-21-*-500`. L'account amministratore locale delle VM di Azure create dall'immagine generalizzata verrà rinominato con il nome specificato durante il provisioning delle VM. In genere sarà quindi diverso da `Administrator`.
#### <a name="view-user-account-properties"></a>Visualizzare le proprietà dell'account utente
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

oppure 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject`. Questo esempio illustra l'account predefinito Administrator locale, che ha sempre il SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Visualizzare i gruppi locali
`(get-localgroup).name | sort``(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>Gestire il registro eventi di Windows
#### <a name="query-event-log-errors"></a>Eseguire query sugli errori nel registro eventi
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Modificare `/c:10` con il numero desiderato di eventi da restituire o rimuoverlo per restituire tutti gli eventi corrispondenti al filtro.
#### <a name="query-event-log-by-event-id"></a>Eseguire query sul registro eventi per ID evento
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Eseguire query sul registro eventi per ID evento e provider
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Eseguire query sul registro eventi per ID evento e provider per le ultime 24 ore
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Usare `604800000` per eseguire la ricerca sugli ultimi 7 giorni invece che sulle ultime 24 ore. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Eseguire query sul registro eventi per ID evento, provider e dati eventi negli ultimi 7 giorni
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Visualizzare o rimuovere le applicazioni installate
#### <a name="list-installed-software"></a>Elencare il software installato
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Disinstallare il software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Gestione del file system
#### <a name="get-file-version"></a>Ottenere la versione del file
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Questo esempio restituisce la versione file del driver della scheda di interfaccia di rete virtuale, denominato netvsc.sys, netvsc63.sys o netvsc60.sys a seconda della versione di Windows.
#### <a name="download-and-extract-file"></a>Scaricare ed estrarre il file
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Questo esempio crea una cartella `c:\bin`, quindi scarica ed estrae la suite Sysinternals di strumenti in `c:\bin`.
### <a name="miscellaneous-tasks"></a>Attività varie
#### <a name="show-os-version"></a>Visualizzare la versione del sistema operativo
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Visualizzare la data di installazione del sistema operativo
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Visualizzare l'ora dell'ultimo avvio
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Visualizzare il tempo di attività di Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Restituisce il tempo di attività come `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, ad esempio `49:16:48:00.00`. 
#### <a name="restart-windows"></a>Riavviare Windows
`restart-computer`

Aggiungendo `-force`, verrà forzata la chiusura delle applicazioni in esecuzione senza avvisare gli utenti.
### <a name="instance-metadata"></a>Metadati dell'istanza

È possibile eseguire query sui metadati dell'istanza di Azure dalla macchina virtuale di Azure per visualizzare i dettagli, ad esempio osType, Location, vmSize, vmId, name, resourceGroupName, subscriptionId, privateIpAddress e publicIpAddress.

Per eseguire query sui metadati dell'istanza, è necessario che la connettività di rete guest sia integra, perché effettua una chiamata REST tramite l'host di Azure al servizio metadati dell'istanza. Se quindi è possibile eseguire query sui metadati dell'istanza, significa che il guest riesce a comunicare tramite la rete con un servizio ospitato da Azure.

Per altre informazioni, vedere [Servizio metadati dell'istanza di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Metadati dell'istanza
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>Tipo di sistema operativo (metadati dell'istanza)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Posizione (metadati dell'istanza)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Dimensioni (metadati dell'istanza)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>ID della VM (metadati dell'istanza)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Nome della VM (metadati dell'istanza)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Nome del gruppo di risorse (metadati dell'istanza)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>ID della sottoscrizione (metadati dell'istanza)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Tag (metadati dell'istanza)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>ID del gruppo di posizionamento (metadati dell'istanza)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Dominio di errore della piattaforma (metadati dell'istanza)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Dominio di aggiornamento della piattaforma (metadati dell'istanza)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>Indirizzo IP privato IPv4 (metadati dell'istanza)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>Indirizzo IP pubblico IPv4 (metadati dell'istanza)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Indirizzo/Prefisso della subnet IPv4 prefisso (metadati dell'istanza)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>Indirizzo IP IPv6 (metadati dell'istanza)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>Indirizzo MAC (metadati dell'istanza)
`$im.network.interface.macAddress`

## <a name="errors"></a>Errors
La maggior parte degli errori è di natura temporanea e, per risolverli, è sufficiente stabilire di nuovo la connessione. La tabella seguente contiene un elenco di errori e il modo per prevenirli 

Tipi di errore                            |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica per "<VMNAME>". Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata. 
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale
Non si hanno le autorizzazioni necessarie per usare questa console seriale per la VM. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio "<STORAGEACCOUNTNAME>". Verificare che la diagnostica di avvio sia abilitata per questa macchina virtuale e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)

## <a name="known-issues"></a>Problemi noti 
Poiché l'accesso alla console seriale è ancora in fase di anteprima, è in corso la soluzione di alcuni problemi noti. Di seguito è riportato un elenco di questi problemi con le possibili soluzioni alternative 

Problema                           |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Non sono disponibili opzioni per la console seriale dell'istanza del set di scalabilità di macchine virtuali | In fase di anteprima l'accesso alla console seriale per le istanze del set di scalabilità di macchine virtuali non è supportato.
Premendo INVIO dopo il banner della connessione, non viene visualizzato un prompt di accesso | [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Durante la connessione a una macchina virtuale Windows, vengono visualizzate solo informazioni sull'integrità| [Segnali di integrità di Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Non è possibile digitare nel prompt SAC se è abilitato il debug del kernel | Effettuare una connessione RDP ed eseguire `bcdedit /debug {current} off` da un prompt dei comandi con privilegi elevati. Se non è possibile effettuare una connessione RDP, è invece possibile collegare il disco del sistema operativo a un'altra macchina virtuale di Azure e modificarlo mentre è collegato come disco dati usando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, quindi effettuare lo swapping del disco.

## <a name="frequently-asked-questions"></a>Domande frequenti 
**D. Come è possibile inviare commenti e suggerimenti?**

R. Per inviare commenti e suggerimenti, andare a https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di http://feedback.azure.com 

**D. Viene visualizzato l'errore "La console esistente presenta un tipo di sistema operativo "Windows" in conflitto con il tipo di sistema operativo Linux richiesto".**

R. Si tratta di un problema noto. Per risolverlo, è sufficiente aprire [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) in modalità Bash e riprovare.

**D. Impossibile accedere alla console seriale. Dove è possibile inserire un caso di supporto?**

R. Questa funzionalità di anteprima è soggetta alle condizioni per l'anteprima di Azure. Per ottenere supporto, è consigliabile usare i canali indicati in precedenza. 

## <a name="next-steps"></a>Passaggi successivi
* Questa console seriale è disponibile anche per le VM [Linux](../linux/serial-console.md)
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md)
