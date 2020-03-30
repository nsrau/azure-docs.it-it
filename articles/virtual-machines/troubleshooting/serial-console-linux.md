---
title: Console seriale di Azure per Linux Documenti Microsoft
description: Console di serie bidirezionale per macchine virtuali di Azure e set di scalabilità di macchine virtuali.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167007"
---
# <a name="azure-serial-console-for-linux"></a>Console seriale di Azure per Linux

La console seriale nel portale di Azure fornisce l'accesso a una console basata su testo per le macchine virtuali Linux (VM) e le istanze del set di scalabilità delle macchine virtuali. Questa connessione seriale si connette alla porta seriale ttys0 dell'istanza del set di scalabilità della macchina virtuale o della macchina virtuale, fornendovi l'accesso indipendentemente dallo stato della rete o del sistema operativo. È possibile accedere alla console seriale solo tramite il portale di Azure ed è consentita solo per gli utenti che dispongono di un ruolo di accesso Collaboratore o versione successiva al set di scalabilità della macchina virtuale o della macchina virtuale.

La console seriale funziona allo stesso modo per le macchine virtuali e le istanze del set di scalabilità delle macchine virtuali. In questo documento, tutte le menzioni alle macchine virtuali includeranno implicitamente le istanze del set di scalabilità delle macchine virtuali, se non diversamente specificato.

Per la documentazione della console seriale per Windows, vedere [Console seriale per Windows](../windows/serial-console.md).

> [!NOTE]
> La console seriale è in genere disponibile nelle aree globali di Azure e nell'anteprima pubblica in Azure per enti pubblici. Non è ancora disponibile nel cloud di Azure China.It is not yet available in the Azure China cloud.


## <a name="prerequisites"></a>Prerequisiti

- La macchina virtuale o l'istanza del set di scalabilità della macchina virtuale deve usare il modello di distribuzione della gestione delle risorse. Le distribuzioni classiche non sono supportate.

- L'account che usa la console seriale deve disporre del [ruolo Collaboratore macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) per la macchina virtuale e dell'account di archiviazione di diagnostica di [avvio](boot-diagnostics.md)

- La macchina virtuale o l'istanza del set di scalabilità della macchina virtuale deve avere un utente basato su password. È possibile crearne uno con la funzione di [reimpostazione della password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**.

- La macchina virtuale o l'istanza del set di scalabilità della macchina virtuale deve avere la diagnostica di [avvio](boot-diagnostics.md) abilitata.

    ![Impostazioni di diagnostica di avvio](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Per le impostazioni specifiche delle distribuzioni Linux, vedere [Disponibilità delle distribuzioni della console seriale per Linux](#serial-console-linux-distribution-availability).

- La macchina virtuale o l'istanza del set `ttys0`di scalabilità della macchina virtuale deve essere configurata per l'output seriale in . Questa è l'impostazione predefinita per le immagini di Azure, ma è consigliabile verificarla nelle immagini personalizzate. Dettagli [qui sotto](#custom-linux-images).


> [!NOTE]
> La console seriale richiede un utente locale con una password configurata. Le macchine virtuali o i set di scalabilità delle macchine virtuali configurati solo con una chiave pubblica SSH non saranno in grado di accedere alla console seriale. Per creare un utente locale con una password, usare l'[estensione VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) disponibile nel portale selezionando **Reimposta password** nel portale di Azure e creare un utente locale con una password.
> È possibile anche reimpostare la password di amministratore nell'account [usando GRUB per eseguire l'avvio in modalità utente singolo](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilità della distribuzione Linux della console seriale
Affinché la console seriale funzioni correttamente, il sistema operativo guest deve essere configurato per leggere e scrivere i messaggi della console nella porta seriale. La maggior parte delle [distribuzioni Linux di Azure approvate](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ha la console seriale configurata per impostazione predefinita. Se si seleziona **Console seriale** nella sezione **Supporto e risoluzione dei problemi** del portale di Azure si consente l'accesso alla console seriale.

> [!NOTE]
> Se non viene visualizzato nulla nella console seriale, verificare che la diagnostica di avvio sia abilitata nella macchina virtuale. Premendo **Invio** spesso si risolvono i problemi in cui nella console seriale non viene visualizzato alcun problema.

Distribuzione      | Accesso alla console seriale
:-----------|:---------------------
Red Hat Enterprise Linux    | L'accesso alla console seriale è abilitato per impostazione predefinita.
CentOS      | L'accesso alla console seriale è abilitato per impostazione predefinita.
Debian      | L'accesso alla console seriale è abilitato per impostazione predefinita.
Ubuntu      | L'accesso alla console seriale è abilitato per impostazione predefinita.
CoreOS      | L'accesso alla console seriale è abilitato per impostazione predefinita.
SUSE        | Le immagini SLES più recenti disponibili in Azure hanno l'accesso alla console seriale abilitato per impostazione predefinita. Se si usano versioni precedenti (fino alla 10) di SLES in Azure, vedere l'[articolo della Knowledge Base](https://www.novell.com/support/kb/doc.php?id=3456486) per abilitare la console seriale.
Oracle Linux        | L'accesso alla console seriale è abilitato per impostazione predefinita.

### <a name="custom-linux-images"></a>Immagini personalizzate di Linux
Per abilitare la console seriale per l'immagine personalizzata della VM Linux, abilitare l'accesso alla console nel file */etc/inittab* per l'esecuzione di un terminale in `ttyS0`. Ad esempio `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Potrebbe anche essere necessario generare un getty su ttyS0. Questo può essere `systemctl start serial-getty@ttyS0.service`fatto con .

È inoltre necessario aggiungere ttys0 come destinazione per l'output seriale. Per altre informazioni sulla configurazione di un'immagine personalizzata da usare con la console seriale, vedere i requisiti di sistema generali in Creare e caricare un disco rigido virtuale Linux in Azure.For more information on configuring a custom image to work with the serial console, see the general system requirements at [Create and upload a Linux VHD in Azure.](https://aka.ms/createuploadvhd#general-linux-system-requirements)

Se si sta creando un kernel personalizzato, provare ad abilitare questi flag kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Il file di configurazione si trova in genere nel percorso */boot/*.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Scenari comuni per l'accesso alla console serialeCommon scenarios for accessing the Serial Console

Scenario          | Azioni nella console seriale
:------------------|:-----------------------------------------
File *FSTAB* interrotto | Premere il tasto **INVIO** per continuare e usare un editor di testo per correggere il file *FSTAB*. Per farlo potrebbe essere necessario essere in modalità utente singolo. Per ulteriori informazioni, vedere la sezione relativa alla console seriale di Come risolvere i problemi di [fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [Utilizzare la console seriale per accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).
Regole del firewall non corrette |  Se le tabelle IPtable sono state configurate per bloccare la connettività SSH, è possibile usare la console seriale per interagire con la macchina virtuale senza la necessità di SSH. Maggiori dettagli possono essere trovati alla [pagina iptables man](https://linux.die.net/man/8/iptables).<br>Analogamente, se il firewall bloccato blocca l'accesso SSH, è possibile accedere alla macchina virtuale tramite console seriale e riconfigurare il firewall. Ulteriori dettagli sono disponibili nella [documentazione con firewall.](https://firewalld.org/documentation/)
Danneggiamento/Controllo del file system | Vedere la sezione della console seriale della [macchina virtuale Azure Linux non può essere avviata](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) a causa di errori del file system per ulteriori dettagli sulla risoluzione dei problemi relativi ai file system danneggiati tramite console seriale.
Problemi di configurazione SSH | Accedere alla console seriale e modificare le impostazioni. La console seriale può essere utilizzata indipendentemente dalla configurazione SSH di una macchina virtuale in quanto non richiede che la macchina virtuale disponga della connettività di rete per funzionare. Una guida alla risoluzione dei problemi è disponibile in Risolvere le connessioni SSH a una [macchina virtuale Di Azure Linux che ha esito negativo, errori in uscita o viene rifiutata.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection) Ulteriori dettagli sono disponibili in procedura di risoluzione dettagliata di SSH per i problemi di connessione a una macchina virtuale Linux in AzureMore details are available at [Detailed SSH troubleshooting steps for issues connecting to a Linux VM in Azure](./detailed-troubleshoot-ssh-connection.md)
Interazione con bootloader | Riavviare la macchina virtuale all'interno del pannello della console seriale per accedere a GRUB nella VM Linux. Per ulteriori dettagli e informazioni specifiche della distribuzione, consultate Utilizzare la [console seriale per accedere a GRUB e alla modalità utente singolo.](serial-console-grub-single-user-mode.md)

## <a name="disable-the-serial-console"></a>Disabilitare la console seriale

Per impostazione predefinita, per tutte le sottoscrizioni è abilitato l'accesso alla console seriale. È possibile disabilitare la console seriale a livello di sottoscrizione o a livello di set di scalabilità di macchine virtuali/VM/virtual machine. Per istruzioni dettagliate, vedere [Abilitare e disabilitare la console seriale](./serial-console-enable-disable.md)di Azure .

## <a name="serial-console-security"></a>Sicurezza della console seriale

### <a name="access-security"></a>Sicurezza dell'accesso
L'accesso alla console seriale è consentito solo agli utenti che hanno un ruolo di accesso di [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) o un ruolo superiore nella macchina virtuale. Se il tenant di Azure Active Directory richiede l'autenticazione a più fattori (MFA), anche l'accesso alla console seriale richiederà MFA perché l'accesso della console seriale avviene tramite il [portale di Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sicurezza del canale
Tutti i dati inviati in entrambe le direzioni vengono crittografati durante il transito.

### <a name="audit-logs"></a>Log di controllo
Ogni accesso alla console seriale viene attualmente registrato nei log di [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) della macchina virtuale. L'accesso a questi log è di proprietà dell'amministratore della macchina virtuale di Azure, che ne ha anche il controllo.

> [!CAUTION]
> Non viene registrata alcuna password di accesso per la console. Tuttavia, se i comandi eseguiti nella console contengono o restituiscono password, segreti, nomi utente o qualsiasi altra forma di informazioni personali, tali elementi verranno scritti nei log di diagnostica di avvio della macchina virtuale, insieme a qualsiasi altro testo visibile, durante l'implementazione della funzione di scorrimento all'indietro della console seriale. Questi log sono circolari e solo gli utenti con autorizzazioni di lettura per l'account di archiviazione di diagnostica possono accedervi. Se si stanno immettendo dati o comandi che contengono segreti o informazioni personali, è consigliabile utilizzare SSH a meno che la console seriale non sia assolutamente necessaria.

### <a name="concurrent-usage"></a>Utilizzo simultaneo
Se un utente è connesso alla console seriale e un altro utente richiede correttamente l'accesso alla stessa macchina virtuale, il primo utente verrà disconnesso, mentre il secondo verrà connesso alla stessa sessione.

> [!CAUTION]
> Ciò significa che un utente disconnesso non verrà disconnesso. La possibilità di imporre una disconnessione alla disconnessione (utilizzando SIGHUP o meccanismo simile) è ancora sulla roadmap. Nella console amministrativa speciale (SAC, Special Administrative Console) è abilitato un timeout automatico per Windows, mentre per Linux è possibile configurare l'impostazione di timeout del terminale. A tale scopo, aggiungere `export TMOUT=600` nel file con estensione *bash_profile* o *profile* per l'utente con cui si esegue l'accesso alla console. Questa impostazione prevede il timeout della sessione dopo 10 minuti.

## <a name="accessibility"></a>Accessibilità
L'accessibilità è un elemento chiave per la console seriale di Azure.Accessibility is a key focus for the Azure Serial Console. A tale scopo, è stato garantito che la console seriale sia completamente accessibile.

### <a name="keyboard-navigation"></a>Navigazione da tastiera
Usare il tasto **TAB** della tastiera per eseguire la navigazione nell'interfaccia della console seriale nel portale di Azure. La posizione verrà evidenziata sullo schermo. Per lasciare lo stato attivo della finestra della console seriale, premere **CTRL**+**F6** sulla tastiera.

### <a name="use-serial-console-with-a-screen-reader"></a>Usare la console seriale con un'utilità per la lettura dello schermo
La console seriale viene fornita con il supporto dell'utilità per la lettura dello schermo incorporato. La navigazione con un'utilità per la lettura dello schermo attivata consentirà la lettura a voce alta del testo alternativo del pulsante attualmente selezionato da parte dell'utilità per la lettura dello schermo.

## <a name="known-issues"></a>Problemi noti
Siamo a conoscenza di alcuni problemi con la console seriale e il sistema operativo della macchina virtuale. Ecco un elenco di questi problemi e passaggi per l'attenuazione per le macchine virtuali Linux.Here's a list of these issues and steps for mitigation for Linux VMs. Questi problemi e attenuazioni si applicano sia alle macchine virtuali che alle istanze del set di scalabilità delle macchine virtuali. Se non corrispondono all'errore riscontrato, vedere gli errori comuni del servizio console seriale in [Errori della console seriale comune](./serial-console-errors.md).

Problema                           |   Strategia di riduzione del rischio
:---------------------------------|:--------------------------------------------|
Se si preme il tasto **INVIO** dopo il banner della connessione, non viene visualizzato un prompt di accesso. | GRUB potrebbe non essere configurato correttamente. Eseguire i seguenti `grub2-mkconfig -o /etc/grub2-efi.cfg` comandi: `grub2-mkconfig -o /etc/grub2.cfg`e/o . Per altre informazioni, vedere [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Questo problema può verificarsi se si esegue una macchina virtuale personalizzata, un'appliance con protezione avanzata o una configurazione GRUB che causa la mancata connessione di Linux alla porta seriale.
Il testo della console seriale occupa solo una parte delle dimensioni dello schermo (spesso dopo l'uso di un editor di testo). | Le console seriali non supportano la negoziazione sulle dimensioni della finestra ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)): questo significa che non sarà presente alcun segnale SIGWINCH inviato per aggiornare le dimensioni dello schermo e la macchina virtuale non avrà alcuna conoscenza delle dimensioni del terminale. Installare xterm o un'utilità analoga per fornire il comando `resize` e quindi eseguire `resize`.
L'operazione di incollare le stringhe lunghe non funziona. | La console seriale limita la lunghezza delle stringhe incollate nel terminale a 2048 caratteri per impedire il sovraccarico della larghezza di banda della porta seriale.
Input da tastiera irregolare nelle immagini SLES BYOS. L'input da tastiera viene riconosciuto solo sporadicamente. | Questo è un problema con il pacchetto Plymouth. Plymouth non deve essere eseguito in Azure perché non è necessaria una schermata iniziale e Plymouth interferisce con la capacità della piattaforma di usare la console seriale. Rimuovere Plymouth `sudo zypper remove plymouth` con e quindi riavviare. In alternativa, modificare la riga del kernel `plymouth.enable=0` della configurazione GRUB aggiungendola alla fine della riga. A tale scopo, è possibile modificare la voce di [avvio al momento dell'avvio](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)o modificare la riga di GRUB_CMDLINE_LINUX in `/etc/default/grub`, ricompilando GRUB con `grub2-mkconfig -o /boot/grub2/grub.cfg`, quindi riavviando.


## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Come posso inviare un feedback?**

R. Fornire commenti e suggerimenti segnalando un problema GitHub in https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), è possibile inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di https://feedback.azure.com.

**D: La console seriale supporta la copia/incolla?**

R. Sì. Utilizzare **Ctrl**+**Maiusc**+**C** e **Ctrl**+**Maiusc**+**V** per copiare e incollare nel terminale.

**D: È possibile utilizzare la console seriale anziché una connessione SSH?**

R. Anche se ciò potrebbe essere tecnicamente possibile, la console seriale è destinata a essere usata principalmente come strumento di risoluzione dei problemi nelle situazioni in cui la connettività tramite SSH non è possibile. Non è consigliabile usare la console seriale in sostituzione di SSH per i motivi seguenti:

- La console seriale non ha la stessa larghezza di banda di SSH. Poiché si tratta di una connessione di solo testo, più interazioni intense con l'interfaccia utente grafica sono difficili.
- L'accesso alla console seriale avviene attualmente solo tramite nome utente e password. Poiché le chiavi SSH sono molto più sicure rispetto alle combinazioni di nome utente/password, dal punto di vista della sicurezza di accesso è consigliabile usare SSH con la console seriale.

**D: Chi può abilitare o disabilitare la console seriale per l'abbonamento?**

R. Per abilitare o disabilitare la console seriale a livello di sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. I ruoli con autorizzazione di scrittura sono quelli di amministratore o proprietario. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

**D: Chi può accedere alla console seriale per il set di scalabilità VM/macchina virtuale?**

R. Per accedere alla console seriale, è necessario disporre del ruolo Collaboratore macchina virtuale o versione successiva per una macchina virtuale o un set di macchine virtuali.

**D: La console seriale non visualizza nulla, cosa devo fare?**

R. L'immagine probabilmente non è configurata correttamente per l'accesso alla console seriale. Per informazioni sulla configurazione dell'immagine per abilitare la console seriale, vedere [Disponibilità delle distribuzioni della console seriale per Linux](#serial-console-linux-distribution-availability).

**D: La console seriale è disponibile per i set di scalabilità delle macchine virtuali?**

R. Sì lo è! Vedere Console seriale per set di [scalabilità di macchine virtualiSee Serial Console for Virtual Machine Scale Sets](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**D: Se si configura il set di scalabilità della macchina virtuale o della macchina virtuale usando solo l'autenticazione con chiave SSH, è comunque possibile usare la console seriale per connettersi all'istanza del set di scalabilità della macchina virtuale o della macchina virtuale?**

R. Sì. Poiché la console seriale non richiede le chiavi SSH, è sufficiente configurare una combinazione di nome utente/password. È possibile farlo selezionando **Reimposta password** nel portale di Azure e usare tali credenziali per accedere alla console seriale.

## <a name="next-steps"></a>Passaggi successivi
* Usare la console seriale per [accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).
* Usare la console seriale per le [chiamate NMI e SysRq](serial-console-nmi-sysrq.md).
* Scopri come utilizzare la console seriale per [abilitare GRUB in varie distro](serial-console-grub-proactive-configuration.md)
* La console seriale è disponibile anche per [le macchine virtuali Windows.](../windows/serial-console.md)
* Ulteriori informazioni sulla diagnostica di [avvio](boot-diagnostics.md).

