---
title: Console seriale per macchine virtuali di Azure | Microsoft Docs
description: Console seriale bidirezionale per macchine virtuali di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: 22128f027f0a218756e413653aa92ee097064587
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741709"
---
# <a name="virtual-machine-serial-console-for-linux"></a>Console seriale per macchine virtuali per Linux

La console seriale per macchine virtuali nel portale di Azure fornisce l'accesso a una console basata su testo per macchine virtuali Linux. Questa connessione seriale viene stabilita con la porta seriale COM1 della macchina virtuale e fornisce l'accesso alla macchina virtuale che è indipendente dallo stato del sistema operativo o della rete della macchina virtuale. È possibile accedere alla console seriale per una macchina virtuale solo tramite il portale di Azure. L'accesso è consentito solo agli utenti che hanno un ruolo di accesso di Collaboratore Macchina virtuale o un ruolo superiore nella macchina virtuale. 

Per la documentazione della console seriale per macchine virtuali Windows, vedere [Console seriale per macchine virtuali per Windows](../windows/serial-console.md).

> [!NOTE] 
> La console seriale per macchine virtuali è disponibile in genere nelle aree globali di Azure. Non è al momento disponibile nei cloud di Azure per enti pubblici o di Azure Cina.


## <a name="prerequisites"></a>Prerequisiti 

* La macchina virtuale da cui si accede a una console seriale deve usare il modello di distribuzione Resource Manager. Le distribuzioni classiche non sono supportate. 

* La macchina virtuale da cui si accede a una console seriale deve avere la [diagnostica di avvio](boot-diagnostics.md) abilitata. Selezionare **Diagnostica di avvio** nella sezione **Supporto e risoluzione dei problemi**.

    ![Impostazioni di diagnostica di avvio](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

Un account che usa la console seriale deve avere il [ruolo Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) per la macchina virtuale e l'account di archiviazione della [diagnostica di avvio](boot-diagnostics.md): 

* La macchina virtuale da cui si accede a una console seriale deve avere un account basato su password. È possibile crearne uno con la funzione di [reimpostazione della password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**. 

* Per le impostazioni specifiche delle distribuzioni Linux, vedere [Disponibilità delle distribuzioni della console seriale per Linux](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>Introduzione alla console seriale
La console seriale per le macchine virtuali è accessibile solo tramite il portale di Azure:

  1. Aprire il [portale di Azure](https://portal.azure.com).
  1. Nel menu a sinistra selezionare **Macchine virtuali**.
  1. Selezionare una macchina virtuale nell'elenco. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.
  1. Scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi** e selezionare **Console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

   ![Finestra della console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)



> [!NOTE] 
> La console seriale richiede un utente locale con una password configurata. Le macchine virtuali configurate solo con una chiave pubblica SSH non potranno accedere alla console seriale. Per creare un utente locale con una password, usare l'[estensione VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) disponibile nel portale selezionando **Reimposta password** nel portale di Azure e creare un utente locale con una password.
> È possibile anche reimpostare la password di amministratore nell'account [usando GRUB per eseguire l'avvio in modalità utente singolo](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilità delle distribuzioni della console seriale per Linux
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

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Scenari comuni per l'accesso alla console seriale 
Scenario          | Azioni nella console seriale                
:------------------|:-----------------------------------------
File *FSTAB* danneggiato | Premere il tasto **INVIO** per continuare e usare un editor di testo per correggere il file *FSTAB*. Per farlo potrebbe essere necessario essere in modalità utente singolo. Per altre informazioni, vedere la pagina relativa alla [risoluzione dei problemi del file fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [Usare la console seriale per accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).
Regole del firewall non corrette | Accedere alla console seriale e correggere gli iptable. 
Danneggiamento/Controllo del file system | Accedere alla console seriale e recuperare il file system. 
Problemi di configurazione SSH/RDP | Accedere alla console seriale e modificare le impostazioni. 
Sistema di blocco della rete| Accedere alla console seriale dal portale di Azure per gestire il sistema. 
Interazione con bootloader | Accedere a GRUB dalla console seriale. Per altre informazioni, vedere [Usare la console seriale per accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md). 

## <a name="disable-the-serial-console"></a>Disabilitare la console seriale
Per impostazione predefinita, tutte le sottoscrizioni hanno accesso alla console seriale in tutte le macchine virtuali. È possibile disabilitare la console seriale a livello di sottoscrizione o a livello di macchina virtuale.

> [!NOTE] 
> Per abilitare o disabilitare la console seriale per una sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. Queste autorizzazioni comprendono i ruoli di amministratore o proprietario. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

### <a name="subscription-level-disable"></a>Disattivazione a livello di sottoscrizione
La console seriale può essere disabilitata per un'intera sottoscrizione tramite la [chiamata all'API REST di Disabilita console](https://docs.microsoft.com/rest/api/serialconsole/console/console_disableconsole). È possibile usare la funzionalità **Try It** disponibile nella pagina della documentazione API per disabilitare e abilitare la console seriale per una sottoscrizione. Immettere l'ID sottoscrizione per **subscriptionId**, immettere **default** per **default** e quindi selezionare **Esegui**. I comandi dell'interfaccia della riga di comando di Azure non sono ancora disponibili.

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

### <a name="vm-level-disable"></a>Disabilitazione a livello di macchina virtuale
La console seriale può essere disabilitata per una macchina virtuale specifica disabilitando l'impostazione della diagnostica di avvio della macchina virtuale. Per disabilitare la console seriale per la macchina virtuale, disattivare la diagnostica di avvio dal portale di Azure.

## <a name="serial-console-security"></a>Sicurezza della console seriale 

### <a name="access-security"></a>Sicurezza dell'accesso 
L'accesso alla console seriale è consentito solo agli utenti che hanno un ruolo di accesso di [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) o un ruolo superiore nella macchina virtuale. Se il tenant di Azure Active Directory richiede l'autenticazione a più fattori (MFA), anche l'accesso alla console seriale richiederà MFA perché l'accesso della console seriale avviene tramite il [portale di Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sicurezza del canale
Tutti i dati inviati in entrambe le direzioni vengono crittografati durante il transito.

### <a name="audit-logs"></a>Log di controllo
Ogni accesso alla console seriale viene attualmente registrato nei log di [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) della macchina virtuale. L'accesso a questi log è di proprietà dell'amministratore della macchina virtuale di Azure, che ne ha anche il controllo.  

>[!CAUTION] 
Non viene registrata alcuna password di accesso per la console. Tuttavia, se i comandi eseguiti nella console contengono o restituiscono password, segreti, nomi utente o qualsiasi altra forma di informazioni personali, tali elementi verranno scritti nei log di diagnostica di avvio della macchina virtuale, insieme a qualsiasi altro testo visibile, durante l'implementazione della funzione di scorrimento all'indietro della console seriale. Questi log sono circolari e solo gli utenti con autorizzazioni di lettura per l'account di archiviazione di diagnostica possono accedervi. Tuttavia, è preferibile seguire la procedura consigliata che prevede l'uso di Desktop remoto per tutto ciò che può implicare segreti e/o informazioni personali. 

### <a name="concurrent-usage"></a>Utilizzo simultaneo
Se un utente è connesso alla console seriale e un altro utente richiede correttamente l'accesso alla stessa macchina virtuale, il primo utente verrà disconnesso, mentre il secondo verrà connesso alla stessa sessione.

>[!CAUTION] 
Questo significa che la sessione dell'utente che viene disconnesso non verrà chiusa. Non è ancora possibile imporre la chiusura della sessione in caso di disconnessione (tramite SIGHUP o un meccanismo simile). Nella console amministrativa speciale (SAC, Special Administrative Console) è abilitato un timeout automatico per Windows, mentre per Linux è possibile configurare l'impostazione di timeout del terminale. A tale scopo, aggiungere `export TMOUT=600` nel file con estensione *bash_profile* o *profile* per l'utente con cui si esegue l'accesso alla console. Questa impostazione prevede il timeout della sessione dopo 10 minuti.

## <a name="accessibility"></a>Accessibilità
L'accessibilità è un obiettivo chiave della console seriale di Azure. A tale scopo, è stato garantito che la console seriale sia completamente accessibile.

### <a name="keyboard-navigation"></a>Navigazione da tastiera
Usare il tasto **TAB** della tastiera per eseguire la navigazione nell'interfaccia della console seriale nel portale di Azure. La posizione verrà evidenziata sullo schermo. Per lasciare lo stato attivo della finestra della console seriale, premere **CTRL**+**F6** sulla tastiera.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Usare la console seriale con un'utilità per la lettura dello schermo
La console seriale viene fornita con il supporto dell'utilità per la lettura dello schermo incorporato. La navigazione con un'utilità per la lettura dello schermo attivata consentirà la lettura a voce alta del testo alternativo del pulsante attualmente selezionato da parte dell'utilità per la lettura dello schermo.

## <a name="errors"></a>Errors
Poiché la maggior parte degli errori è temporanea, riprovare la connessione può spesso risolverli. La tabella seguente contiene un elenco di errori e il modo per risolverli.

Tipi di errore                            |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica di avvio per *&lt;VMNAME&gt;*. Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata. 
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale.
Non si hanno le autorizzazioni necessarie per usare questa macchina virtuale con la console seriale. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede determinate autorizzazioni. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio *&lt;STORAGEACCOUNTNAME&gt;*. Verificare che la diagnostica di avvio sia abilitata per questa VM e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario usare l'elenco elementi consentiti `*.console.azure.com`. Un approccio più dettagliato, ma a lungo termine è l'uso di un elenco elementi consentiti per gli [intervalli IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), che cambiano piuttosto regolarmente.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.

## <a name="known-issues"></a>Problemi noti 
La console seriale presenta alcuni problemi. Di seguito è riportato un elenco dei problemi riscontrati e delle procedure necessarie per risolverli.

Problema                           |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Se si preme il tasto **INVIO** dopo il banner della connessione, non viene visualizzato un prompt di accesso. | Per altre informazioni, vedere [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Questo problema può verificarsi se è in esecuzione una macchina virtuale personalizzata, un'appliance con protezione avanzata o una configurazione GRUB che non consente a Linux di connettersi correttamente alla porta seriale.
Il testo della console seriale occupa solo una parte delle dimensioni dello schermo (spesso dopo l'uso di un editor di testo). | Le console seriali non supportano la negoziazione sulle dimensioni della finestra ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)): questo significa che non sarà presente alcun segnale SIGWINCH inviato per aggiornare le dimensioni dello schermo e la macchina virtuale non avrà alcuna conoscenza delle dimensioni del terminale. Installare xterm o un'utilità analoga per fornire il comando `resize` e quindi eseguire `resize`.
L'operazione di incollare le stringhe lunghe non funziona. | La console seriale limita la lunghezza delle stringhe incollate nel terminale a 2048 caratteri per impedire il sovraccarico della larghezza di banda della porta seriale.


## <a name="frequently-asked-questions"></a>Domande frequenti 

**D. Come è possibile inviare commenti e suggerimenti?**

R. Fornire commenti e suggerimenti segnalando un problema GitHub in https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), è possibile inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di http://feedback.azure.com.

**D. La console seriale supporta le operazioni di copia e incolla?**

R. Sì. Usare **CTRL**+**MAIUSC**+**C** e **CTRL**+**MAIUSC**+**V** per copiare e incollare nel terminale.

**D. È possibile usare la console seriale anziché una connessione SSH?**

R. Anche se ciò potrebbe essere tecnicamente possibile, la console seriale è destinata a essere usata principalmente come strumento di risoluzione dei problemi nelle situazioni in cui la connettività tramite SSH non è possibile. Non è consigliabile usare la console seriale in sostituzione di SSH per i motivi seguenti:

- La console seriale non ha la stessa larghezza di banda di SSH. Poiché si tratta di una connessione di solo testo, più interazioni intense con l'interfaccia utente grafica sono difficili.
- L'accesso alla console seriale avviene attualmente solo tramite nome utente e password. Poiché le chiavi SSH sono molto più sicure rispetto alle combinazioni di nome utente/password, dal punto di vista della sicurezza di accesso è consigliabile usare SSH con la console seriale.

**D. Chi può abilitare o disabilitare la console seriale per una sottoscrizione?**

R. Per abilitare o disabilitare la console seriale a livello di sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. I ruoli con autorizzazione di scrittura sono quelli di amministratore o proprietario. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

**D. Chi può accedere alla console seriale per la macchina virtuale?**

R. È necessario avere il ruolo di Collaboratore Macchina virtuale o un ruolo superiore per poter accedere alla console seriale della macchina virtuale. 

**D. La console seriale non visualizza nulla, cosa bisogna fare?**

R. L'immagine probabilmente non è configurata correttamente per l'accesso alla console seriale. Per informazioni sulla configurazione dell'immagine per abilitare la console seriale, vedere [Disponibilità delle distribuzioni della console seriale per Linux](#serial-console-linux-distribution-availability).

**D. La console seriale è disponibile per i set di scalabilità di macchine virtuali?**

R. L'accesso alla console seriale per le istanze del set di scalabilità di macchine virtuali non è attualmente supportato.

**D. Se si configura la macchina virtuale usando solo l'autenticazione con chiave SSH, è ancora possibile usare la console seriale per connettersi alla macchina virtuale?**

R. Sì. Poiché la console seriale non richiede le chiavi SSH, è sufficiente configurare una combinazione di nome utente/password. È possibile farlo selezionando **Reimposta password** nel portale di Azure e usare tali credenziali per accedere alla console seriale.

## <a name="next-steps"></a>Passaggi successivi
* Usare la console seriale per [accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).
* Usare la console seriale per le [chiamate NMI e SysRq](serial-console-nmi-sysrq.md).
* Informazioni su come usare la console seriale per [abilitare GRUB in varie distribuzioni](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* La console seriale è disponibile anche per [macchine virtuali Windows](../windows/serial-console.md).
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md).

