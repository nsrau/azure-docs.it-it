---
title: Risolvere gli errori di installazione push del servizio Mobility durante l'abilitazione della replica per il ripristino di emergenza | Microsoft Docs
description: Risolvere gli errori di installazione del servizio Mobility durante l'abilitazione della replica per il ripristino di emergenza
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 10/29/2018
ms.openlocfilehash: 1a8396f91f1e6f863d99be17dc8d00133a1bdd3a
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162552"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Risolvere i problemi di installazione push del servizio Mobility

L'installazione del servizio Mobility è un passaggio chiave dell'abilitazione della replica. La riuscita di questo passaggio dipende esclusivamente dal rispetto dei prerequisiti e dall'utilizzo di configurazioni supportate. Gli errori più comuni che si verificano durante l'installazione del servizio Mobility sono dovuti a

* Errori relativi a credenziali o privilegi
* Errori di connettività
* Sistemi operativi non supportati
* Errori di installazione di VSS

Quando si abilita la replica, Azure Site Recovery tenta di eseguire l'installazione push dell'agente del servizio Mobility nella macchina virtuale. Nell'ambito di questa operazione, il server di configurazione tenta di connettersi alla macchina virtuale e di copiare l'agente. Per consentire la riuscita dell'installazione, seguire le indicazioni dettagliate per la risoluzione dei problemi descritte di seguito.

## <a name="credentials-check-errorid-95107--95108"></a>Controllo delle credenziali (ID errore: 95107 e 95108)

* Verificare se l'account utente scelto durante l'abilitazione della replica è **valido e corretto**.
* Per eseguire l'installazione push, Azure Site Recovery richiede **privilegi di amministratore**.
  * Per Windows, verificare se l'account utente ha accesso amministrativo, locale o di dominio, nel computer di origine.
  * Se non si usa un account di dominio, è necessario disabilitare il controllo di accesso utente remoto nel computer locale.
    * Per disabilitare il controllo di accesso utente remoto, nella chiave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System del Registro di sistema aggiungere un nuovo valore DWORD: LocalAccountTokenFilterPolicy. Impostare il valore su 1. Per eseguire questo comando, al prompt dei comandi eseguire il comando seguente:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Per la riuscita dell'installazione dell'agente di mobilità in Linux, è necessario scegliere l'account radice.

Se si vogliono modificare le credenziali dell'account utente scelto, seguire le istruzioni illustrate [qui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="connectivity-check-errorid-95117--97118"></a>**Controllo della connettività (ID errore: 95117 e 97118)**

* Assicurarsi che sia possibile effettuare il ping del computer di origine dal server di configurazione. Se durante l'abilitazione della replica si è scelto un server di elaborazione con scalabilità orizzontale, assicurarsi che sia possibile effettuare il ping del computer di origine dal server di elaborazione.
  * Dalla riga di comando del computer del server di origine, usare Telnet per effettuare il ping del server di configurazione o del server di elaborazione scale-out attraverso la porta HTTPS (135) come illustrato di seguito, per stabilire se sono presenti problemi di connettività di rete o problemi che causano il blocco delle porte del firewall.

     `telnet <CS/ scale-out PS IP address> <135>`
  * Controllare lo stato di **InMage Scout VX Agent - Sentinel/Outpost** del servizio. Se non è in esecuzione, avviare il servizio.
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
* Può trattarsi di un problema correlato alla connettività, alla rete o al dominio. L'errore può anche essere dovuto a un problema di risoluzione dei nomi DNS o di esaurimento delle porte TCP. Controllare se sono presenti problemi noti di questo tipo all'interno del dominio.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Controllo del servizio di condivisione di file e stampanti (ID errore: 95105 e 95106)

Dopo il controllo della connettività, verificare se il servizio di condivisione di file e stampanti è abilitato nella macchina virtuale.

Per **Windows 2008 R2 e versioni precedenti**,

* Per abilitare la condivisione di file e stampanti tramite Windows Firewall,
  * Aprire Pannello di controllo -> Sistema e sicurezza -> Windows Firewall -> nel riquadro sinistro fare clic su Impostazioni avanzate -> nell'albero della console fare clic su Regole in entrata.
  * Individuare le regole Condivisione file e stampanti (NB-Session-In) e Condivisione file e stampanti (SMB-In). Su ogni regola fare clic con il pulsante destro del mouse e quindi fare clic su **Abilita regola**.
* Per abilitare la condivisione di file con Criteri di gruppo,
  * Passare a Start ed eseguire una ricerca digitando gpmc.msc.
  * Nel riquadro di spostamento aprire le cartelle seguenti: Criteri del computer locale, Configurazione utente, Modelli amministrativi, Componenti di Windows e Condivisione di rete.
  * Nel riquadro dei dettagli fare doppio clic su **Impedisci di condividere file nel profilo utente**. Per disabilitare questa impostazione di Criteri di gruppo e consentire la condivisione di file, fare clic su Disabilitato. Fare clic su OK per salvare le modifiche. Per altre informazioni, fare clic [qui](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Per abilitare la condivisione di file e stampanti nelle **versioni successive**, seguire le istruzioni descritte [qui](vmware-azure-install-mobility-service.md).

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Controllo della configurazione di Strumentazione gestione Windows (WMI)

Dopo il controllo dei servizi file e stampanti, abilitare il servizio WMI attraverso il firewall.

* Nel Pannello di controllo fare clic su Sicurezza e quindi su Windows Firewall.
* Fare clic su Cambia impostazioni e quindi sulla scheda Eccezioni.
* Nella finestra Eccezioni selezionare la casella di controllo Strumentazione gestione Windows (WMI) per consentire il traffico WMI attraverso il firewall. 

È anche possibile abilitare il traffico WMI attraverso il firewall dal prompt dei comandi. Usare il comando seguente: `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Di seguito sono elencati altri articoli sulla risoluzione dei problemi di WMI.

* [Test WMI di base](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Risoluzione dei problemi WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Risoluzione dei problemi con gli script WMI e i servizi WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Sistemi operativi non supportati

Un altro motivo di errore molto comune è l'uso di un sistema operativo non supportato. Per la riuscita dell'installazione del servizio Mobility, assicurarsi di usare la versione supportata del kernel e del sistema operativo.

Per informazioni sui sistemi operativi supportati da Azure Site Recovery, fare riferimento al [documento matrice di supporto](vmware-physical-azure-support-matrix.md#replicated-machines).

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