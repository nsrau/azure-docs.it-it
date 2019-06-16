---
title: Risolvere gli errori di installazione push del servizio Mobility durante l'abilitazione della replica per il ripristino di emergenza | Microsoft Docs
description: Risolvere gli errori di installazione del servizio Mobility durante l'abilitazione della replica per il ripristino di emergenza
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/27/2019
ms.openlocfilehash: 58c09c71aad2b6244f6e2f3d144c033665932f50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925567"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Risolvere i problemi di installazione push del servizio Mobility

L'installazione del servizio Mobility è un passaggio chiave dell'abilitazione della replica. La riuscita di questo passaggio dipende esclusivamente dal rispetto dei prerequisiti e dall'utilizzo di configurazioni supportate. Gli errori più comuni che si verificano durante l'installazione del servizio Mobility sono dovuti a:

* [Errori relativi a credenziali o privilegi](#credentials-check-errorid-95107--95108)
* [Errori di accesso](#login-failures-errorid-95519-95520-95521-95522)
* [Errori di connettività](#connectivity-failure-errorid-95117--97118)
* [Errori di condivisione file e stampanti](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Errori WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemi operativi non supportati](#unsupported-operating-systems)
* [Configurazioni di avvio non supportate](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS installation failures](#vss-installation-failures) (Errori di installazione di VSS)
* [Nome del dispositivo nella configurazione GRUB invece del dispositivo UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume della LVM](#lvm-support-from-920-version)
* [Avvisi di riavvio](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando si abilita la replica, Azure Site Recovery tenta di eseguire l'installazione push dell'agente del servizio Mobility nella macchina virtuale. Nell'ambito di questa operazione, il server di configurazione tenta di connettersi alla macchina virtuale e di copiare l'agente. Per consentire la riuscita dell'installazione, seguire le indicazioni dettagliate per la risoluzione dei problemi descritte di seguito.

## <a name="credentials-check-errorid-95107--95108"></a>Controllo delle credenziali (ID errore: 95107 e 95108)

* Verificare se l'account utente scelto durante l'abilitazione della replica è **valido e corretto**.
* Per eseguire l'installazione push, Azure Site Recovery richiede un account **radice** o un account utente con **privilegi di amministratore**. In caso contrario, l'installazione push viene bloccata nel computer di origine.
  * In Windows (**errore 95107**), verificare se l'account utente dispone dell'accesso come amministratore, locale o di dominio, nel computer di origine.
  * Se non si usa un account di dominio, è necessario disabilitare il controllo di accesso utente remoto nel computer locale.
    * Per disabilitare il controllo di accesso utente remoto, nella chiave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Chiave del Registro di sistema, aggiungere un nuovo valore DWORD: LocalAccountTokenFilterPolicy. Impostare il valore su 1. Per eseguire questo comando, al prompt dei comandi eseguire il comando seguente:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Per la riuscita dell'installazione dell'agente di mobilità in Linux (**errore 95108**), è necessario scegliere l'account radice. I servizi SFTP devono essere in esecuzione. Per abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config:
    1. Accedere come utente ROOT.
    2. Passare al file /etc/ssh/sshd_config, individuare la riga che inizia con PasswordAuthentication.
    3. Rimuovere il commento dalla riga e modificare il valore in yes.
    4. Individuare la riga che inizia con Subsystem e rimuovere il commento.
    5. Riavviare il servizio sshd.

Se si vogliono modificare le credenziali dell'account utente scelto, seguire le istruzioni illustrate [qui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Errore di privilegi insufficienti (ID errore: 95517)

Se l'utente scelto per installare l'agente di mobilità non dispone dei privilegi di amministratore, il server di configurazione/server di elaborazione dello scale-out non può copiare il software dell'agente di mobilità nel computer di origine. Questo errore è quindi il risultato di un errore di accesso negato. Assicurarsi che l'account utente disponga dei privilegi di amministratore.

Se si vogliono modificare le credenziali dell'account utente scelto, seguire le istruzioni illustrate [qui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Errore di privilegi insufficienti (ID errore: 95518)

Quando stabilire una relazione di trust di dominio tra la workstation e il dominio primario non riesce durante il tentativo di accedere al computer di origine, l'installazione dell'agente di mobilità ha esito negativo con errore ID 95518. Pertanto, assicurarsi che l'account utente usato per installare l'agente di mobilità disponga dei privilegi amministrativi per eseguire l'accesso tramite il dominio primario sul computer di origine.

Se si vogliono modificare le credenziali dell'account utente scelto, seguire le istruzioni illustrate [qui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Errori di accesso (ID errore: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Le credenziali dell'account utente sono state disattivate (ID errore: 95519)

L'account utente scelto durante l'abilitazione della replica è stato disabilitato. Per abilitare l'account utente, fare riferimento all'articolo [qui](https://aka.ms/enable_login_user) oppure eseguire il comando seguente sostituendo *username* con il nome utente effettivo.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Credenziali bloccate a causa di più tentativi di accesso non riusciti (ID errore: 95520)

Se si eseguono più tentativi di accesso a un computer senza esito positivo, il sistema blocca l'account utente. La cause dell'errore possono essere le seguenti:

* le credenziali specificate durante l'installazione della configurazione non sono corrette O
* l'account utente scelto durante l'abilitazione della replica è errato.

Modificare quindi le credenziali scelte seguendo le istruzioni indicate [qui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e provare a eseguire l'operazione dopo avere atteso qualche minuto.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>I server di accesso non sono disponibili nel computer di origine (ID errore: 95521)

Questo errore si verifica quando i server di accesso non sono disponibili nel computer di origine. La mancata disponibilità dei server di accesso genera errori di richiesta di accesso e pertanto non è possibile installare l'agente di mobilità. Per eseguire l'accesso, assicurarsi che i server di accesso siano disponibili nel computer di origine e avviare il servizio di accesso. Per istruzioni dettagliate, vedere la Knowledge Base [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) al messaggio di errore: Esistono attualmente Nessun accesso server disponibile.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Il servizio di accesso non è in esecuzione nel computer di origine (ID errore: 95522)

Il servizio di accesso non è in esecuzione nel computer di origine e ha causato un errore nella richiesta di accesso. L'agente di mobilità non può pertanto essere installato. Per risolvere il problema, verificare che il servizio di accesso sia in esecuzione nel computer di origine. Per avviare il servizio di accesso, eseguire il comando "net start Logon" dal prompt dei comandi o avviare il servizio "Accesso rete" da Gestione attività.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Errore di connettività (ID errore: 95117 e 97118)**

Il server di configurazione/server di elaborazione dello scale-out tenta di connettersi alla macchina virtuale di origine per installare l'agente di mobilità. Questo errore si verifica quando il computer di origine non è raggiungibile a causa di problemi di connettività di rete. Per risolvere il problema:

* Assicurarsi che sia possibile effettuare il ping del computer di origine dal server di configurazione. Se durante l'abilitazione della replica si è scelto un server di elaborazione con scalabilità orizzontale, assicurarsi che sia possibile effettuare il ping del computer di origine dal server di elaborazione.
  * Dalla riga di comando del computer del server di origine, usare Telnet per effettuare il ping del server di configurazione o del server di elaborazione scale-out attraverso la porta HTTPS (135) come illustrato di seguito, per stabilire se sono presenti problemi di connettività di rete o problemi che causano il blocco delle porte del firewall.

     `telnet <CS/ scale-out PS IP address> <135>`
* Per una **macchina virtuale Linux**, poi,
  * Controllare se sono installati i pacchetti openssh, openssh-server e openssl più recenti.
  * Assicurarsi che il protocollo SSH (Secure Shell) sia abilitato e in esecuzione sulla porta 22.
  * I servizi SFTP devono essere in esecuzione. Per abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config,
    * Accedere come utente ROOT.
    * Passare al file /etc/ssh/sshd_config, individuare la riga che inizia con PasswordAuthentication.
    * Rimuovere il commento dalla riga e modificare il valore in yes
    * Individuare la riga che inizia con Subsystem e rimuovere il commento
    * Riavviare il servizio sshd.
* Un tentativo di connessione può avere esito negativo se non è stata ricevuta una risposta corretta dopo un determinato periodo di tempo, oppure una connessione stabilita può generare un errore perché l'host connesso non ha risposto.
* Può trattarsi di un problema correlato alla connettività, alla rete o al dominio. L'errore può anche essere dovuto a un problema di risoluzione dei nomi DNS o di esaurimento delle porte TCP. Controllare se sono presenti problemi noti di questo tipo nel dominio.

## <a name="connectivity-failure-errorid-95523"></a>Errore di connettività (ID errore: 95523)

Questo errore si verifica quando la rete in cui risiede il computer di origine non viene trovata, potrebbe essere stata eliminata o non è più disponibile. L'unico modo per risolvere il problema è assicurarsi che esista la rete.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Controllo del servizio di condivisione di file e stampanti (ID errore 95105 e 95106)

Dopo il controllo della connettività, verificare se il servizio di condivisione di file e stampanti è abilitato nella macchina virtuale. Queste impostazioni sono necessarie per copiare l'agente di mobilità nel computer di origine.

Per **Windows 2008 R2 e versioni precedenti**,

* Per abilitare la condivisione di file e stampanti tramite Windows Firewall,
  * Aprire Pannello di controllo -> Sistema e sicurezza -> Windows Firewall -> nel riquadro sinistro fare clic su Impostazioni avanzate -> nell'albero della console fare clic su Regole in entrata.
  * Individuare le regole Condivisione file e stampanti (NB-Session-In) e Condivisione file e stampanti (SMB-In). Su ogni regola fare clic con il pulsante destro del mouse e quindi fare clic su **Abilita regola**.
* Per abilitare la condivisione di file con Criteri di gruppo,
  * Passare a Start ed eseguire una ricerca digitando gpmc.msc.
  * Nel riquadro di spostamento, aprire le cartelle seguenti: Criteri del computer locale, Configurazione utente, Modelli amministrativi, Componenti di Windows e Condivisione di rete.
  * Nel riquadro dei dettagli fare doppio clic su **Impedisci di condividere file nel profilo utente**. Per disabilitare questa impostazione di Criteri di gruppo e consentire la condivisione di file, fare clic su Disabilitato. Fare clic su OK per salvare le modifiche. Per altre informazioni, vedere [Abilita o disabilita la condivisione di File con criteri di gruppo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Per la **nelle versioni successive**, seguire le istruzioni fornite nella [installa il servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici](vmware-azure-install-mobility-service.md) per abilitare la condivisione file e stampanti.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Controllo della configurazione di Strumentazione gestione Windows (WMI) (ID errore: 95103)

Dopo il controllo dei servizi file e stampanti, abilitare il servizio WMI per i profili, pubblici, privati e di dominio attraverso il firewall. Queste impostazioni sono necessarie per completare l'esecuzione remota nel computer di origine. Per abilitare:

* Accedere al Pannello di controllo, fare clic su Sicurezza e quindi su Windows Firewall.
* Fare clic su Cambia impostazioni e quindi sulla scheda Eccezioni.
* Nella finestra Eccezioni selezionare la casella di controllo Strumentazione gestione Windows (WMI) per consentire il traffico WMI attraverso il firewall. 

È anche possibile abilitare il traffico WMI attraverso il firewall dal prompt dei comandi. Usare il comando seguente: `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Di seguito sono elencati altri articoli sulla risoluzione dei problemi di WMI.

* [Test WMI di base](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Risoluzione dei problemi WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Risoluzione dei problemi con gli script WMI e i servizi WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Sistemi operativi non supportati

Un altro motivo di errore molto comune è l'uso di un sistema operativo non supportato. Per la riuscita dell'installazione del servizio Mobility, assicurarsi di usare la versione supportata del kernel e del sistema operativo. Evitare l'uso di una patch privata.
Per visualizzare l'elenco delle versioni del kernel e dei sistemi operativi supportati da Azure Site Recovery, fare riferimento al [documento matrice di supporto](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurazioni del disco di avvio non supportate (ID errore: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Le partizioni e i volumi di avvio e di sistema non sono lo stesso disco (ID errore: 95309)

Nelle versioni precedenti alla 9.20, le partizioni e i volumi di avvio e di sistema su dischi diversi sono una configurazione non supportata. A partire dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) questa configurazione è supportata. Per usufruire di questo supporto, usare la versione più recente.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Il disco di avvio non è disponibile (ID errore: 95310)

Non è possibile proteggere una macchina virtuale senza un disco di avvio. Lo scopo è garantire un ripristino senza problemi della macchina virtuale durante l'operazione di failover. In assenza del disco di avvio, la macchina virtuale non può essere avviata dopo il failover. Assicurarsi che la macchina virtuale contenga un disco di avvio e ripetere l'operazione. Si noti anche che non sono supportati più dischi di avvio nella stessa macchina.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Più dischi di avvio presenti nel computer di origine (ID errore: 95311)

Una macchina virtuale con più dischi di avvio non è una [configurazione supportata](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partizione di sistema su più dischi (ID errore: 95313)

Nelle versioni precedenti alla 9.20, la partizione o il volume radice su più dischi è una configurazione non supportata. A partire dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) questa configurazione è supportata. Per usufruire di questo supporto, usare la versione più recente.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Abilitare la protezione non riuscita come nome del dispositivo indicato nella configurazione GRUB invece di UUID (ID errore: 95320)

**Causa possibile:** </br>
I file di configurazione di GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" o"/etc/default/grub") possono contenere il valore per i parametri **root** (radice) e **resume** (riprendi) come nomi effettivi dei dispositivi anziché l'UUID. Site Recovery impone l'approccio UUID in quanto il nome dei dispositivi è soggetto a cambiamento al riavvio della macchina virtuale; la stessa potrebbe non essere visualizzata con lo stesso nome in caso di failover, con conseguenti problemi. Ad esempio: </br>


- La riga seguente si trova nel file GRUB **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- La riga seguente si trova nel file GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Se si osserva la stringa precedente formattata in grassetto, si noterà che GRUB contiene i nomi dei dispositivi effettivi per i parametri "root"(radice) e "resume" (riprendi) invece di UUID.
 
**Come correggere:**<br>
I nomi dei dispositivi devono essere sostituiti con l'UUID corrispondente.<br>


1. Individuare l'UUID del dispositivo eseguendo il comando "blkid \<nome dispositivo >". Ad esempio:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. A questo punto sostituire il nome del dispositivo con le UUID nel formato, ad esempio "radice = UUID =\<UUID >". Ad esempio, se sostituire i nomi dei dispositivi con UUID per radice e riprendere parametro indicato in precedenza nei file "/ boot/grub2/grub.cfg", "/ boot/grub2/grub.cfg" o "/ e così via/predefinita/grub: quindi le righe nei file saranno simili. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. Riavviare nuovamente la protezione

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installare il servizio Mobility completo di avviso di riavvio (ID errore: 95265 e 95266)

Il servizio Mobility di Site Recovery ha molti componenti, uno dei quali è denominato driver filtro. Il driver filtro viene caricato nella memoria di sistema solo in una fase di riavvio del sistema. Ciò significa che le correzioni del driver filtro possono essere realizzate solo quando viene caricato un nuovo driver filtro, il che può avvenire solo al momento del riavvio del sistema.

**Nota**: questo è un avviso; la replica esistente funzionerà anche dopo l'aggiornamento del nuovo agente. È possibile scegliere di riavviare ogni volta che si desidera ottenere i vantaggi di nuovi driver di filtro, ma se si non riavvia i vecchi mantiene driver filtro sull'utilizzo. In questo caso, dopo un aggiornamento senza riavvio, oltre al driver filtro **si realizzano benefici di altri miglioramenti e correzioni nel servizio di mobilità**. Pertanto, anche se consigliato, non è obbligatorio per riavviare il sistema dopo ogni aggiornamento. Per informazioni su quando un riavvio è obbligatorio, configurare il [riavvio della macchina di origine dopo l'aggiornamento dell'agente di mobilità ](https://aka.ms/v2a_asr_reboot) sezione negli aggiornamenti del servizio Azure Site Recovery.

> [!TIP]
>Per le procedure consigliate nella pianificazione degli aggiornamenti durante la finestra di manutenzione, vedere la [supporto per le versioni del sistema operativo/kernel più recente](https://aka.ms/v2a_asr_upgrade_practice) negli aggiornamenti del servizio Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Supporto LVM dalla versione 9.20

Nelle versioni precedenti alla 9.20, LVM è supportato per solo i dischi dati. /boot deve trovarsi in una partizione del disco e non essere un volume LVM.

A partire dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), il [disco del sistema operativo su LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) è supportato. Per usufruire di questo supporto, usare la versione più recente.

## <a name="insufficient-space-errorid-95524"></a>Spazio insufficiente (ID errore: 95524)

Per copiare l'agente di mobilità nel computer di origine, sono necessari almeno 100 MB di spazio disponibile su disco. Verificare pertanto che nel computer di origine sia presente lo spazio disponibile richiesto e ripetere l'operazione.

## <a name="vss-installation-failures"></a>Errori di installazione di VSS

Installazione di VSS è una parte dell'installazione dell'agente di mobilità. Questo servizio viene usato nel processo di generazione di punti di recupero coerenti dell'applicazione. I motivi degli errori di installazione di VSS possono essere diversi. Per identificare esattamente gli errori, vedere **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Nella sezione seguente sono evidenziati alcuni errori comuni e le procedure di risoluzione corrispondenti.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Errore VSS -2147023170 [0x800706BE] - codice di uscita 511

Questo problema si verifica principalmente quando un software antivirus sta bloccando le operazioni di servizi di Azure Site Recovery. Per risolvere il problema:

1. Escludere tutte le cartelle indicate [qui](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Seguire le linee guida pubblicate dal provider del software antivirus per sbloccare la registrazione della DLL in Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Errore VSS 7 [0x7] - codice di uscita 511

Si tratta di un errore di runtime causato dalla mancanza di memoria sufficiente per l'installazione di VSS. Per consentire il completamento dell'operazione, assicurarsi di aumentare lo spazio disponibile su disco.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Errore VSS -2147023824 [0x80070430] - codice di uscita 517

Questo errore si verifica quando il servizio Provider VSS di Azure Site Recovery è [contrassegnato per l'eliminazione](https://msdn.microsoft.com/library/ms838153.aspx). Provare a installare manualmente VSS nel computer di origine tramite la riga di comando seguente

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Errore VSS -2147023841 [0x8007041F] - codice di uscita 512

Questo errore si verifica quando il database del servizio Provider VSS di Azure Site Recovery è [bloccato](https://msdn.microsoft.com/library/ms833798.aspx). Provare a installare manualmente VSS nel computer di origine tramite la riga di comando seguente

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>Errore VSS codice di uscita 806

Questo errore si verifica quando l'account utente usato per l'installazione non dispone delle autorizzazioni necessarie per eseguire il comando CSScript. Concedere all'account utente le autorizzazioni necessarie per eseguire lo script e ripetere l'operazione.

### <a name="other-vss-errors"></a>Altri errori VSS

Provare a installare manualmente il servizio Provider VSS nel computer di origine tramite la riga di comando seguente

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Errore VSS - 0x8004E00F

Questo errore si verifica in genere durante l'installazione dell'agente di mobilità a causa di problemi in DCOM e DCOM è in uno stato critico.

Utilizzare la procedura seguente per determinare la causa dell'errore.

**Esaminare i log di installazione**

1. Aprire il log di installazione che si trova in c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. La presenza dell'errore seguente indica il problema:

    L'annullamento della registrazione dell'applicazione esistente...  Creare l'oggetto catalogo ottenere la raccolta di applicazioni 

    ERRORE:

    - Codice di errore:-2147164145 [0x8004E00F]
    - Codice di uscita: 802

Per risolvere il problema:

Contattare il [team della piattaforma Microsoft Windows](https://aka.ms/Windows_Support) per ottenere assistenza per la risoluzione del problema DCOM.

Quando viene risolto il problema DCOM, reinstallare il Provider VSS di Azure Site Recovery manualmente usando il comando seguente:
 
**C:\Programmi\Microsoft file (x86) \Microsoft Azure Site Recovery\agent > "c:\Programmi\Microsoft file (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Se la coerenza dell'applicazione non è critica per le proprie esigenze di ripristino di emergenza, è possibile ignorare l'installazione del Provider VSS. 

Per ignorare l'installazione di Provider VSS di Azure Site Recovery e installare manualmente dopo l'installazione Provider VSS di Azure Site Recovery:

1. Installare il servizio mobility. 
   > [!Note]
   > 
   > L'installazione avrà esito negativo in fase di 'Dopo l'installazione configuration'. 
2. Per ignorare l'installazione di VSS:
   1. Aprire la directory di installazione servizio Mobility di Azure Site Recovery che si trova in:
   
      C:\Programmi\Microsoft file (x86) \Microsoft Azure Site Recovery\agent
   2. Modificare gli script di installazione di Provider VSS di Azure Site Recovery **nMageVSSProvider_Install** e **InMageVSSProvider_Uninstall.cmd** abbia sempre esito positivo aggiungendo le righe seguenti:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Eseguire nuovamente l'installazione dell'agente di mobilità manualmente. 
4. Quando l'installazione ha esito positivo e vengono spostate con il passaggio successivo **configura**, rimuovere le righe aggiunte.
5. Per installare il provider VSS, aprire un prompt dei comandi come amministratore ed eseguire il comando seguente:
   
    **C:\Programmi\Microsoft file (x86) \Microsoft Azure Site Recovery\agent >.\InMageVSSProvider_Install.cmd**

9. Verificare che il Provider VSS di Azure Site Recovery viene installato come un servizio in servizi di Windows e aprire la console MMC Servizi componenti per verificare che sia elencato il Provider VSS di Azure Site Recovery.
10. Se il Provider VSS di installare persiste, rivolgersi per risolvere gli errori di autorizzazioni in CAPI2 CX.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Installazione di Provider VSS non riesce a causa di un servizio cluster viene abilitato nel computer non cluster

Questo problema causa l'installazione di Azure Site Recovery Mobility Agent esito negativo durante il passaggio di installazione di Provider VSS di ASAzure Site Recovery a causa di un problema con COM+ che impedisce l'installazione del provider VSS.
 
### <a name="to-identify-the-issue"></a>Per identificare il problema

Nel log che si trova nel server di configurazione al C:\ProgramData\ASRSetupLogs\UploadedLogs\<data e ora > UA_InstallLogFile.log, si noterà l'eccezione seguente:

COM+ non è riuscito a comunicare con il servizio Microsoft Distributed Transaction Coordinator (eccezione da HRESULT: 0x8004E00F)

Per risolvere il problema:

1.  Verificare che questo computer è un computer non cluster e che i componenti cluster non vengano usati.
3.  Se non vengono utilizzati i componenti, rimuovere i componenti cluster dal computer.

## <a name="drivers-are-missing-on-the-source-server"></a>Driver non sono disponibili nel Server di origine

Se l'installazione dell'agente di mobilità non riesce, esaminare i registri in C:\ProgramData\ASRSetupLogs per determinare se alcuni dei driver necessari non sono presenti in alcuni set di controllo.
 
Per risolvere il problema:
  
1. Usando un editor del Registro di sistema, ad esempio regedit.msc, aprire il Registro di sistema.
2. Aprire il nodo HKEY_LOCAL_MACHINE\SYSTEM.
3. Individuare il controllo del nodo di sistema, i set.
4. Aprire ogni set di controlli e verificare che siano presenti i driver di Windows seguenti:

   - Atapi
   - Vmbus
   - storflt
   - Storvsc
   - intelide
 
Reinstallare tutti i driver mancanti.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](vmware-azure-tutorial.md) configurare il ripristino di emergenza per le macchine virtuali VMware.
