---
title: Console seriale di Azure per Linux | Microsoft Docs
description: Console seriale bidirezionale per macchine virtuali di Azure e set di scalabilità di macchine virtuali.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 4e0c91096d5efdcc9639a7127126d8e4b89ef068
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090154"
---
# <a name="azure-serial-console-for-linux"></a>Console seriale di Azure per Linux

La console seriale nel portale di Azure fornisce l'accesso a una console basata su testo per le macchine virtuali Linux e le istanze del set di scalabilità di macchine virtuali. Questa connessione seriale si connette alla porta seriale ttyS0 della VM o dell'istanza del set di scalabilità di macchine virtuali, garantendo l'accesso indipendentemente dalla rete o dallo stato del sistema operativo. È possibile accedere alla console seriale solo usando la portale di Azure ed è consentita solo per gli utenti che hanno un ruolo accesso di collaboratore o superiore alla macchina virtuale o al set di scalabilità di macchine virtuali.

La console seriale funziona allo stesso modo per le macchine virtuali e le istanze del set di scalabilità di macchine virtuali. In questo documento, tutte le menzioni delle macchine virtuali includeranno in modo implicito le istanze del set di scalabilità di macchine virtuali, se non diversamente specificato.

Per la documentazione sulla console seriale per Windows, vedere [console seriale per Windows](../windows/serial-console.md).

> [!NOTE]
> La console seriale è disponibile a livello generale nelle aree globali di Azure. Non è al momento disponibile nei cloud di Azure per enti pubblici o di Azure Cina.


## <a name="prerequisites"></a>Prerequisiti

- L'istanza della VM o del set di scalabilità di macchine virtuali deve usare il modello di distribuzione di gestione delle risorse. Le distribuzioni classiche non sono supportate.

- L'account che usa la console seriale deve avere il [ruolo Collaboratore macchina virtuale per la macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e l'account di archiviazione di [diagnostica di avvio](boot-diagnostics.md)

- L'istanza della VM o del set di scalabilità di macchine virtuali deve avere un utente basato su password. È possibile crearne uno con la funzione di [reimpostazione della password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**.

- Per la VM o l'istanza del set di scalabilità di macchine virtuali deve essere abilitata la [diagnostica di avvio](boot-diagnostics.md)

    ![Impostazioni di diagnostica di avvio](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Per le impostazioni specifiche delle distribuzioni Linux, vedere [Disponibilità delle distribuzioni della console seriale per Linux](#serial-console-linux-distribution-availability).

- La VM o l'istanza del set di scalabilità di macchine virtuali deve essere `ttys0`configurata per l'output seriale in. Si tratta dell'impostazione predefinita per le immagini di Azure, ma è consigliabile eseguire una doppia verifica sulle immagini personalizzate. Dettagli di [seguito](#custom-linux-images).


## <a name="get-started-with-the-serial-console"></a>Introduzione alla console seriale
La console seriale per le macchine virtuali e il set di scalabilità di macchine virtuali è accessibile solo tramite la portale di Azure:

### <a name="serial-console-for-virtual-machines"></a>Console seriale per le macchine virtuali
La console seriale per le macchine virtuali è semplice come fare clic su **console seriale** nella sezione **supporto e risoluzione dei problemi** nel portale di Azure.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **tutte le risorse** e selezionare una macchina virtuale. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.

  1. Scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi** e selezionare **Console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Finestra console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console seriale per i set di scalabilità di macchine virtuali
La console seriale è disponibile in base alle singole istanze per i set di scalabilità di macchine virtuali. Prima di visualizzare il pulsante **console seriale** , sarà necessario passare alla singola istanza di un set di scalabilità di macchine virtuali. Se per il set di scalabilità di macchine virtuali non è abilitata la diagnostica di avvio, assicurarsi di aggiornare il modello del set di scalabilità di macchine virtuali per abilitare la diagnostica di avvio e quindi aggiornare tutte le istanze al nuovo modello per accedere alla console seriale.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **tutte le risorse** e selezionare un set di scalabilità di macchine virtuali. Verrà visualizzata la pagina Panoramica per il set di scalabilità di macchine virtuali.

  1. Passa a **istanze**

  1. Selezionare un'istanza del set di scalabilità di macchine virtuali

  1. Nella sezione **supporto e risoluzione dei problemi** selezionare **console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Console seriale del set di scalabilità di macchine virtuali Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> La console seriale richiede un utente locale con una password configurata. Le VM o i set di scalabilità di macchine virtuali configurati solo con una chiave pubblica SSH non saranno in grado di accedere alla console seriale. Per creare un utente locale con una password, usare l'[estensione VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) disponibile nel portale selezionando **Reimposta password** nel portale di Azure e creare un utente locale con una password.
> È possibile anche reimpostare la password di amministratore nell'account [usando GRUB per eseguire l'avvio in modalità utente singolo](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilità distribuzione Linux console seriale
Affinché la console seriale funzioni correttamente, il sistema operativo guest deve essere configurato per leggere e scrivere i messaggi della console nella porta seriale. La maggior parte delle [distribuzioni di Azure per Linux approvate](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ha la console seriale configurata per impostazione predefinita. Se si seleziona **Console seriale** nella sezione **Supporto e risoluzione dei problemi** del portale di Azure si consente l'accesso alla console seriale.

> [!NOTE]
> Se non viene visualizzato nulla nella console seriale, verificare che la diagnostica di avvio sia abilitata nella macchina virtuale. Se si preme **invio** , si correggeranno spesso i problemi che non vengono visualizzati nella console seriale.

Distribuzione      | Accesso alla console seriale
:-----------|:---------------------
Red Hat Enterprise Linux.    | L'accesso alla console seriale è abilitato per impostazione predefinita.
CentOS      | L'accesso alla console seriale è abilitato per impostazione predefinita.
Ubuntu      | L'accesso alla console seriale è abilitato per impostazione predefinita.
CoreOS      | L'accesso alla console seriale è abilitato per impostazione predefinita.
SUSE        | Le immagini SLES più recenti disponibili in Azure hanno l'accesso alla console seriale abilitato per impostazione predefinita. Se si usano versioni precedenti (fino alla 10) di SLES in Azure, vedere l'[articolo della Knowledge Base](https://www.novell.com/support/kb/doc.php?id=3456486) per abilitare la console seriale.
Oracle Linux        | L'accesso alla console seriale è abilitato per impostazione predefinita.

### <a name="custom-linux-images"></a>Immagini personalizzate di Linux
Per abilitare la console seriale per l'immagine personalizzata della VM Linux, abilitare l'accesso alla console nel file */etc/inittab* per l'esecuzione di un terminale in `ttyS0`. Ad esempio: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`.

Sarà anche possibile aggiungere ttyS0 come destinazione per l'output seriale. Per altre informazioni sulla configurazione di un'immagine personalizzata per l'uso con la console seriale, vedere i requisiti di sistema generali in [creare e caricare un disco rigido virtuale Linux in Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Se si sta creando un kernel personalizzato, provare ad abilitare questi flag kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Il file di configurazione si trova in genere nel percorso */boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Scenari comuni per l'accesso alla console seriale

Scenario          | Azioni nella console seriale
:------------------|:-----------------------------------------
File *FSTAB* danneggiato | Premere il tasto **INVIO** per continuare e usare un editor di testo per correggere il file *FSTAB*. Per farlo potrebbe essere necessario essere in modalità utente singolo. Per altre informazioni, vedere la sezione relativa alla console seriale di [come correggere i problemi di fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [usare la console seriale per accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).
Regole del firewall non corrette |  Se sono state configurate iptables per bloccare la connettività SSH, è possibile usare la console seriale per interagire con la macchina virtuale senza che sia necessario SSH. Per ulteriori informazioni, vedere la pagina relativa a [iptables Man](https://linux.die.net/man/8/iptables).<br>Analogamente, se il firewall blocca l'accesso SSH, è possibile accedere alla macchina virtuale tramite la console seriale e riconfigurare il firewall. Per ulteriori informazioni, vedere la [documentazione relativa ai firewall](https://firewalld.org/documentation/).
Danneggiamento/Controllo del file system | Vedere la sezione console seriale della [macchina virtuale Linux di Azure non può essere avviata a causa di errori di file System](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) per altri dettagli sulla risoluzione dei problemi relativi ai file System danneggiati con la console seriale.
Problemi di configurazione di SSH | Accedere alla console seriale e modificare le impostazioni. Console seriale può essere usato indipendentemente dalla configurazione SSH di una macchina virtuale perché non richiede che la macchina virtuale disponga della connettività di rete per funzionare. Una guida alla risoluzione dei problemi è disponibile in [risolvere i problemi relativi alle connessioni SSH a una macchina virtuale Linux di Azure che ha esito negativo, genera errori o viene rifiutata](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). [Per informazioni dettagliate sulla risoluzione dei problemi di connessione a una VM Linux in Azure](./detailed-troubleshoot-ssh-connection.md) , sono disponibili altri dettagli.
Interazione con bootloader | Riavviare la macchina virtuale all'interno del pannello della console seriale per accedere a GRUB nella VM Linux. Per altri dettagli e informazioni specifiche della distribuzione, vedere [usare la console seriale per accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Disabilitare la console seriale
Per impostazione predefinita, per tutte le sottoscrizioni è abilitato l'accesso alla console seriale. È possibile disabilitare la console seriale a livello di sottoscrizione o di VM/set di scalabilità di macchine virtuali. Si noti che la diagnostica di avvio deve essere abilitata in una macchina virtuale per consentire il funzionamento della console seriale.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>VM/set di scalabilità di macchine virtuali-disabilitazione a livello
La console seriale può essere disabilitata per una VM specifica o un set di scalabilità di macchine virtuali disabilitando l'impostazione di diagnostica di avvio. Disattivare la diagnostica di avvio dalla portale di Azure per disabilitare la console seriale per la VM o il set di scalabilità di macchine virtuali. Se si usa la console seriale in un set di scalabilità di macchine virtuali, assicurarsi di aggiornare le istanze del set di scalabilità di macchine virtuali al modello più recente.

> [!NOTE]
> Per abilitare o disabilitare la console seriale per una sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. Queste autorizzazioni comprendono i ruoli di amministratore o proprietario. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

### <a name="subscription-level-disable"></a>Disattivazione a livello di sottoscrizione
La console seriale può essere disabilitata per un'intera sottoscrizione tramite la [chiamata all'API REST di Disabilita console](/rest/api/serialconsole/console/disableconsole). Questa azione richiede l'accesso a livello di collaboratore o superiore alla sottoscrizione. È possibile usare la funzionalità **Try It** disponibile nella pagina della documentazione API per disabilitare e abilitare la console seriale per una sottoscrizione. Immettere l'ID sottoscrizione per **subscriptionId**, immettere **default** per **default** e quindi selezionare **Esegui**. I comandi dell'interfaccia della riga di comando di Azure non sono ancora disponibili.

Per riabilitare la console seriale per una sottoscrizione, usare la [chiamata all'API REST della console](/rest/api/serialconsole/console/enableconsole).

![API REST Try It](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

In alternativa, è possibile usare il set di comandi Bash seguente in Cloud Shell per disabilitare, abilitare e visualizzare lo stato disabilitato della console seriale per una sottoscrizione:

* Per ottenere lo stato disabilitato della console seriale per una sottoscrizione:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Per disabilitare la console seriale per una sottoscrizione:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Per abilitare la console seriale per una sottoscrizione:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>Sicurezza della console seriale

### <a name="access-security"></a>Sicurezza dell'accesso
L'accesso alla console seriale è consentito solo agli utenti che hanno un ruolo di accesso di [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) o un ruolo superiore nella macchina virtuale. Se il tenant di Azure Active Directory richiede l'autenticazione a più fattori (MFA), anche l'accesso alla console seriale richiederà MFA perché l'accesso della console seriale avviene tramite il [portale di Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sicurezza del canale
Tutti i dati inviati in entrambe le direzioni vengono crittografati durante il transito.

### <a name="audit-logs"></a>Log di controllo
Ogni accesso alla console seriale viene attualmente registrato nei log di [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) della macchina virtuale. L'accesso a questi log è di proprietà dell'amministratore della macchina virtuale di Azure, che ne ha anche il controllo.

> [!CAUTION]
> Non viene registrata alcuna password di accesso per la console. Tuttavia, se i comandi eseguiti nella console contengono o restituiscono password, segreti, nomi utente o qualsiasi altra forma di informazioni personali, tali elementi verranno scritti nei log di diagnostica di avvio della macchina virtuale, insieme a qualsiasi altro testo visibile, durante l'implementazione della funzione di scorrimento all'indietro della console seriale. Questi log sono circolari e solo gli utenti con autorizzazioni di lettura per l'account di archiviazione di diagnostica possono accedervi. Tuttavia, è preferibile seguire la procedura consigliata che prevede l'uso di Desktop remoto per tutto ciò che può implicare segreti e/o informazioni personali.

### <a name="concurrent-usage"></a>Utilizzo simultaneo
Se un utente è connesso alla console seriale e un altro utente richiede correttamente l'accesso alla stessa macchina virtuale, il primo utente verrà disconnesso, mentre il secondo verrà connesso alla stessa sessione.

> [!CAUTION]
> Questo significa che la sessione dell'utente che viene disconnesso non verrà chiusa. La possibilità di applicare una disconnessione al momento della disconnessione (usando SIGHUP o un meccanismo simile) è ancora in programma di roadmap. Nella console amministrativa speciale (SAC, Special Administrative Console) è abilitato un timeout automatico per Windows, mentre per Linux è possibile configurare l'impostazione di timeout del terminale. A tale scopo, aggiungere `export TMOUT=600` nel file con estensione *bash_profile* o *profile* per l'utente con cui si esegue l'accesso alla console. Questa impostazione prevede il timeout della sessione dopo 10 minuti.

## <a name="accessibility"></a>Accessibilità
L'accessibilità è un fattore fondamentale per la console seriale di Azure. A tale scopo, è stato garantito che la console seriale sia completamente accessibile.

### <a name="keyboard-navigation"></a>Navigazione da tastiera
Usare il tasto **TAB** della tastiera per eseguire la navigazione nell'interfaccia della console seriale nel portale di Azure. La posizione verrà evidenziata sullo schermo. Per lasciare lo stato attivo della finestra della console seriale, premere **CTRL**+**F6** sulla tastiera.

### <a name="use-serial-console-with-a-screen-reader"></a>Usare la console seriale con un'utilità per la lettura dello schermo
La console seriale viene fornita con il supporto dell'utilità per la lettura dello schermo incorporato. La navigazione con un'utilità per la lettura dello schermo attivata consentirà la lettura a voce alta del testo alternativo del pulsante attualmente selezionato da parte dell'utilità per la lettura dello schermo.

## <a name="errors"></a>Errors
Poiché la maggior parte degli errori è temporanea, riprovare la connessione può spesso risolverli. La tabella seguente contiene un elenco di errori e il modo per risolverli. Questi errori e mitigazioni si applicano sia alle macchine virtuali che alle istanze dei set di scalabilità di macchine virtuali.

Errore                            |   Attenuazione
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica di avvio per *&lt;VMNAME&gt;* . Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata.
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale.
Non si hanno le autorizzazioni necessarie per usare questa macchina virtuale con la console seriale. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede determinate autorizzazioni. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio *&lt;STORAGEACCOUNTNAME&gt;* . Verificare che la diagnostica di avvio sia abilitata per questa VM e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario usare l'elenco elementi consentiti `*.console.azure.com`. Un approccio più dettagliato, ma a lungo termine è l'uso di un elenco elementi consentiti per gli [intervalli IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), che cambiano piuttosto regolarmente.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.

## <a name="known-issues"></a>Problemi noti
La console seriale presenta alcuni problemi. Di seguito è riportato un elenco dei problemi riscontrati e delle procedure necessarie per risolverli. Questi problemi e mitigazioni si applicano sia alle macchine virtuali che alle istanze dei set di scalabilità di macchine virtuali.

Problema                           |   Attenuazione
:---------------------------------|:--------------------------------------------|
Se si preme il tasto **INVIO** dopo il banner della connessione, non viene visualizzato un prompt di accesso. | Per altre informazioni, vedere [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Questo problema può verificarsi se si esegue una macchina virtuale personalizzata, un dispositivo con protezione avanzata o una configurazione di GRUB che causa la mancata connessione di Linux alla porta seriale.
Il testo della console seriale occupa solo una parte delle dimensioni dello schermo (spesso dopo l'uso di un editor di testo). | Le console seriali non supportano la negoziazione sulle dimensioni della finestra ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)): questo significa che non sarà presente alcun segnale SIGWINCH inviato per aggiornare le dimensioni dello schermo e la macchina virtuale non avrà alcuna conoscenza delle dimensioni del terminale. Installare xterm o un'utilità analoga per fornire il comando `resize` e quindi eseguire `resize`.
L'operazione di incollare le stringhe lunghe non funziona. | La console seriale limita la lunghezza delle stringhe incollate nel terminale a 2048 caratteri per impedire il sovraccarico della larghezza di banda della porta seriale.
La console seriale non funziona con un firewall dell'account di archiviazione. | Per impostazione predefinita, la console seriale non funziona con i firewall dell'account di archiviazione abilitati nell'account di archiviazione della diagnostica di avvio.
Console seriale non funziona con un account di archiviazione usando Azure Data Lake Storage Gen2 con spazi dei nomi gerarchici. | Si tratta di un problema noto con gli spazi dei nomi gerarchici. Per attenuare, verificare che l'account di archiviazione della diagnostica di avvio della macchina virtuale non venga creato con Azure Data Lake Storage Gen2. Questa opzione può essere impostata solo al momento della creazione dell'account di archiviazione. Potrebbe essere necessario creare un account di archiviazione di diagnostica di avvio separato senza Azure Data Lake Storage Gen2 abilitato per attenuare questo problema.
Input da tastiera irregolare nelle immagini SLES BYOS. L'input da tastiera viene riconosciuto solo sporadicamente. | Si tratta di un problema con il pacchetto Plymouth. Plymouth non deve essere eseguito in Azure perché non è necessaria una schermata iniziale e Plymouth interferisce con la possibilità di usare la console seriale. Rimuovere Plymouth con `sudo zypper remove plymouth` e quindi riavviare. In alternativa, modificare la riga del kernel della configurazione di GRUB aggiungendo `plymouth.enable=0` alla fine della riga. Questa operazione può essere eseguita [modificando la voce di avvio in fase di avvio](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)oppure modificando la riga `/etc/default/grub`GRUB_CMDLINE_LINUX in, ricompilando GRUB con `grub2-mkconfig -o /boot/grub2/grub.cfg`e riavviando il sistema.


## <a name="frequently-asked-questions"></a>Domande frequenti

**D. Come è possibile inviare commenti e suggerimenti?**

R. Fornire commenti e suggerimenti segnalando un problema GitHub in https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), è possibile inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di https://feedback.azure.com.

**D. La console seriale supporta le operazioni di copia e incolla?**

R. Sì. Usare **CTRL**+**MAIUSC**+**C** e **CTRL**+**MAIUSC**+**V** per copiare e incollare nel terminale.

**D. È possibile usare la console seriale anziché una connessione SSH?**

R. Anche se ciò potrebbe essere tecnicamente possibile, la console seriale è destinata a essere usata principalmente come strumento di risoluzione dei problemi nelle situazioni in cui la connettività tramite SSH non è possibile. Non è consigliabile usare la console seriale in sostituzione di SSH per i motivi seguenti:

- La console seriale non ha la stessa larghezza di banda di SSH. Poiché si tratta di una connessione di solo testo, più interazioni intense con l'interfaccia utente grafica sono difficili.
- L'accesso alla console seriale avviene attualmente solo tramite nome utente e password. Poiché le chiavi SSH sono molto più sicure rispetto alle combinazioni di nome utente/password, dal punto di vista della sicurezza di accesso è consigliabile usare SSH con la console seriale.

**D. Chi può abilitare o disabilitare la console seriale per una sottoscrizione?**

R. Per abilitare o disabilitare la console seriale a livello di sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. I ruoli con autorizzazione di scrittura sono quelli di amministratore o proprietario. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

**D. Chi può accedere alla console seriale per la VM o il set di scalabilità di macchine virtuali?**

R. Per accedere alla console seriale, è necessario avere il ruolo Collaboratore macchina virtuale o versione successiva per una VM o un set di scalabilità di macchine virtuali.

**D. La console seriale non visualizza nulla, cosa bisogna fare?**

R. L'immagine probabilmente non è configurata correttamente per l'accesso alla console seriale. Per informazioni sulla configurazione dell'immagine per abilitare la console seriale, vedere [Disponibilità delle distribuzioni della console seriale per Linux](#serial-console-linux-distribution-availability).

**D. La console seriale è disponibile per i set di scalabilità di macchine virtuali?**

R. Sì lo è! Vedere [console seriale per i set di scalabilità di macchine virtuali](#serial-console-for-virtual-machine-scale-sets)

**D. Se si configura la VM o il set di scalabilità di macchine virtuali usando solo l'autenticazione con chiave SSH, è possibile continuare a usare la console seriale per connettersi alla macchina virtuale/istanza del set di scalabilità di macchine virtuali?**

R. Sì. Poiché la console seriale non richiede le chiavi SSH, è sufficiente configurare una combinazione di nome utente/password. È possibile farlo selezionando **Reimposta password** nel portale di Azure e usare tali credenziali per accedere alla console seriale.

## <a name="next-steps"></a>Passaggi successivi
* Usare la console seriale per [accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).
* Usare la console seriale per le [chiamate NMI e SysRq](serial-console-nmi-sysrq.md).
* Informazioni su come usare la console seriale per [abilitare GRUB in varie distribuzioni](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* La console seriale è disponibile anche per [macchine virtuali Windows](../windows/serial-console.md).
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md).

