---
title: Risolvere gli errori di installazione push del servizio Mobility durante l'abilitazione della replica per il ripristino di emergenza | Microsoft Docs
description: Risolvere gli errori di installazione del servizio Mobility durante l'abilitazione della replica per il ripristino di emergenza
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 12/12/2018
ms.openlocfilehash: fef0cfd05fe0d44966cbb9f15ba1148f8473207d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789908"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Risolvere i problemi di installazione push del servizio Mobility

L'installazione del servizio Mobility è un passaggio chiave dell'abilitazione della replica. La riuscita di questo passaggio dipende esclusivamente dal rispetto dei prerequisiti e dall'utilizzo di configurazioni supportate. Gli errori più comuni che si verificano durante l'installazione del servizio Mobility sono dovuti a:

* Errori relativi a credenziali o privilegi
* Errori di accesso
* Errori di connettività
* Sistemi operativi non supportati
* Errori di installazione di VSS

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

Quando l'impostazione della relazione di trust tra il dominio primario e la workstation non riesce durante un tentativo di accesso al computer di origine, l'installazione dell'agente di mobilità genera un errore con ID 95518. Assicurarsi quindi che l'account utente usato per installare l'agente di mobilità disponga dei privilegi di amministratore per l'accesso al computer di origine tramite il dominio primario.

Se si vogliono modificare le credenziali dell'account utente scelto, seguire le istruzioni illustrate [qui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failure-errorid-95519"></a>Errore di accesso (ID errore: 95519)

L'account utente scelto durante l'abilitazione della replica è stato disabilitato. Per abilitare l'account utente, fare riferimento all'articolo [qui](https://aka.ms/enable_login_user) oppure eseguire il comando seguente sostituendo *username* con il nome utente effettivo.
`net user 'username' /active:yes`

## <a name="login-failure-errorid-95520"></a>Errore di accesso (ID errore: 95520)

Se si eseguono più tentativi di accesso a un computer senza esito positivo, il sistema blocca l'account utente. La cause dell'errore possono essere le seguenti:

* le credenziali specificate durante l'installazione della configurazione non sono corrette O
* l'account utente scelto durante l'abilitazione della replica è errato.

Modificare quindi le credenziali scelte seguendo le istruzioni indicate [qui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e provare a eseguire l'operazione dopo avere atteso qualche minuto.

## <a name="login-failure-errorid-95521"></a>Errore di accesso (ID errore: 95521)

Questo errore si verifica quando i server di accesso non sono disponibili nel computer di origine. La mancata disponibilità dei server di accesso genera errori di richiesta di accesso e pertanto non è possibile installare l'agente di mobilità. Per eseguire l'accesso, assicurarsi che i server di accesso siano disponibili nel computer di origine e avviare il servizio di accesso. Per istruzioni dettagliate, fare clic [qui](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available).

## <a name="login-failure-errorid-95522"></a>Errore di accesso (ID errore: 95522)

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
  * Nel riquadro dei dettagli fare doppio clic su **Impedisci di condividere file nel profilo utente**. Per disabilitare questa impostazione di Criteri di gruppo e consentire la condivisione di file, fare clic su Disabilitato. Fare clic su OK per salvare le modifiche. Per altre informazioni, fare clic [qui](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Per abilitare la condivisione di file e stampanti nelle **versioni successive**, seguire le istruzioni descritte [qui](vmware-azure-install-mobility-service.md).

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

## <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Le partizioni e i volumi di avvio e di sistema non sono lo stesso disco (ID errore: 95309)

Nelle versioni precedenti alla 9.20, le partizioni e i volumi di avvio e di sistema su dischi diversi sono una configurazione non supportata. A partire dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) questa configurazione è supportata. Per usufruire di questo supporto, usare la versione più recente.

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partizione di sistema su più dischi (ID errore: 95313)

Nelle versioni precedenti alla 9.20, la partizione o il volume radice su più dischi è una configurazione non supportata. A partire dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) questa configurazione è supportata. Per usufruire di questo supporto, usare la versione più recente.

## <a name="grub-uuid-failure-errorid-95320"></a>Errore UUID GRUB (ID errore: 95320)

Se il GRUB del computer di origine usa un nome dispositivo anziché un UUID, l'installazione dell'agente di mobilità non viene completata. Rivolgersi all'amministratore del sistema per apportare le modifiche al file di GRUB.

## <a name="lvm-support-from-920-version"></a>Supporto LVM dalla versione 9.20

Nelle versioni precedenti alla 9.20, LVM è supportato per solo i dischi dati. /boot deve trovarsi in una partizione del disco e non essere un volume LVM.

A partire dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), il [disco del sistema operativo su LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) è supportato. Per usufruire di questo supporto, usare la versione più recente.

## <a name="insufficient-space-errorid-95524"></a>Spazio insufficiente (ID errore: 95524)

Per copiare l'agente di mobilità nel computer di origine, sono necessari almeno 100 MB di spazio disponibile su disco. Verificare pertanto che nel computer di origine sia presente lo spazio disponibile richiesto e ripetere l'operazione.

## <a name="vss-installation-failures"></a>Errori di installazione di VSS

Installazione di VSS è una parte dell'installazione dell'agente di mobilità. Questo servizio viene usato nel processo di generazione di punti di recupero coerenti dell'applicazione. I motivi degli errori di installazione di VSS possono essere diversi. Per identificare esattamente gli errori, vedere **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Nella sezione seguente sono evidenziati alcuni errori comuni e le procedure di risoluzione corrispondenti.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Errore VSS -2147023170 [0x800706BE] - codice di uscita 511

Questo problema si verifica principalmente quando un software antivirus blocca le operazioni di servizi di Azure Site Recovery. Per risolvere il problema:

1. Escludere tutte le cartelle indicate [qui](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Seguire le linee guida pubblicate dal provider del software antivirus per sbloccare la registrazione della DLL in Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Errore VSS 7 [0x7] - codice di uscita 511

Si tratta di un errore di runtime causato dalla mancanza di memoria sufficiente per l'installazione di VSS. Per consentire il completamento dell'operazione, assicurarsi di aumentare lo spazio disponibile su disco.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Errore VSS -2147023824 [0x80070430] - codice di uscita 517

Questo errore si verifica quando il servizio Provider VSS di Azure Site Recovery è [contrassegnato per l'eliminazione](https://msdn.microsoft.com/en-us/library/ms838153.aspx). Provare a installare manualmente VSS nel computer di origine tramite la riga di comando seguente

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Errore VSS -2147023841 [0x8007041F] - codice di uscita 512

Questo errore si verifica quando il database del servizio Provider VSS di Azure Site Recovery è [bloccato](https://msdn.microsoft.com/en-us/library/ms833798.aspx). Provare a installare manualmente VSS nel computer di origine tramite la riga di comando seguente

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>Errore VSS codice di uscita 806

Questo errore si verifica quando l'account utente usato per l'installazione non dispone delle autorizzazioni necessarie per eseguire il comando CSScript. Concedere all'account utente le autorizzazioni necessarie per eseguire lo script e ripetere l'operazione.

### <a name="other-vss-errors"></a>Altri errori VSS

Provare a installare manualmente il servizio Provider VSS nel computer di origine tramite la riga di comando seguente

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](vmware-azure-tutorial.md) configurare il ripristino di emergenza per le macchine virtuali VMware.
