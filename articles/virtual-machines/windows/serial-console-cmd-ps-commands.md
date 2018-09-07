---
title: CMD e PowerShell in macchine virtuali Windows in Azure | Microsoft Docs
description: Come usare i comandi di PowerShell e CMD in una console SAC all'interno di macchine virtuali Windows di Azure
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: a6f8984086771fea4df4851b2a878d480b2050ea
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918518"
---
# <a name="windows-commands---cmd-and-powershell"></a>Comandi di Windows - CMD e PowerShell

Questa sezione include comandi di esempio per l'esecuzione di attività comuni all'interno di scenari in cui potrebbe essere necessario usare la console SAC per accedere alla macchina virtuale Windows, ad esempio quando è necessario risolvere errori di connessione Remote Desktop Protocol.

SAC è stata inclusa in tutte le versioni di Windows, a partire da Windows Server 2003, ma è disabilitata per impostazione predefinita. SAC si basa sul driver del kernel `sacdrv.sys`, sul servizio `Special Administration Console Helper`(`sacsvr`) e sul processo `sacsess.exe`. Per altre informazioni, vedere [Emergency Management Services Tools and Settings](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)) (Strumenti e impostazioni di Servizi di gestione emergenze).

SAC consente di connettersi al sistema operativo in esecuzione tramite la porta seriale. Quando si avvia CMD da SAC, `sacsess.exe` avvia `cmd.exe` nel sistema operativo in esecuzione. Si noterà che in Gestione attività, se si effettua una connessione RDP alla macchina virtuale, nello stesso momento si viene connessi a SAC tramite la funzionalità della console seriale. L'istanza di CMD a cui si accede tramite SAC è la stessa `cmd.exe` usata per la connessione tramite RDP. Gli stessi strumenti e comandi sono tutti disponibili, inclusa la possibilità di avviare PowerShell da tale istanza di CMD. Una delle principali differenze tra SAC e Ambiente ripristino Windows è che SAC consente di gestire il sistema operativo in esecuzione, mentre Ambiente ripristino Windows viene avviato in un altro sistema operativo minimo. Anche se le macchine virtuali di Azure non supportano la possibilità di accedere ad Ambiente ripristino Windows, con la funzionalità della console seriale, le macchine virtuali di Azure possono essere gestite tramite SAC.

Poiché SAC è limitata a un buffer dello schermo pari a 80x24 senza scorrimento all'indietro, aggiungere `| more` ai comandi per visualizzare l'output una pagina alla volta. Usare `<spacebar>` per visualizzare la pagina successiva o `<enter>` per visualizzare la riga successiva.  

`SHIFT+INSERT` è il collegamento per Incolla per la finestra della console seriale.

A causa del buffer dello schermo limitato di SAC, potrebbe essere più semplice digitare i comandi più lunghi in un editor di testo locale e quindi incollarli in SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Visualizzare e modificare le impostazioni del Registro di sistema di Windows
### <a name="verify-rdp-is-enabled"></a>Verificare che RDP sia abilitato
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

La seconda chiave (in \Policies) esisterà solo se è configurata l'impostazione di Criteri di gruppo pertinente.

### <a name="enable-rdp"></a>Abilitare RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

La seconda chiave (in \Policies) sarebbe necessaria solo se fosse stata configurata l'impostazione di Criteri di gruppo pertinente. Il valore verrà riscritto al prossimo aggiornamento di Criteri di gruppo se è stato configurato in Criteri di gruppo.

## <a name="manage-windows-services"></a>Gestire i servizi di Windows

### <a name="view-service-state"></a>Visualizzare lo stato del servizio
`sc query termservice`
###  <a name="view-service-logon-account"></a>Visualizzare l'account di accesso al servizio
`sc qc termservice`
### <a name="set-service-logon-account"></a>Impostare l'account di accesso al servizio 
`sc config termservice obj= "NT Authority\NetworkService"`

Dopo il segno di uguale è necessario uno spazio.
### <a name="set-service-start-type"></a>Impostare il tipo di avvio del servizio
`sc config termservice start= demand` 

Dopo il segno di uguale è necessario uno spazio. I valori possibili di avvio includono `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
### <a name="set-service-dependencies"></a>Impostare le dipendenze del servizio
`sc config termservice depend= RPCSS`

Dopo il segno di uguale è necessario uno spazio.
### <a name="start-service"></a>Avviare il servizio
`net start termservice`

oppure

`sc start termservice`
### <a name="stop-service"></a>Arrestare il servizio
`net stop termservice`

oppure

`sc stop termservice`
## <a name="manage-networking-features"></a>Gestire le funzionalità di rete
### <a name="show-nic-properties"></a>Visualizzare le proprietà della scheda di interfaccia di rete
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Visualizzare le proprietà dell'IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Visualizzare la configurazione IPSec
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Abilitare la scheda di interfaccia di rete
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Impostare la scheda di interfaccia di rete per usare DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Per altre informazioni su `netsh`, [fare clic qui](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Le macchine virtuali di Azure devono essere sempre configurate nel sistema operativo guest per usare DHCP per poter ottenere un indirizzo IP. L'impostazione dell'IP statico di Azure usa tuttavia DHCP per assegnare l'IP statico alla VM.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Ping della porta  
Installare il client telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testare la connettività

`telnet bing.com 80`

Per rimuovere il client telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Se limitato ai metodi disponibili in Windows per impostazione predefinita, PowerShell può essere un approccio migliore per testare la connettività di porta. Vedere la sezione di PowerShell seguente per gli esempi.
### <a name="test-dns-name-resolution"></a>Testare la risoluzione dei nomi DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Visualizzare la regola di Windows Firewall
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Disabilitare Windows Firewall
`netsh advfirewall set allprofiles state off`

Per escludere temporaneamente Windows Firewall, è possibile usare questo comando durante la risoluzione dei problemi. Verrà abilitato al riavvio successivo o quando lo si abilita usando il comando indicato sotto. Non arrestare il servizio Windows Firewall (MPSSVC) o il servizio Base Filtering Engine (BFE) per escludere Windows Firewall. L'arresto di MPSSVC o BFE determinerà il blocco di tutta la connettività.
### <a name="enable-windows-firewall"></a>Abilitare Windows Firewall
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Gestire utenti e gruppi
### <a name="create-local-user-account"></a>Creare l'account utente locale
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Aggiungere l'utente locale al gruppo locale
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Verificare che l'account utente sia abilitato
`net user <username> | find /i "active"`

L'account amministratore locale delle VM di Azure create dall'immagine generalizzata verrà rinominato con il nome specificato durante il provisioning delle VM. In genere sarà quindi diverso da `Administrator`.
### <a name="enable-user-account"></a>Abilitare l'account utente
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Visualizzare le proprietà dell'account utente
`net user <username>`

Righe di esempio rilevanti da un account amministratore locale:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Visualizzare i gruppi locali
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Gestire il registro eventi di Windows
### <a name="query-event-log-errors"></a>Eseguire query sugli errori nel registro eventi
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Modificare `/c:10` con il numero desiderato di eventi da restituire o rimuoverlo per restituire tutti gli eventi corrispondenti al filtro.
### <a name="query-event-log-by-event-id"></a>Eseguire query sul registro eventi per ID evento
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Eseguire query sul registro eventi per ID evento e provider
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Eseguire query sul registro eventi per ID evento e provider per le ultime 24 ore
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Usare `604800000` per eseguire la ricerca sugli ultimi 7 giorni invece che sulle ultime 24 ore.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Eseguire query sul registro eventi per ID evento, provider e dati eventi negli ultimi 7 giorni
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visualizzare o rimuovere le applicazioni installate
### <a name="list-installed-applications"></a>Elencare le applicazioni installate
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` applica l'ordinamento decrescente in base alla data di installazione per visualizzare le applicazioni installate di recente. Usare `<spacebar>` per passare alla pagina successiva dell'output o `<enter>` per avanzare di una riga.
### <a name="uninstall-an-application"></a>Disinstallare un'applicazione
`wmic path win32_product where name="<name>" call uninstall`

Sostituire `<name>` con il nome restituito nel comando precedente per l'applicazione che si vuole rimuovere.

## <a name="file-system-management"></a>Gestione del file system
### <a name="get-file-version"></a>Ottenere la versione del file
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Questo esempio restituisce la versione file del driver della scheda di interfaccia di rete virtuale, ovvero netvsc.sys, netvsc63.sys o netvsc60.sys a seconda della versione di Windows.
### <a name="scan-for-system-file-corruption"></a>Cercare il danneggiamento del file system
`sfc /scannow`

Vedere anche [Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image) (Riparare un'immagine Windows).
### <a name="scan-for-system-file-corruption"></a>Cercare il danneggiamento del file system
`dism /online /cleanup-image /scanhealth`

Vedere anche [Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image) (Riparare un'immagine Windows).
### <a name="export-file-permissions-to-text-file"></a>Esportare le autorizzazioni del file in un file di testo
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Salvare le autorizzazioni del file in un file ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Ripristinare le autorizzazioni del file da un file ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Il percorso quando si usa `/restore` deve corrispondere alla cartella padre della cartella specificata per l'uso di `/save`. In questo esempio `\RSA` è l'elemento padre della cartella `\MachineKeys` specificata nell'esempio `/save` precedente.
### <a name="take-ntfs-ownership-of-a-folder"></a>Acquisire la proprietà NTFS di una cartella
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Concedere le autorizzazioni NTFS in modo ricorsivo a una cartella
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Gestire i dispositivi
### <a name="remove-non-present-pnp-devices"></a>Rimuovere i dispositivi Plug and Play non presenti
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Gestire Criteri di gruppo
### <a name="force-group-policy-update"></a>Forzare l'aggiornamento di Criteri di gruppo
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Attività varie
### <a name="show-os-version"></a>Visualizzare la versione del sistema operativo
`ver`

oppure 

`wmic os get caption,version,buildnumber /format:list`

oppure 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Visualizzare la data di installazione del sistema operativo
`systeminfo | find /i "original"`

oppure 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Visualizzare l'ora dell'ultimo avvio
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Visualizzare il fuso orario
`systeminfo | find /i "time zone"`

oppure

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Riavviare Windows
`shutdown /r /t 0`

Aggiungendo `/f`, verrà forzata la chiusura delle applicazioni in esecuzione senza avvisare gli utenti.
### <a name="detect-safe-mode-boot"></a>Rilevare l'avvio in modalità sicura
`bcdedit /enum | find /i "safeboot"` 

# <a name="windows-commands---powershell"></a>Comandi di Windows - PowerShell

Per eseguire PowerShell in SAC, dopo avere raggiunto un prompt CMD, digitare:

`powershell <enter>`

>[!CAUTION]
Rimuovere il modulo PSReadLine dalla sessione di PowerShell prima di eseguire altri comandi di PowerShell. Esiste un problema noto per cui possono essere introdotti caratteri aggiuntivi nel testo incollato dagli Appunti se PSReadLine è in esecuzione in una sessione di PowerShell in SAC.

Controllare prima di tutto se PSReadLine viene caricato. Viene caricato per impostazione predefinita in Windows Server 2016, Windows 10 e versioni successive di Windows. È presente nelle versioni precedenti di Windows solo se è stato installato manualmente. 

Se questo comando torna a un prompt senza output, il modulo non è stato caricato ed è possibile continuare a usare la sessione di PowerShell in SAC come di consueto.

`get-module psreadline`

Se il comando precedente restituisce la versione del modulo PSReadLine, eseguire il comando seguente per scaricarla. Questo comando non elimina né disinstalla il modulo, semplicemente lo scarica dalla sessione corrente di PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Visualizzare e modificare le impostazioni del Registro di sistema di Windows
### <a name="verify-rdp-is-enabled"></a>Verificare che RDP sia abilitato
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

La seconda chiave (in \Policies) esisterà solo se è configurata l'impostazione di Criteri di gruppo pertinente.
### <a name="enable-rdp"></a>Abilitare RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

La seconda chiave (in \Policies) sarebbe necessaria solo se fosse stata configurata l'impostazione di Criteri di gruppo pertinente. Il valore verrà riscritto al prossimo aggiornamento di Criteri di gruppo se è stato configurato in Criteri di gruppo.
## <a name="manage-windows-services"></a>Gestire i servizi di Windows
### <a name="view-service-details"></a>Visualizzare i dettagli del servizio
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` può essere usato, ma non include l'account di accesso del servizio. `Get-WmiObject win32-service` invece sì.
### <a name="set-service-logon-account"></a>Impostare l'account di accesso al servizio
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Quando si usa un account del servizio diverso da `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService` o `LocalSystem`, specificare la password dell'account come ultimo (ottavo) argomento dopo il nome dell'account.
### <a name="set-service-startup-type"></a>Impostare il tipo di avvio del servizio
`set-service termservice -startuptype Manual`

`Set-service` accetta `Automatic`, `Manual` o `Disabled` come tipo di avvio.
### <a name="set-service-dependencies"></a>Impostare le dipendenze del servizio
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Avviare il servizio
`start-service termservice`
### <a name="stop-service"></a>Arrestare il servizio
`stop-service termservice`
## <a name="manage-networking-features"></a>Gestire le funzionalità di rete
### <a name="show-nic-properties"></a>Visualizzare le proprietà della scheda di interfaccia di rete
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

oppure 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` è disponibile per le versioni del 2012 e successive. Per 2008R2, usare `Get-WmiObject`.
### <a name="show-ip-properties"></a>Visualizzare le proprietà dell'IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Abilitare la scheda di interfaccia di rete
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

oppure

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` è disponibile per le versioni del 2012 e successive. Per 2008R2, usare `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Impostare la scheda di interfaccia di rete per usare DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject`. Le macchine virtuali di Azure devono essere sempre configurate nel sistema operativo guest per usare DHCP per poter ottenere un indirizzo IP. L'impostazione dell'IP statico di Azure usa tuttavia DHCP per assegnare l'IP alla VM.
### <a name="ping"></a>Ping
`test-netconnection`

oppure

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` senza parametri proverà a effettuare il ping di `internetbeacon.msedge.net`. È disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject` come nel secondo esempio.
### <a name="port-ping"></a>Ping della porta
`test-netconnection -ComputerName bing.com -Port 80`

oppure

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testare la risoluzione dei nomi DNS
`resolve-dnsname bing.com` 

oppure 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Visualizzare la regola di Windows Firewall per nome
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Visualizzare la regola di Windows Firewall per porta
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

oppure

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare l'oggetto COM `hnetcfg.fwpolicy2`. 
### <a name="disable-windows-firewall"></a>Disabilitare Windows Firewall
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `netsh advfirewall` come illustrato nella sezione relativa a CMD precedente.
## <a name="manage-users-and-groups"></a>Gestire utenti e gruppi
### <a name="create-local-user-account"></a>Creare l'account utente locale
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Verificare che l'account utente sia abilitato
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

oppure 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject`. Questo esempio illustra l'account predefinito Administrator locale, che ha sempre il SID `S-1-5-21-*-500`. L'account amministratore locale delle VM di Azure create dall'immagine generalizzata verrà rinominato con il nome specificato durante il provisioning delle VM. In genere sarà quindi diverso da `Administrator`.
### <a name="add-local-user-to-local-group"></a>Aggiungere l'utente locale al gruppo locale
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Abilitare l'account utente locale
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Questo esempio abilita l'account predefinito Administrator locale, che ha sempre il SID `S-1-5-21-*-500`. L'account amministratore locale delle VM di Azure create dall'immagine generalizzata verrà rinominato con il nome specificato durante il provisioning delle VM. In genere sarà quindi diverso da `Administrator`.
### <a name="view-user-account-properties"></a>Visualizzare le proprietà dell'account utente
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

oppure 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject`. Questo esempio illustra l'account predefinito Administrator locale, che ha sempre il SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Visualizzare i gruppi locali
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` è disponibile nelle versioni del 2012 e successive. Per 2008R2 usare `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>Gestire il registro eventi di Windows
### <a name="query-event-log-errors"></a>Eseguire query sugli errori nel registro eventi
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Modificare `/c:10` con il numero desiderato di eventi da restituire o rimuoverlo per restituire tutti gli eventi corrispondenti al filtro.
### <a name="query-event-log-by-event-id"></a>Eseguire query sul registro eventi per ID evento
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Eseguire query sul registro eventi per ID evento e provider
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Eseguire query sul registro eventi per ID evento e provider per le ultime 24 ore
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Usare `604800000` per eseguire la ricerca sugli ultimi 7 giorni invece che sulle ultime 24 ore. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Eseguire query sul registro eventi per ID evento, provider e dati eventi negli ultimi 7 giorni
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visualizzare o rimuovere le applicazioni installate
### <a name="list-installed-software"></a>Elencare il software installato
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Disinstallare il software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Gestione del file system
### <a name="get-file-version"></a>Ottenere la versione del file
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Questo esempio restituisce la versione file del driver della scheda di interfaccia di rete virtuale, denominato netvsc.sys, netvsc63.sys o netvsc60.sys a seconda della versione di Windows.
### <a name="download-and-extract-file"></a>Scaricare ed estrarre il file
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Questo esempio crea una cartella `c:\bin`, quindi scarica ed estrae la suite Sysinternals di strumenti in `c:\bin`.
## <a name="miscellaneous-tasks"></a>Attività varie
### <a name="show-os-version"></a>Visualizzare la versione del sistema operativo
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Visualizzare la data di installazione del sistema operativo
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Visualizzare l'ora dell'ultimo avvio
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Visualizzare il tempo di attività di Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Restituisce il tempo di attività come `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, ad esempio `49:16:48:00.00`. 
### <a name="restart-windows"></a>Riavviare Windows
`restart-computer`

Aggiungendo `-force`, verrà forzata la chiusura delle applicazioni in esecuzione senza avvisare gli utenti.
## <a name="instance-metadata"></a>Metadati dell'istanza

È possibile eseguire query sui metadati dell'istanza di Azure dalla macchina virtuale di Azure per visualizzare i dettagli, ad esempio osType, Location, vmSize, vmId, name, resourceGroupName, subscriptionId, privateIpAddress e publicIpAddress.

Per eseguire query sui metadati dell'istanza, è necessario che la connettività di rete guest sia integra, perché effettua una chiamata REST tramite l'host di Azure al servizio metadati dell'istanza. Se quindi è possibile eseguire query sui metadati dell'istanza, significa che il guest riesce a comunicare tramite la rete con un servizio ospitato da Azure.

Per altre informazioni, vedere [Servizio metadati dell'istanza di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Metadati dell'istanza
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Tipo di sistema operativo (metadati dell'istanza)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Posizione (metadati dell'istanza)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Dimensioni (metadati dell'istanza)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID della VM (metadati dell'istanza)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Nome della VM (metadati dell'istanza)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Nome del gruppo di risorse (metadati dell'istanza)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>ID della sottoscrizione (metadati dell'istanza)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Tag (metadati dell'istanza)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>ID del gruppo di posizionamento (metadati dell'istanza)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Dominio di errore della piattaforma (metadati dell'istanza)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Dominio di aggiornamento della piattaforma (metadati dell'istanza)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Indirizzo IP privato IPv4 (metadati dell'istanza)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Indirizzo IP pubblico IPv4 (metadati dell'istanza)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Indirizzo/Prefisso della subnet IPv4 prefisso (metadati dell'istanza)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>Indirizzo IP IPv6 (metadati dell'istanza)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Indirizzo MAC (metadati dell'istanza)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Passaggi successivi
* La pagina principale della documentazione di Windows relativa alla console seriale è disponibile [qui](serial-console.md).
* Questa console seriale è disponibile anche per le macchine virtuali [Linux](../linux/serial-console.md).
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md).
