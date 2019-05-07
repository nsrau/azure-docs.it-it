---
title: Azure Console seriale per Linux | Microsoft Docs
description: Console seriale bidirezionale per macchine virtuali di Azure e set di scalabilità di macchine virtuali.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: harijay
ms.openlocfilehash: 7019d80c05a1953f4e57f0f42d46588310911791
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141104"
---
# <a name="azure-serial-console-for-linux"></a>Azure Console seriale per Linux

La Console seriale nel portale di Azure fornisce l'accesso a una console basata su testo per macchine virtuali (VM) Linux e istanze (set di scalabilità di macchine virtuali) del set di scalabilità di macchine virtuali. Questa connessione seriale si connette alla porta seriale COM1 della macchina virtuale o istanza di set di scalabilità di macchine virtuali, che fornisce accesso a esso indipendenti dello stato del sistema operativo o di rete. La console seriale è accessibile solo tramite il portale di Azure e viene consentito solo per gli utenti che dispongono di un ruolo di accesso di collaboratore o versione successiva per il set di scalabilità della macchina virtuale o una macchina virtuale.

Console seriale funziona allo stesso modo per le macchine virtuali e istanze del set di scalabilità di macchine virtuali. In questo documento, tutti i riferimenti alle macchine virtuali in modo implicito include istanze di set di scalabilità di macchine virtuali se non indicato diversamente.

Per la documentazione della Console seriale per Windows, vedere [Console seriale per Windows](../windows/serial-console.md).

> [!NOTE]
> La Console seriale è disponibile a livello generale in aree globali di Azure. Non è al momento disponibile nei cloud di Azure per enti pubblici o di Azure Cina.


## <a name="prerequisites"></a>Prerequisiti

- L'istanza di set di scalabilità di macchine Virtuali o una macchina virtuale deve usare il modello di distribuzione resource Manager. Le distribuzioni classiche non sono supportate.

- L'account che utilizza la console seriale deve avere il [ruolo Collaboratore macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) per la macchina virtuale e il [diagnostica di avvio](boot-diagnostics.md) account di archiviazione

- L'istanza di set di scalabilità di macchine Virtuali o una macchina virtuale deve avere un utente basato su password. È possibile crearne uno con la funzione di [reimpostazione della password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**.

- L'istanza di set di scalabilità di macchine Virtuali o una macchina virtuale deve avere [diagnostica di avvio](boot-diagnostics.md) abilitata.

    ![Impostazioni di diagnostica di avvio](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Per le impostazioni specifiche delle distribuzioni Linux, vedere [Disponibilità delle distribuzioni della console seriale per Linux](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>Introduzione alla Console seriale
La Console seriale per macchine virtuali e set di scalabilità di macchine virtuali è accessibile solo tramite il portale di Azure:

### <a name="serial-console-for-virtual-machines"></a>Console seriale per macchine virtuali
Console seriale per macchine virtuali è semplice come facendo clic su **console seriale** all'interno di **supporto + risoluzione dei problemi** sezione nel portale di Azure.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **tutte le risorse** e selezionare una macchina virtuale. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.

  1. Scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi** e selezionare **Console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Finestra della Console seriale di Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console seriale per il set di scalabilità di macchine virtuali
Console seriale è disponibile in una base per istanza per il set di scalabilità di macchine virtuali. Sarà necessario passare a istanza singola di un set di scalabilità di macchine virtuali prima di visualizzare il **console seriale** pulsante. Se il set di scalabilità di macchine virtuali non dispone di diagnostica di avvio abilitata, assicurarsi di che aggiornare il modello di set di scalabilità di macchine virtuali per abilitare la diagnostica di avvio e quindi aggiornare tutte le istanze nel nuovo modello per poter accedere alla console seriale.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **tutte le risorse** e selezionare un Set di scalabilità di macchine virtuali. Pagina di panoramica per la scalabilità di macchine virtuali impostato si apre.

  1. Passare a **istanze**

  1. Selezionare un'istanza di set di scalabilità di macchine virtuali

  1. Dal **supporto + risoluzione dei problemi** sezione, selezionare **console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Console seriale del set di scalabilità di macchine virtuali Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> La console seriale richiede un utente locale con una password configurata. Le macchine virtuali o set di scalabilità di macchine virtuali configurate solo con una chiave pubblica SSH non è possibile accedere alla console seriale. Per creare un utente locale con una password, usare l'[estensione VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) disponibile nel portale selezionando **Reimposta password** nel portale di Azure e creare un utente locale con una password.
> È possibile anche reimpostare la password di amministratore nell'account [usando GRUB per eseguire l'avvio in modalità utente singolo](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilità di distribuzione di Linux Console seriale
Affinché la console seriale funzioni correttamente, il sistema operativo guest deve essere configurato per leggere e scrivere i messaggi della console nella porta seriale. La maggior parte delle [distribuzioni di Azure per Linux approvate](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ha la console seriale configurata per impostazione predefinita. Se si seleziona **Console seriale** nella sezione **Supporto e risoluzione dei problemi** del portale di Azure si consente l'accesso alla console seriale.

Distribuzione      | Accesso alla console seriale
:-----------|:---------------------
Red Hat Enterprise Linux.    | L'accesso alla console seriale è abilitato per impostazione predefinita.
CentOS      | L'accesso alla console seriale è abilitato per impostazione predefinita.
Ubuntu      | L'accesso alla console seriale è abilitato per impostazione predefinita.
CoreOS      | L'accesso alla console seriale è abilitato per impostazione predefinita.
SUSE        | Le immagini SLES più recenti disponibili in Azure hanno l'accesso alla console seriale abilitato per impostazione predefinita. Se si usano versioni precedenti (fino alla 10) di SLES in Azure, vedere l'[articolo della Knowledge Base](https://www.novell.com/support/kb/doc.php?id=3456486) per abilitare la console seriale.
Oracle Linux        | L'accesso alla console seriale è abilitato per impostazione predefinita.
Immagini personalizzate di Linux     | Per abilitare la console seriale per l'immagine personalizzata della VM Linux, abilitare l'accesso alla console nel file */etc/inittab* per l'esecuzione di un terminale in `ttyS0`. Ad esempio: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Per altre informazioni su come creare correttamente immagini personalizzate, vedere [Creazione e caricamento di un file VHD Linux in Azure](https://aka.ms/createuploadvhd). Se si sta creando un kernel personalizzato, provare ad abilitare questi flag kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Il file di configurazione si trova in genere nel percorso */boot/*.

> [!NOTE]
> Se non viene visualizzato nulla nella console seriale, verificare che la diagnostica di avvio sia abilitata nella macchina virtuale. Raggiungendo **invio** sarà spesso risolvere i problemi in cui non viene visualizzato nella console seriale.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Scenari comuni per l'accesso alla Console seriale

Scenario          | Azioni nella Console seriale
:------------------|:-----------------------------------------
File *FSTAB* danneggiato | Premere il tasto **INVIO** per continuare e usare un editor di testo per correggere il file *FSTAB*. Per farlo potrebbe essere necessario essere in modalità utente singolo. Per altre informazioni, vedere la sezione della console seriale del [come risolvere i problemi di fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [usare la console seriale per accedere a GRUB e modalità utente singolo](serial-console-grub-single-user-mode.md).
Regole del firewall non corrette |  Se è stato configurato iptables per bloccare la connettività SSH, è possibile utilizzare la console seriale per interagire con la macchina virtuale senza la necessità di SSH. Altre informazioni, vedere la [iptables man pagina](https://linux.die.net/man/8/iptables).<br>Analogamente, se il firewalld sta bloccando l'accesso SSH, è possibile accedere alla macchina virtuale tramite console seriale e riconfigurare firewalld. Altre informazioni sono reperibili nel [firewalld documentazione](https://firewalld.org/documentation/).
Danneggiamento/Controllo del file system | Vedere la sezione della console seriale del [VM Linux di Azure non è possibile avviare a causa di errori del file system](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) per altre informazioni dettagliate sulla risoluzione dei problemi danneggiato sistemi di file tramite console seriale.
Problemi di configurazione SSH | Accedere alla console seriale e modificare le impostazioni. Console seriale è utilizzabile indipendentemente dalla configurazione SSH di una macchina virtuale perché non richiede la macchina virtuale avere la connettività di rete a funzionare. Una Guida alla risoluzione dei problemi è disponibile all'indirizzo [risolvere i problemi di connessione SSH a una VM Linux di Azure che ha esito negativo, genera errori o è stata rifiutata](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Altri dettagli sono disponibili in [dettagliate SSH risoluzione dei problemi di connessione a una VM Linux in Azure](./detailed-troubleshoot-ssh-connection.md)
Interazione con bootloader | Riavviare la macchina virtuale all'interno del pannello della console seriale per accedere a GRUB nella VM Linux. Per altre informazioni dettagliate e informazioni di specifica della distribuzione, vedere [usare la console seriale per accedere a GRUB e modalità utente singolo](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Disabilitare la Console seriale
Per impostazione predefinita, tutte le sottoscrizioni hanno accesso alla console seriale abilitato. È possibile disabilitare la console seriale nel server di livello di abbonamento o VM/virtuale a livello di set di scalabilità macchina. Si noti che la diagnostica di avvio deve essere abilitata in una macchina virtuale affinché console seriale per lavorare.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Disabilitazione a livello di set di scalabilità della macchina virtuale o una macchina virtuale
È possibile disabilitare la console seriale per una specifica macchina virtuale o macchina virtuale di set di scalabilità disabilitando l'impostazione di diagnostica di avvio. Disattivare la diagnostica di avvio dal portale di Azure consente di disabilitare la console seriale per la macchina virtuale o il set di scalabilità di macchine virtuali. Se si usa la console seriale su un set di scalabilità di macchine virtuali, assicurarsi di che aggiornare istanze del set di scalabilità di macchine virtuali per il modello più recente.

> [!NOTE]
> Per abilitare o disabilitare la console seriale per una sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. Queste autorizzazioni comprendono i ruoli di amministratore o proprietario. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

### <a name="subscription-level-disable"></a>Disattivazione a livello di sottoscrizione
La console seriale può essere disabilitata per un'intera sottoscrizione tramite la [chiamata all'API REST di Disabilita console](/rest/api/serialconsole/console/disableconsole). È possibile usare la funzionalità **Try It** disponibile nella pagina della documentazione API per disabilitare e abilitare la console seriale per una sottoscrizione. Immettere l'ID sottoscrizione per **subscriptionId**, immettere **default** per **default** e quindi selezionare **Esegui**. I comandi dell'interfaccia della riga di comando di Azure non sono ancora disponibili.

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
> Questo significa che la sessione dell'utente che viene disconnesso non verrà chiusa. La possibilità di applicare una disconnessione di disconnessione (tramite SIGHUP o un meccanismo simile) è ancora in futuro. Nella console amministrativa speciale (SAC, Special Administrative Console) è abilitato un timeout automatico per Windows, mentre per Linux è possibile configurare l'impostazione di timeout del terminale. A tale scopo, aggiungere `export TMOUT=600` nel file con estensione *bash_profile* o *profile* per l'utente con cui si esegue l'accesso alla console. Questa impostazione prevede il timeout della sessione dopo 10 minuti.

## <a name="accessibility"></a>Accessibilità
Accessibilità è un obiettivo chiave per la Console seriale di Azure. A tale scopo, è stato garantito che la console seriale sia completamente accessibile.

### <a name="keyboard-navigation"></a>Navigazione da tastiera
Usare il tasto **TAB** della tastiera per eseguire la navigazione nell'interfaccia della console seriale nel portale di Azure. La posizione verrà evidenziata sullo schermo. Per lasciare lo stato attivo della finestra della console seriale, premere **CTRL**+**F6** sulla tastiera.

### <a name="use-serial-console-with-a-screen-reader"></a>Usare la Console seriale con una lettura dello schermo
La console seriale viene fornita con il supporto dell'utilità per la lettura dello schermo incorporato. La navigazione con un'utilità per la lettura dello schermo attivata consentirà la lettura a voce alta del testo alternativo del pulsante attualmente selezionato da parte dell'utilità per la lettura dello schermo.

## <a name="errors"></a>Errors
Poiché la maggior parte degli errori è temporanea, riprovare la connessione può spesso risolverli. La tabella seguente contiene un elenco di errori e il modo per risolverli. Si applicano questi errori e soluzioni di prevenzione per entrambe le macchine virtuali e istanze del set di scalabilità di macchine virtuali.

Tipi di errore                            |   Mitigazione
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica di avvio per *&lt;VMNAME&gt;*. Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata.
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale.
Non si hanno le autorizzazioni necessarie per usare questa macchina virtuale con la console seriale. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede determinate autorizzazioni. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio *&lt;STORAGEACCOUNTNAME&gt;*. Verificare che la diagnostica di avvio sia abilitata per questa VM e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario usare l'elenco elementi consentiti `*.console.azure.com`. Un approccio più dettagliato, ma a lungo termine è l'uso di un elenco elementi consentiti per gli [intervalli IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), che cambiano piuttosto regolarmente.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.

## <a name="known-issues"></a>Problemi noti
La console seriale presenta alcuni problemi. Di seguito è riportato un elenco dei problemi riscontrati e delle procedure necessarie per risolverli. Si applicano questi problemi e soluzioni di prevenzione per entrambe le macchine virtuali e istanze del set di scalabilità di macchine virtuali.

Problema                           |   Mitigazione
:---------------------------------|:--------------------------------------------|
Se si preme il tasto **INVIO** dopo il banner della connessione, non viene visualizzato un prompt di accesso. | Per altre informazioni, vedere [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Questo problema può verificarsi se si esegue una macchina virtuale personalizzata, con protezione avanzata di appliance o configurazione GRUB che causa Linux a non riuscire a connettersi alla porta seriale.
Il testo della console seriale occupa solo una parte delle dimensioni dello schermo (spesso dopo l'uso di un editor di testo). | Le console seriali non supportano la negoziazione sulle dimensioni della finestra ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)): questo significa che non sarà presente alcun segnale SIGWINCH inviato per aggiornare le dimensioni dello schermo e la macchina virtuale non avrà alcuna conoscenza delle dimensioni del terminale. Installare xterm o un'utilità analoga per fornire il comando `resize` e quindi eseguire `resize`.
L'operazione di incollare le stringhe lunghe non funziona. | La console seriale limita la lunghezza delle stringhe incollate nel terminale a 2048 caratteri per impedire il sovraccarico della larghezza di banda della porta seriale.
La console seriale non funziona con un firewall dell'account di archiviazione. | Per impostazione predefinita, la console seriale non funziona con i firewall dell'account di archiviazione abilitati nell'account di archiviazione della diagnostica di avvio.


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

**D. Chi può accedere alla console seriale per il set di scalabilità di macchine virtuali/macchine Virtuali?**

R. È necessario disporre del ruolo di collaboratore macchina virtuale o versione successiva per una macchina virtuale o macchina virtuale di set di scalabilità per accedere alla console seriale.

**D. La console seriale non visualizza nulla, cosa bisogna fare?**

R. L'immagine probabilmente non è configurata correttamente per l'accesso alla console seriale. Per informazioni sulla configurazione dell'immagine per abilitare la console seriale, vedere [Disponibilità delle distribuzioni della console seriale per Linux](#serial-console-linux-distribution-availability).

**D. La console seriale è disponibile per i set di scalabilità di macchine virtuali?**

R. Sì, è! Vedere [Console seriale per il set di scalabilità di macchine virtuali](#serial-console-for-virtual-machine-scale-sets)

**D. Se configura la macchina virtuale o set usando solo autenticazione tramite chiave SSH di scalabilità di macchine virtuali, è possibile comunque utilizzare la console seriale per connettersi a mio istanza di set di scalabilità di macchine virtuali/macchine Virtuali?**

R. Sì. Poiché la console seriale non richiede le chiavi SSH, è sufficiente configurare una combinazione di nome utente/password. È possibile farlo selezionando **Reimposta password** nel portale di Azure e usare tali credenziali per accedere alla console seriale.

## <a name="next-steps"></a>Passaggi successivi
* Usare la console seriale per [accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).
* Usare la console seriale per le [chiamate NMI e SysRq](serial-console-nmi-sysrq.md).
* Informazioni su come usare la console seriale per [abilitare GRUB in varie distribuzioni](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* La console seriale è disponibile anche per [macchine virtuali Windows](../windows/serial-console.md).
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md).

