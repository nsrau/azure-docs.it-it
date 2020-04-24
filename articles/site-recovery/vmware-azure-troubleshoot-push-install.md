---
title: Risolvere i problemi di installazione push del servizio Mobility con Azure Site Recovery
description: Risolvere gli errori di installazione dei servizi Mobility quando si Abilita la replica per il ripristino di emergenza con Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656313"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Risolvere i problemi di installazione push del servizio Mobility

L'installazione del servizio Mobility è un passaggio chiave per abilitare la replica. Il successo di questo passaggio dipende dalla riunione dei prerequisiti e dall'uso delle configurazioni supportate. Gli errori più comuni che potrebbero verificarsi durante l'installazione del servizio Mobility sono dovuti a:

* [Errori relativi a credenziali o privilegi](#credentials-check-errorid-95107--95108)
* [Errori di accesso](#login-failures-errorid-95519-95520-95521-95522)
* [Errori di connettività](#connectivity-failure-errorid-95117--97118)
* [Errori di condivisione file e stampanti](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Errori di Strumentazione gestione Windows (WMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemi operativi non supportati](#unsupported-operating-systems)
* [Configurazioni di avvio non supportate](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Errori di installazione del servizio Copia Shadow del volume (VSS)](#vss-installation-failures)
* [Nome del dispositivo nella configurazione GRUB invece del dispositivo UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume Logical Volume Manager (LVM)](#lvm-support-from-920-version)
* [Avvisi di riavvio](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando si Abilita la replica, Azure Site Recovery tenta di installare l'agente del servizio Mobility nella macchina virtuale (VM). Nell'ambito di questo processo, il server di configurazione tenta di connettersi alla macchina virtuale e di copiare l'agente. Per abilitare correttamente l'installazione, seguire le istruzioni dettagliate per la risoluzione dei problemi.

## <a name="credentials-check-errorid-95107--95108"></a>Controllo delle credenziali (ID errore: 95107 e 95108)

Verificare che l'account utente scelto durante l'abilitazione della replica sia valido e accurato. Azure Site Recovery richiede l'account **radice** o l'account utente con **privilegi di amministratore** per eseguire un'installazione push. In caso contrario, l'installazione push verrà bloccata nel computer di origine.

Per Windows (**errore 95107**), verificare che l'account utente disponga dell'accesso amministrativo al computer di origine, con un account locale o un account di dominio. Se non si usa un account di dominio, è necessario disabilitare il controllo dell'accesso utente remoto nel computer locale.

* Per aggiungere manualmente una chiave del registro di sistema che disabilita il controllo dell'accesso utente remoto:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Aggiungere un nuovo `DWORD`:`LocalAccountTokenFilterPolicy`
  * Impostare il valore su`1`

* Per aggiungere la chiave del registro di sistema, al prompt dei comandi eseguire il comando seguente:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Per Linux (**errore 95108**), è necessario scegliere l'account **radice** per l'installazione corretta dell'agente del servizio Mobility. Inoltre, è necessario che i servizi SSH File Transfer Protocol (SFTP) siano in esecuzione. Per abilitare il sottosistema SFTP e l'autenticazione della password nel file _sshd_config_ :

1. Accedere come utente **root**.
1. Passare a _nella/etc/ssh/sshd_config file_, trovare la riga che inizia con `PasswordAuthentication`.
1. Rimuovere il commento dalla riga e modificare il valore in `yes`.
1. Trovare la riga che inizia con `Subsystem`e rimuovere il commento dalla riga.
1. Riavviare il servizio `sshd` .

Se si desidera modificare le credenziali dell'account utente scelto, attenersi alle [istruzioni seguenti](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Errore privilegi insufficienti (ErrorID: 95517)

Quando l'utente scelto per l'installazione dell'agente di mobilità non dispone dei privilegi di amministratore, il server di configurazione/server di elaborazione con scalabilità orizzontale non sarà autorizzato a copiare il software dell'agente di mobilità nel computer di origine. Questo errore è dovuto a un errore di accesso negato. Assicurarsi che l'account utente disponga dei privilegi di amministratore.

Se si desidera modificare le credenziali dell'account utente scelto, attenersi alle [istruzioni seguenti](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Errore privilegi insufficienti (ErrorID: 95518)

Quando lo stabilimento di relazioni di trust tra il dominio primario e la workstation non riesce durante il tentativo di accesso alla macchina di origine, l'installazione dell'agente di mobilità non riesce con ID errore 95518. Assicurarsi che l'account utente usato per installare l'agente di mobilità disponga dei privilegi amministrativi per accedere tramite il dominio primario della macchina di origine.

Se si desidera modificare le credenziali dell'account utente scelto, attenersi alle [istruzioni](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)riportate di seguito.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Errori di accesso (ErrorID: 95519, 95520, 95521, 95522)

Questa sezione descrive le credenziali e i messaggi di errore di accesso.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Le credenziali dell'account utente sono state disabilitate (ErrorID: 95519)

L'account utente scelto durante l'abilitazione della replica è stato disabilitato. Per abilitare l'account utente, fare riferimento a [questo articolo](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) o eseguire il comando seguente sostituendo Text _username_ con il nome utente effettivo.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Credenziali bloccate a causa di più tentativi di accesso non riusciti (ErrorID: 95520)

Se si eseguono più tentativi di accesso a un computer senza esito positivo, il sistema blocca l'account utente. La cause dell'errore possono essere le seguenti:

* Le credenziali fornite durante l'installazione della configurazione non sono corrette.
* L'account utente scelto durante l'abilitazione della replica non è corretto.

Modificare le credenziali scelte seguendo [queste istruzioni](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e ripetere l'operazione.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>I server di accesso non sono disponibili nella macchina di origine (ErrorID: 95521)

Questo errore si verifica quando i server di accesso non sono disponibili nel computer di origine. Se i server di accesso non sono disponibili, le richieste di accesso avranno esito negativo e l'agente di mobilità non potrà essere installato. Per un accesso riuscito, verificare che i server di accesso siano disponibili nel computer di origine e avviare il servizio Netlogon. Per ulteriori informazioni, vedere [scenari di accesso di Windows](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Il servizio di accesso non è in esecuzione nella macchina di origine (ErrorID: 95522)

Il servizio di accesso non è in esecuzione nel computer di origine e ha causato l'errore della richiesta di accesso. Non è possibile installare l'agente di mobilità. Per risolvere l'errore, usare uno dei metodi seguenti per avviare il `Netlogon` servizio nel computer di origine:

* Per avviare il `Netlogon` servizio da un prompt dei comandi, eseguire il `net start Netlogon`comando.
* Da Gestione attività avviare il `Netlogon` servizio.

## <a name="connectivity-failure-errorid-95117--97118"></a>Errore di connettività (ErrorID: 95117 & 97118)

Il server di configurazione/server di elaborazione con scalabilità orizzontale tenta di connettersi alla VM di origine per installare l'agente di mobilità. Questo errore si verifica quando la macchina di origine non è raggiungibile a causa di problemi di connettività di rete.

Per risolvere l'errore:

* Assicurarsi che sia possibile effettuare il ping del computer di origine dal server di configurazione. Se è stato scelto il server di elaborazione con scalabilità orizzontale durante l'abilitazione della replica, assicurarsi che sia possibile effettuare il ping del computer di origine dal server di elaborazione.

* Dalla riga di comando del computer del server di `Telnet` origine usare per eseguire il ping del server di configurazione o del server di elaborazione con scalabilità orizzontale sulla porta HTTPS 135, come illustrato nel comando seguente. Questo comando controlla se sono presenti problemi di connettività di rete o problemi di blocco delle porte del firewall.

  `telnet <CS/ scale-out PS IP address> <135>`

* Inoltre, per una VM Linux:
  * Controllare se sono installati i pacchetti OpenSSH, OpenSSH server e OpenSSL più recenti.
  * Assicurarsi che il protocollo SSH (Secure Shell) sia abilitato e in esecuzione sulla porta 22.
  * I servizi SFTP devono essere in esecuzione. Per abilitare il sottosistema SFTP e l'autenticazione della password nel file _sshd_config_ :

    1. Accedere come utente **root**.
    1. Passare a _nella/etc/ssh/sshd_config_ file, trovare la riga che inizia con `PasswordAuthentication`.
    1. Rimuovere il commento dalla riga e modificare il valore in `yes`.
    1. Trovare la riga che inizia con `Subsystem`e rimuovere il commento dalla riga
    1. Riavviare il servizio `sshd` .

* Un tentativo di connessione potrebbe non riuscire se non sono presenti risposte corrette dopo un periodo di tempo o se una connessione stabilita non è riuscita perché un host connesso non è riuscito a rispondere.
* Potrebbe trattarsi di un problema relativo alla connettività, alla rete o al dominio. Il problema potrebbe essere dovuto al fatto che il nome DNS risolve un problema o un problema di esaurimento delle porte TCP. Controllare se sono presenti problemi noti di questo tipo nel dominio.

## <a name="connectivity-failure-errorid-95523"></a>Errore di connettività (ErrorID: 95523)

Questo errore si verifica quando la rete in cui risiede la macchina di origine non è stata trovata, potrebbe essere stata eliminata o non è più disponibile. L'unico modo per risolvere l'errore consiste nel verificare che la rete esista.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Controllo del servizio di condivisione di file e stampanti (ID errore: 95105 e 95106)

Dopo un controllo della connettività, verificare se il servizio di condivisione file e stampanti è abilitato nella macchina virtuale. Queste impostazioni sono necessarie per copiare l'agente di mobilità nella macchina di origine.

Per **Windows 2008 R2 e versioni precedenti**:

* Per abilitare la condivisione di file e stampanti tramite Windows Firewall,
  1. Aprire **il pannello** > **di controllo sistema e** > **Windows Firewall**sicurezza. Nel riquadro sinistro selezionare **Impostazioni** > avanzate**regole in ingresso** nell'albero della console.
  1. Individuare le regole Condivisione file e stampanti (NB-Session-In) e Condivisione file e stampanti (SMB-In).
  1. Su ogni regola fare clic con il pulsante destro del mouse e quindi fare clic su **Abilita regola**.

* Per abilitare la condivisione di file con Criteri di gruppo:
  1. Passare a **Start**, digitare `gpmc.msc` e cercare.
  1. Nel riquadro di spostamento aprire le cartelle seguenti:**configurazione** > **Administrative Templates** > **Windows Components** > utente >  **criteri computer locale**modelli amministrativi**condivisione di rete**componenti di Windows.
  1. Nel riquadro dei dettagli fare doppio clic su **Impedisci di condividere file nel profilo utente**.

     Per disabilitare l'impostazione Criteri di gruppo e abilitare la capacità dell'utente di condividere i file, selezionare **disabilitato**.

  1. Selezionare **OK** per salvare le modifiche.

  Per altre informazioni, vedere [abilitare o disabilitare la condivisione di file con criteri di gruppo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Per abilitare la condivisione di file e stampanti per le versioni successive di Windows o Linux, seguire le istruzioni in [installare il servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Verifica della configurazione di Strumentazione gestione Windows (WMI) (codice errore: 95103)

Dopo la verifica dei servizi file e stampanti, abilitare il servizio WMI per i profili privato, pubblico e di dominio attraverso il firewall. Queste impostazioni sono necessarie per completare l'esecuzione remota nel computer di origine.

Per abilitare WMI:

1. Passare a **Pannello** > di controllo**sicurezza** e selezionare **Windows Firewall**.
1. Selezionare **Cambia impostazioni** , quindi selezionare la scheda **eccezioni** .
1. Nella finestra **eccezioni** selezionare la casella di controllo Strumentazione gestione Windows (WMI) per abilitare il traffico WMI attraverso il firewall.

È anche possibile abilitare il traffico WMI attraverso il firewall dal prompt dei comandi con il comando seguente:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Di seguito sono elencati altri articoli sulla risoluzione dei problemi di WMI.

* [Test WMI di base](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Risoluzione dei problemi WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Risoluzione dei problemi con gli script WMI e i servizi WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Sistemi operativi non supportati

Un altro motivo comune per l'errore potrebbe essere causato da un sistema operativo non supportato. Usare un sistema operativo e una versione del kernel supportati per una corretta installazione del servizio Mobility. Evitare l'uso di patch private.

Per visualizzare l'elenco dei sistemi operativi e le versioni del kernel supportate da Azure Site Recovery, vedere il documento relativo alla [matrice di supporto](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurazioni del disco di avvio non supportate (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Le partizioni e i volumi di avvio e di sistema non hanno lo stesso disco (ErrorID: 95309)

Prima della versione 9,20, le partizioni e i volumi di avvio e di sistema in dischi diversi erano una configurazione non supportata. A partire dalla [versione 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), questa configurazione è supportata.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Il disco di avvio non è disponibile (ErrorID: 95310)

Una macchina virtuale senza un disco di avvio non può essere protetta. Un disco di avvio garantisce un ripristino uniforme di una macchina virtuale durante un'operazione di failover. L'assenza di un disco di avvio comporta un errore di avvio del computer dopo il failover. Verificare che la macchina virtuale contenga un disco di avvio e ripetere l'operazione. Inoltre, non sono supportati più dischi di avvio nello stesso computer.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Nella macchina di origine sono presenti più dischi di avvio (ErrorID: 95311)

Una macchina virtuale con più dischi di avvio non è una [configurazione supportata](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partizione di sistema su più dischi (ErrorID: 95313)

Prima della versione 9,20, la configurazione di una partizione radice o di un volume su più dischi era una configurazione non supportata. A partire dalla [versione 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), questa configurazione è supportata.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>L'abilitazione della protezione non è riuscita perché il nome del dispositivo è indicato nella configurazione di GRUB anziché in UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Possibile causa

I file di configurazione di Grand Unified Bootloader (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/GRUB2/grub.cfg_o _/etc/default/grub_) possono contenere il valore per la **radice** dei parametri e **riprendere** come nomi di dispositivo effettivi anziché come identificatore univoco universale (UUID). Site Recovery impone l'approccio UUID perché i nomi dei dispositivi possono cambiare al riavvio della macchina virtuale. Ad esempio, la macchina virtuale potrebbe non tornare online con lo stesso nome in caso di failover e ciò comporta problemi.

Ad esempio:

- La riga seguente si trova nel file GRUB _/boot/GRUB2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- La riga seguente si trova nel file GRUB _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> Le linee di GRUB contengono i nomi di dispositivo effettivi per la **radice** dei parametri e vengono **ripresi** anziché l'UUID.

### <a name="how-to-fix"></a>Correzione

I nomi dei dispositivi devono essere sostituiti con l'UUID corrispondente.

1. Trovare l'UUID del dispositivo eseguendo il comando `blkid \<device name>`.

   Ad esempio:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. A questo punto, sostituire il nome del dispositivo con il relativo `root=UUID=\<UUID>`UUID nel formato come. Ad esempio, se si sostituiscono i nomi di dispositivo con UUID per root e Resume parametro indicato nei file _/boot/GRUB2/grub.cfg_, _/boot/GRUB2/grub.cfg_o _/etc/default/grub_ , le righe nei file appaiono come la riga seguente:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Riavviare la protezione.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installare il servizio Mobility completato con avviso per il riavvio (ErrorID: 95265 & 95266)

Site Recovery servizio Mobility include molti componenti, uno dei quali è denominato driver di filtro. Il driver del filtro viene caricato nella memoria di sistema solo durante un riavvio del sistema. Le correzioni dei driver di filtro possono essere realizzate solo quando viene caricato un nuovo driver di filtro al momento del riavvio del sistema.

> [!IMPORTANT]
> Si tratta di un avviso e la replica esistente funzionerà anche dopo il nuovo aggiornamento dell'agente. È possibile scegliere di riavviare ogni volta che si desidera ottenere i vantaggi del nuovo driver di filtro, ma se non si riavvia, il driver del filtro precedente continuerà a funzionare. Quindi, dopo un aggiornamento senza riavvio, ad eccezione del driver del filtro, **vengono realizzati i vantaggi di altri miglioramenti e correzioni del servizio Mobility**. Sebbene sia consigliabile, non è obbligatorio riavviare il computer dopo ogni aggiornamento. Per informazioni su quando un riavvio è obbligatorio, impostare la sezione [Riavvia dopo l'aggiornamento del servizio Mobility](service-updates-how-to.md#reboot-after-mobility-service-upgrade) in aggiornamenti del servizio in Azure Site Recovery.

> [!TIP]
>Per le procedure consigliate per la pianificazione degli aggiornamenti durante la finestra di manutenzione, vedere il [supporto per il sistema operativo più recente/kernel](service-updates-how-to.md#support-for-latest-operating-systemskernels) negli aggiornamenti dei servizi in Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Supporto LVM dalla versione 9.20

Prima della versione 9,20, Logical Volume Manager (LVM) era supportato solo per i dischi dati. La `/boot` partizione deve trovarsi in una partizione del disco e non in un volume LVM.

A partire dalla [versione 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), il [disco del sistema operativo su LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) è supportato.

## <a name="insufficient-space-errorid-95524"></a>Spazio insufficiente (ErrorID: 95524)

Quando l'agente di mobilità viene copiato nella macchina di origine, è necessario almeno 100 MB di spazio disponibile. Verificare che il computer di origine disponga della quantità di spazio disponibile necessaria e ripetere l'operazione.

## <a name="vss-installation-failures"></a>Errori di installazione di VSS

L'installazione del servizio Copia Shadow del volume (VSS) fa parte dell'installazione dell'agente di mobilità. Questo servizio viene utilizzato nel processo per generare punti di ripristino coerenti con l'applicazione. I motivi degli errori di installazione di VSS possono essere diversi. Per identificare gli errori esatti, vedere _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Alcuni degli errori comuni e i passaggi di risoluzione sono evidenziati nella sezione seguente.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Errore VSS -2147023170 [0x800706BE] - codice di uscita 511

Questo problema si verifica più spesso quando il software antivirus blocca le operazioni dei servizi Azure Site Recovery.

Per risolvere il problema:

1. Esaminare l'elenco di [esclusioni di cartelle dal programma antivirus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Seguire le linee guida pubblicate dal provider antivirus per sbloccare la registrazione della DLL in Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Errore VSS 7 [0x7] - codice di uscita 511

Si tratta di un errore di runtime causato da una memoria insufficiente per installare VSS. Aumentare lo spazio su disco per completare correttamente l'operazione.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Errore VSS -2147023824 [0x80070430] - codice di uscita 517

Questo errore si verifica quando il servizio Provider VSS di Azure Site Recovery è [contrassegnato per l'eliminazione](/previous-versions/ms838153(v=msdn.10)). Provare a installare VSS manualmente nel computer di origine eseguendo il comando seguente:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Errore VSS -2147023841 [0x8007041F] - codice di uscita 512

Questo errore si verifica quando Azure Site Recovery database del servizio provider VSS è [bloccato](/previous-versions/ms833798(v=msdn.10)). Provare a installare VSS manualmente nel computer di origine eseguendo il comando seguente da un prompt dei comandi:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Quando si verifica un errore, controllare se un programma antivirus o altri servizi sono bloccati in uno stato **iniziale** . Un processo in uno stato **iniziale** può mantenere il blocco sui servizi di database. Si verificheranno errori nell'installazione del provider VSS. Verificare che nessun servizio sia in uno stato **iniziale** , quindi riprovare a eseguire l'operazione precedente.

### <a name="vss-exit-code-806"></a>Errore VSS codice di uscita 806

Questo errore si verifica quando l'account utente utilizzato per l'installazione non dispone delle autorizzazioni `CSScript` necessarie per eseguire il comando. Concedere all'account utente le autorizzazioni necessarie per eseguire lo script e ripetere l'operazione.

### <a name="other-vss-errors"></a>Altri errori VSS

Provare a installare manualmente il servizio del provider VSS nel computer di origine eseguendo il comando seguente da un prompt dei comandi:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Errore VSS-0x8004E00F

Questo errore si verifica in genere durante l'installazione dell'agente di mobilità a causa `DCOM` di `DCOM` problemi in e si trova in uno stato critico.

Utilizzare la procedura seguente per determinare la cause dell'errore.

### <a name="examine-the-installation-logs"></a>Esaminare i log di installazione

1. Aprire il log di installazione disponibile in _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. La presenza dell'errore seguente indica questo problema:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Per risolvere il problema:

Contattare il [team della piattaforma Microsoft Windows](https://aka.ms/Windows_Support) per ottenere assistenza per la risoluzione del problema DCOM.

Quando il problema DCOM viene risolto, reinstallare manualmente il provider VSS Azure Site Recovery usando il comando seguente da un prompt dei comandi:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Se la coerenza dell'applicazione non è cruciale per i requisiti di ripristino di emergenza, è possibile ignorare l'installazione del provider VSS.

Per ignorare l'installazione del provider VSS Azure Site Recovery e installare manualmente Azure Site Recovery provider VSS dopo l'installazione:

1. Installare il servizio Mobility. L'installazione avrà esito negativo nel passaggio: **post-installazione configurazione**.
1. Per ignorare l'installazione VSS:
   1. Aprire la directory di installazione del servizio Mobility Azure Site Recovery disponibile all'indirizzo:

      _C:\Programmi (x86) \Microsoft Azure site Recovery\agent_

   1. Modificare gli script di installazione del provider VSS Azure Site Recovery _InMageVSSProvider_Install_ e _InMageVSSProvider_Uninstall. cmd_ per avere sempre esito positivo aggiungendo le righe seguenti:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Eseguire un'installazione manuale dell'agente di mobilità.
1. Quando l'installazione ha esito positivo e passa al passaggio successivo, **configurare**e rimuovere le righe aggiunte.
1. Per installare il provider VSS, aprire un prompt dei comandi come amministratore ed eseguire il comando seguente:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Verificare che il provider Azure Site Recovery VSS sia installato come servizio nei servizi di Windows. Aprire il servizio componente MMC per verificare che sia elencato il provider VSS.
1. Se l'installazione del provider VSS continua a non riuscire, rivolgersi al supporto tecnico per risolvere gli errori relativi alle autorizzazioni in CAPI2 (Cryptographic Application Programming Interface).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>L'installazione del provider VSS non riesce perché il servizio cluster è abilitato nel computer non cluster

Questo problema causa l'esito negativo dell'installazione dell'agente di Azure Site Recovery Mobility durante l'installazione del provider Azure Site Recovery VSS. L'errore è dovuto a `COM+` un problema che impedisce l'installazione del provider VSS.

### <a name="to-identify-the-issue"></a>Per identificare il problema

Nel log che si trova nel server di configurazione alla _Data/ora C:\ProgramData\ASRSetupLogs\UploadedLogs\<>UA_InstallLogFile. log_ è disponibile la seguente eccezione:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Per risolvere il problema:

1. Verificare che il computer non sia un cluster e che i componenti del cluster non vengano utilizzati.
1. Se i componenti non vengono usati, rimuovere i componenti del cluster dal computer.

## <a name="drivers-are-missing-on-the-source-server"></a>Driver mancanti nel server di origine

Se l'installazione dell'agente di mobilità non riesce, esaminare i log in _C:\ProgramData\ASRSetupLogs_ per determinare se alcuni dei driver necessari sono mancanti in alcuni set di controlli.

Per risolvere il problema:

1. Utilizzando un editor del registro di `regedit.msc`sistema, ad esempio, aprire il registro di sistema.
1. Aprire il `HKEY_LOCAL_MACHINE\SYSTEM` nodo.
1. Nel `SYSTEM` nodo individuare i set di controlli.
1. Aprire ogni set di controlli e verificare che siano presenti i driver di Windows seguenti:

   * ATAPI
   * VMBus
   * Storflt
   * Storvsc
   * Intelide

1. Reinstallare i driver mancanti.

## <a name="next-steps"></a>Passaggi successivi

[Altre](vmware-azure-tutorial.md) informazioni su come configurare il ripristino di emergenza per le macchine virtuali VMware.
