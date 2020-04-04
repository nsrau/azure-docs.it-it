---
title: Risolvere i problemi relativi all'installazione push del servizio Mobility con Azure Site RecoveryTroubleshoot Mobility Service push installation with Azure Site Recovery
description: Risolvere gli errori di installazione di Servizi per dispositivi mobili quando si abilita la replica per il ripristino di emergenza con Azure Site Recovery.Troubleshoot Mobility Services installation errors when enabling replication for disaster recovery with Azure Site Recovery.
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
# <a name="troubleshoot-mobility-service-push-installation"></a>Risolvere i problemi relativi all'installazione push del servizio MobilityTroubleshoot Mobility service push installation

L'installazione del servizio Mobility è un passaggio chiave per abilitare la replica. L'esito positivo di questo passaggio dipende dalla conformità dei prerequisiti e dall'utilizzo delle configurazioni supportate. Gli errori più comuni che potrebbero verificarsi durante l'installazione del servizio Mobility sono dovuti a quanto:The most common failures you might face during the Mobility service installation are due to:

* [Errori relativi a credenziali o privilegi](#credentials-check-errorid-95107--95108)
* [Errori di accesso](#login-failures-errorid-95519-95520-95521-95522)
* [Errori di connettività](#connectivity-failure-errorid-95117--97118)
* [Errori di condivisione file e stampanti](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Errori di Strumentazione gestione Windows (WMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemi operativi non supportati](#unsupported-operating-systems)
* [Configurazioni di avvio non supportate](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Errori di installazione del servizio Copia Shadow del volume (VSS)](#vss-installation-failures)
* [Nome del dispositivo nella configurazione GRUB invece del dispositivo UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume di Logical Volume Manager (LVM)](#lvm-support-from-920-version)
* [Avvisi di riavvio](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando si abilita la replica, Azure Site Recovery tenta di installare l'agente del servizio Mobility nella macchina virtuale (VM). Come parte di questo processo, il server di configurazione tenta di connettersi alla macchina virtuale e copiare l'agente. Per abilitare la corretta installazione, seguire le istruzioni dettagliate per la risoluzione dei problemi.

## <a name="credentials-check-errorid-95107--95108"></a>Controllo delle credenziali (ID errore: 95107 e 95108)

Verificare se l'account utente scelto durante l'abilitazione della replica è valido e accurato. Azure Site Recovery richiede l'account **radice** o l'account utente con privilegi di **amministratore** per eseguire un'installazione push. In caso contrario, l'installazione push verrà bloccata nel computer di origine.

Per Windows (**errore 95107**), verificare che l'account utente disponga dell'accesso amministrativo nel computer di origine, con un account locale o di dominio. Se non si utilizza un account di dominio, è necessario disabilitare il controllo di accesso utente remoto nel computer locale.

* Per aggiungere manualmente una chiave del Registro di sistema che disabilita il controllo di accesso utente remoto:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Aggiungere un `DWORD`nuovo :`LocalAccountTokenFilterPolicy`
  * Impostare il valore su`1`

* Per aggiungere la chiave del Registro di sistema, da un prompt dei comandi, eseguire il comando seguente:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Per Linux (**errore 95108**), è necessario scegliere l'account **radice** per la corretta installazione dell'agente del servizio Mobility. Inoltre, i servizi SSH File Transfer Protocol (SFTP) devono essere in esecuzione. Per abilitare il sottosistema SFTP e l'autenticazione con password nel file _sshd_config:_

1. Accedere come utente **root**.
1. Vai al _file /etc/ssh/sshd_config_, trova `PasswordAuthentication`la riga che inizia con .
1. Rimuovere il commento dalla riga `yes`e modificare il valore in .
1. Individuare la riga `Subsystem`che inizia con e rimuovere il commento dalla riga.
1. Riavviare il servizio `sshd` .

Se si desidera modificare le credenziali dell'account utente scelto, seguire [queste istruzioni.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="insufficient-privileges-failure-errorid-95517"></a>Errore di privilegi insufficienti (ErrorID: 95517)

Quando l'utente scelto di installare l'agente Mobility non dispone di privilegi di amministratore, il server di configurazione/server di elaborazione con scalabilità orizzontale non sarà autorizzato a copiare il software dell'agente Mobility nel computer di origine. Questo errore è il risultato di un errore di accesso negato. Assicurarsi che l'account utente disponga dei privilegi di amministratore.

Se si desidera modificare le credenziali dell'account utente scelto, seguire [queste istruzioni.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="insufficient-privileges-failure-errorid-95518"></a>Errore di privilegi insufficienti (ErrorID: 95518)

Quando la creazione della relazione di trust di dominio tra il dominio primario e la workstation non riesce durante il tentativo di accedere al computer di origine, l'installazione dell'agente Mobility ha esito negativo con ID errore 95518. Assicurarsi che l'account utente utilizzato per installare l'agente Mobility disponga di privilegi amministrativi per accedere tramite il dominio primario del computer di origine.

Se si desidera modificare le credenziali dell'account utente selezionato, seguire le [istruzioni riportate](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)di seguito.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Errori di accesso (Id errore: 95519, 95520, 95521, 95522)

In questa sezione vengono descritti i messaggi di errore relativi alle credenziali e all'accesso.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Le credenziali dell'account utente sono state disabilitate (ErrorID: 95519)

L'account utente scelto durante l'abilitazione della replica è stato disabilitato. Per abilitare l'account utente, fare riferimento a [questo articolo](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) o eseguire il comando seguente sostituendo _text username_ con il nome utente effettivo.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Credenziali bloccate a causa di più tentativi di accesso non riusciti (ErrorID: 95520)

Se si eseguono più tentativi di accesso a un computer senza esito positivo, il sistema blocca l'account utente. La cause dell'errore possono essere le seguenti:

* Le credenziali fornite durante l'installazione della configurazione non sono corrette.
* L'account utente scelto durante l'abilitazione della replica è errato.

Modificare le credenziali scelte seguendo [queste istruzioni](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e ripetere l'operazione.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>I server di accesso non sono disponibili nel computer di origine (ErrorID: 95521)

Questo errore si verifica quando i server di accesso non sono disponibili nel computer di origine. Se i server di accesso non sono disponibili, le richieste di accesso avranno esito negativo e non sarà possibile installare l'agente Mobility. Per un accesso corretto, assicurarsi che i server di accesso siano disponibili nel computer di origine e avviare il servizio Accesso rete. Per ulteriori informazioni, vedere [Scenari di accesso di Windows](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Il servizio di accesso non è in esecuzione nel computer di origine (ErrorID: 95522)

Il servizio di accesso non è in esecuzione nel computer di origine e ha causato un errore della richiesta di accesso. L'agente Mobility non può essere installato. Per risolvere l'errore, utilizzare uno dei `Netlogon` seguenti metodi per avviare il servizio nel computer di origine:

* Per avviare il `Netlogon` servizio da un `net start Netlogon`prompt dei comandi, eseguire il comando .
* Da Task Manager, `Netlogon` avviare il servizio.

## <a name="connectivity-failure-errorid-95117--97118"></a>Errore di connettività (ErrorID: 95117 & 97118)

Il server di configurazione/server di elaborazione con scalabilità orizzontale tenta di connettersi alla macchina virtuale di origine per installare l'agente Mobility. Questo errore si verifica quando il computer di origine non è raggiungibile perché si verificano problemi di connettività di rete.

Per risolvere l'errore:

* Assicurarsi di poter eseguire il ping del computer di origine dal server di configurazione. Se è stato scelto il server di elaborazione con scalabilità orizzontale durante l'abilitazione della replica, assicurarsi di poter eseguire il ping del computer di origine dal server di elaborazione.

* Dalla riga di comando del `Telnet` computer server di origine, utilizzare per eseguire il ping del server di configurazione o del server di elaborazione con scalabilità orizzontale sulla porta HTTPS 135, come illustrato nel comando seguente. Questo comando controlla se sono presenti problemi di connettività di rete o problemi di blocco delle porte del firewall.

  `telnet <CS/ scale-out PS IP address> <135>`

* Inoltre, per una macchina virtuale Linux:Additionally, for a Linux VM:
  * Verificare se sono installati i pacchetti OpenSSH, OpenSSH Server e OpenSSL più recenti.
  * Assicurarsi che il protocollo SSH (Secure Shell) sia abilitato e in esecuzione sulla porta 22.
  * I servizi SFTP devono essere in esecuzione. Per abilitare l'autenticazione del sottosistema SFTP e della password nel file _sshd_config:_

    1. Accedere come utente **root**.
    1. Vai al file _/etc/ssh/sshd_config,_ trova `PasswordAuthentication`la riga che inizia con .
    1. Rimuovere il commento dalla riga `yes`e modificare il valore in .
    1. Trovare la riga `Subsystem`che inizia con , e rimuovere il commento dalla riga
    1. Riavviare il servizio `sshd` .

* Un tentativo di connessione potrebbe non essere riuscito se non sono presenti risposte appropriate dopo un periodo di tempo o se una connessione stabilita non è riuscita perché un host connesso non ha risposto.
* Potrebbe trattarsi di un problema relativo alla connettività/rete/dominio. Potrebbe anche essere perché il nome DNS risoluzione problema o problema di esaurimento della porta TCP. Controllare se sono presenti problemi noti di questo tipo nel dominio.

## <a name="connectivity-failure-errorid-95523"></a>Errore di connettività (ErrorID: 95523)

Questo errore si verifica quando la rete in cui risiede il computer di origine non viene trovata, potrebbe essere stata eliminata o non è più disponibile. L'unico modo per risolvere l'errore consiste nel verificare che la rete esista.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Controllo del servizio di condivisione di file e stampanti (ID errore: 95105 e 95106)

Dopo un controllo di connettività, verificare se il servizio di condivisione di file e stampanti è abilitato nella macchina virtuale. Queste impostazioni sono necessarie per copiare l'agente Mobility nel computer di origine.

Per **Windows 2008 R2 e versioni precedenti**:

* Per abilitare la condivisione di file e stampanti tramite Windows Firewall,
  1. Aprire **Il Pannello di controllo** > **Sistema e sicurezza** > **di Windows Firewall**. Nel riquadro sinistro selezionare**Regole connessioni** **avanzate** > in ingresso nell'albero della console.
  1. Individuare le regole Condivisione file e stampanti (NB-Session-In) e Condivisione file e stampanti (SMB-In).
  1. Su ogni regola fare clic con il pulsante destro del mouse e quindi fare clic su **Abilita regola**.

* Per abilitare la condivisione dei file con Criteri di gruppo:
  1. Vai **Start**a Start `gpmc.msc` , digitare e cercare.
  1. Nel riquadro di spostamento aprire le seguenti cartelle:**Configurazione utente** >  **criterio** > computer locale Modelli**amministrativi** > Di Windows**Componenti** > **di condivisione di rete**.
  1. Nel riquadro dei dettagli fare doppio clic su **Impedisci di condividere file nel profilo utente**.

     Per disattivare l'impostazione di Criteri di gruppo e abilitare la possibilità dell'utente di condividere file, selezionare **Disabilitato**.

  1. Selezionare **OK** per salvare le modifiche.

  Per ulteriori informazioni, vedere [Abilitare o disabilitare Condivisione file con Criteri di gruppo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Per abilitare la condivisione di file e stampanti per le versioni successive di Windows o Linux, seguire le istruzioni in [Installare il servizio Mobility per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici.](vmware-azure-install-mobility-service.md)

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Controllo della configurazione di Strumentazione gestione Windows (WMI) (codice di errore: 95103)

Dopo il controllo dei servizi file e stampanti, abilitare il servizio WMI per i profili privati, pubblici e di dominio attraverso il firewall. Queste impostazioni sono necessarie per completare l'esecuzione remota nel computer di origine.

Per abilitare WMI:

1. Passare a**Protezione** **del Pannello di** > controllo e selezionare Windows **Firewall**.
1. Selezionare **Modifica impostazioni** e quindi selezionare la scheda **Eccezioni.**
1. Nella finestra **Eccezioni** selezionare la casella di controllo Strumentazione gestione Windows (WMI) per abilitare il traffico WMI attraverso il firewall.

È inoltre possibile abilitare il traffico WMI attraverso il firewall dal prompt dei comandi con il seguente comando:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Di seguito sono elencati altri articoli sulla risoluzione dei problemi di WMI.

* [Test WMI di base](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Risoluzione dei problemi WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Risoluzione dei problemi con gli script WMI e i servizi WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Sistemi operativi non supportati

Un altro motivo comune per l'errore potrebbe essere il risultato di un sistema operativo non supportato. Utilizzare un sistema operativo e una versione del kernel supportati per una corretta installazione del servizio Mobility. Evitare l'uso di patch private.

Per visualizzare l'elenco dei sistemi operativi e delle versioni del kernel supportate da Azure Site Recovery, vedere il documento della matrice di [supporto.](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurazioni del disco di avvio non supportate (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Le partizioni/volumi di avvio e di sistema non sono lo stesso disco (ErrorID: 95309)

Prima della versione 9.20, le partizioni/volumi di avvio e di sistema su dischi diversi erano una configurazione non supportata. A partire dalla [versione 9.20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), questa configurazione è supportata.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Il disco di avvio non è disponibile (ErrorID: 95310)

Una macchina virtuale senza disco di avvio non può essere protetta. Un disco di avvio garantisce un ripristino regolare di una macchina virtuale durante un'operazione di failover. L'assenza di un disco di avvio comporta un errore di avvio del computer dopo il failover. Verificare che la macchina virtuale contenga un disco di avvio e ripetere l'operazione. Inoltre, non sono supportati più dischi di avvio sullo stesso computer.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Più dischi di avvio presenti nel computer di origine (ErrorID: 95311)

Una macchina virtuale con più dischi di avvio non è una [configurazione supportata.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partizione di sistema su più dischi (ErrorID: 95313)

Prima della versione 9.20, l'installazione di una partizione radice o di un volume su più dischi era una configurazione non supportata. A partire dalla [versione 9.20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), questa configurazione è supportata.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Abilita protezione non riuscita come nome del dispositivo indicato nella configurazione GRUB anziché UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Possibile causa

I file di configurazione del Grand Unified Bootloader (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_o _/etc/default/grub_) possono contenere il valore per la **radice** dei parametri e **riprenderla** come nomi di periferica effettivi anziché come identificatore univoco universale (UUID). Site Recovery impone l'approccio UUID poiché i nomi dei dispositivi possono cambiare al riavvio della macchina virtuale. Ad esempio, la macchina virtuale potrebbe non essere online con lo stesso nome in caso di failover e ciò comporta problemi.

Ad esempio:

- La riga seguente è tratta dal file GRUB _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- La riga seguente è tratta dal file GRUB _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> Le righe GRUB contengono i nomi effettivi dei dispositivi per la **radice** dei parametri e la **ripresa** anziché l'UUID.

### <a name="how-to-fix"></a>Come risolvere il problema

I nomi dei dispositivi devono essere sostituiti con l'UUID corrispondente.

1. Trovare l'UUID del dispositivo eseguendo `blkid \<device name>`il comando .

   Ad esempio:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Ora sostituire il nome del dispositivo con `root=UUID=\<UUID>`il relativo UUID nel formato come . Ad esempio, se sostituiamo i nomi dei dispositivi con UUID per root e resume parametro menzionato nei file _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_, o _/etc/default/grub_ quindi le righe nei file simile alla seguente riga:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Riavviare la protezione.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installare il servizio Mobility completato con avviso di riavvio (ErrorID: 95265 & 95266)

Il servizio Per dispositivi mobili di Site Recovery dispone di molti componenti, uno dei quali è denominato driver di filtro. Il driver di filtro viene caricato nella memoria di sistema solo durante un riavvio del sistema. Le correzioni dei driver di filtro possono essere realizzate solo quando viene caricato un nuovo driver di filtro al momento del riavvio del sistema.

> [!IMPORTANT]
> Si tratta di un avviso e la replica esistente funzionerà anche dopo l'aggiornamento del nuovo agente. È possibile scegliere di riavviare ogni volta che si desidera ottenere i vantaggi del nuovo driver di filtro, ma se non si riavvia, il driver di filtro precedente continua a funzionare. Pertanto, dopo un aggiornamento senza riavvio, ad eccezione del driver di filtro, **i vantaggi di altri miglioramenti e correzioni nel servizio Mobility vengono realizzati**. Sebbene sia consigliato, non è obbligatorio riavviare dopo ogni aggiornamento. Per informazioni su quando è obbligatorio riavviare il riavvio, [impostare](service-updates-how-to.md#reboot-after-mobility-service-upgrade) la sezione Riavvia dopo l'aggiornamento del servizio Mobility in Aggiornamenti del servizio in Azure Site Recovery.For information about when a reboot is mandatory, set the Reboot after Mobility service upgrade section in Service updates in Azure Site Recovery.

> [!TIP]
>Per le procedure consigliate per la pianificazione degli aggiornamenti durante la finestra di manutenzione, vedere Supporto per gli aggiornamenti più recenti del sistema operativo/kernel in Aggiornamenti del servizio in Azure Site Recovery.For best practices on scheduling upgrades during your maintenance window, see the [Support for latest operating system/kernel](service-updates-how-to.md#support-for-latest-operating-systemskernels) in Service updates in Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Supporto LVM dalla versione 9.20

Prima della versione 9.20, Logical Volume Manager (LVM) era supportato solo per i dischi dati. La `/boot` partizione deve trovarsi in una partizione del disco e non in un volume LVM.

A partire dalla [versione 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)è supportato il disco del sistema [operativo su LVM.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="insufficient-space-errorid-95524"></a>Spazio insufficiente (Id Errore: 95524)

Quando l'agente Mobility viene copiato nel computer di origine, sono necessari almeno 100 MB di spazio libero. Assicurarsi che il computer di origine disponga della quantità di spazio libero necessaria e ripetere l'operazione.

## <a name="vss-installation-failures"></a>Errori di installazione di VSS

L'installazione del servizio Copia Shadow del volume (VSS) fa parte dell'installazione dell'agente Mobility. Questo servizio viene utilizzato nel processo per generare punti di ripristino coerenti con l'applicazione. I motivi degli errori di installazione di VSS possono essere diversi. Per identificare gli errori esatti, fare riferimento a _C:_ Alcuni degli errori comuni e i passaggi di risoluzione sono evidenziati nella sezione seguente.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Errore VSS -2147023170 [0x800706BE] - codice di uscita 511

Questo problema si verifica più spesso quando il software antivirus blocca le operazioni dei servizi di Azure Site Recovery.

Per risolvere il problema:

1. Esaminare l'elenco delle [esclusioni](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)di cartelle dal programma antivirus .
1. Seguire le linee guida pubblicate dal provider antivirus per sbloccare la registrazione della DLL in Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Errore VSS 7 [0x7] - codice di uscita 511

Questo errore è un errore di runtime che ha causato perché la memoria insufficiente per installare VSS. Aumentare lo spazio su disco per il corretto completamento di questa operazione.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Errore VSS -2147023824 [0x80070430] - codice di uscita 517

Questo errore si verifica quando il servizio Provider VSS di Azure Site Recovery è [contrassegnato per l'eliminazione](/previous-versions/ms838153(v=msdn.10)). Provare a installare VSS manualmente nel computer di origine eseguendo il comando seguente:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Errore VSS -2147023841 [0x8007041F] - codice di uscita 512

Questo errore si verifica quando il database del servizio provider VSS di Azure Site Recovery è [bloccato.](/previous-versions/ms833798(v=msdn.10)) Provare a installare VSS manualmente nel computer di origine eseguendo il comando seguente da un prompt dei comandi:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Quando si verifica un errore, verificare se qualsiasi programma antivirus o altri servizi sono bloccati nello stato **Iniziale.** Un processo in stato **Iniziale** può mantenere il blocco sui servizi di database. Ciò comporterà errori nell'installazione del provider VSS. Assicurarsi che nessun servizio sia in stato **iniziale,** quindi ripetere l'operazione precedente.

### <a name="vss-exit-code-806"></a>Errore VSS codice di uscita 806

Questo errore si verifica quando l'account utente utilizzato `CSScript` per l'installazione non dispone delle autorizzazioni per eseguire il comando. Concedere all'account utente le autorizzazioni necessarie per eseguire lo script e ripetere l'operazione.

### <a name="other-vss-errors"></a>Altri errori VSS

Provare a installare manualmente il servizio provider VSS nel computer di origine eseguendo il comando seguente da un prompt dei comandi:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Errore VSS - 0x8004E00F

Questo errore si verifica in genere durante l'installazione dell'agente Mobility a causa di problemi in `DCOM` ed `DCOM` è in uno stato critico.

Utilizzare la procedura seguente per determinare la causa dell'errore.

### <a name="examine-the-installation-logs"></a>Esaminare i registri di installazione

1. Aprire il registro di installazione che si trova nel _percorso C:_
2. La presenza del seguente errore indica questo problema:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Per risolvere il problema:

Contattare il team della [piattaforma Microsoft Windows](https://aka.ms/Windows_Support) per ottenere assistenza per la risoluzione del problema DCOM.

Quando il problema DCOM viene risolto, reinstallare manualmente il provider VSS di Azure Site Recovery utilizzando il comando seguente da un prompt dei comandi:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Se la coerenza dell'applicazione non è fondamentale per i requisiti di ripristino di emergenza, è possibile ignorare l'installazione del provider VSS.

Per ignorare l'installazione del provider VSS di Azure Site Recovery e installare manualmente la postinstallazione del provider VSS di Azure Site Recovery:To bypass the Azure Site Recovery VSS Provider installation and manually install Azure Site Recovery VSS Provider post installation:

1. Installare il servizio Mobility. L'installazione avrà esito negativo nel passaggio: **Configurazione post-installazione**.
1. Per ignorare l'installazione di VSS:
   1. Aprire la directory di installazione del servizio per dispositivi mobili di Azure Site Recovery disponibile in:Open the Azure Site Recovery Mobility Service installation directory located at:

      _C: (x86) file di programma (x86) Microsoft Azure Site Recovery agente_

   1. Modificare gli script di installazione del provider VSS di Azure Site Recovery InMageVSSProvider_Install e InMageVSSProvider_Uninstall.cmd per avere sempre esito positivo aggiungendo le righe seguenti:Modify the Azure Site Recovery VSS Provider installation scripts _InMageVSSProvider_Install_ and _InMageVSSProvider_Uninstall.cmd_ to always succeed by adding the following lines:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Eseguire un'installazione manuale dell'agente Mobility.
1. Quando l'installazione ha esito positivo e passa al passaggio successivo, **Configura**, rimuovere le righe aggiunte.
1. Per installare il provider VSS, aprire un prompt dei comandi come amministratore ed eseguire il comando seguente:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Verificare che il provider VSS di Azure Site Recovery sia installato come servizio in Servizi Windows.Verify that the Azure Site Recovery VSS Provider is installed as a service in Windows Services. Aprire MMC Servizio componenti per verificare che il provider VSS sia elencato.
1. Se l'installazione del provider VSS continua a non riuscire, utilizzare il supporto tecnico per risolvere gli errori di autorizzazioni in Cryptographic Application Programming Interface (CAPI2).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>L'installazione del provider VSS non riesce perché il servizio cluster viene abilitato in computer non cluster

Questo problema causa l'installazione dell'agente di protezione mobili di Azure Site Recovery non riuscire durante l'installazione del provider VSS di Azure Site Recovery. L'errore è dovuto al `COM+` fatto che si è verificato un problema con che impedisce l'installazione del provider VSS.

### <a name="to-identify-the-issue"></a>Per identificare il problema

Nel registro che si trova nel server di configurazione in C: , _ProgramData , ASRSetupLogs , UploadedLogs\<data-time>UA_InstallLogFile.log_ si trova l'eccezione seguente:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Per risolvere il problema:

1. Verificare che la macchina sia una macchina non cluster e che i componenti del cluster non siano in uso.
1. Se i componenti non vengono utilizzati, rimuovere i componenti del cluster dalla macchina.

## <a name="drivers-are-missing-on-the-source-server"></a>Driver mancanti nel server di origine

Se l'installazione dell'agente per dispositivi mobili non riesce, esaminare i registri in _C:_

Per risolvere il problema:

1. Utilizzando un editor `regedit.msc`del Registro di sistema come , aprire il Registro di sistema.
1. Aprire `HKEY_LOCAL_MACHINE\SYSTEM` il nodo.
1. Nel `SYSTEM` nodo individuare i set di controlli.
1. Aprire ogni set di controlli e verificare che siano presenti i seguenti driver di Windows:

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. Reinstallare i driver mancanti.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](vmware-azure-tutorial.md) su come configurare il ripristino di emergenza per le macchine virtuali VMware.Learn more about how to set up disaster recovery for VMware VMs.
