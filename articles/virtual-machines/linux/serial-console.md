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
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: 857998c73abed76c9e20d5b3422ce607fb9f733d
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782882"
---
# <a name="virtual-machine-serial-console-preview"></a>Console seriale per macchine virtuali (anteprima) 


La console seriale per macchine virtuali di Azure fornisce l'accesso a una console basata su testo per macchine virtuali Linux. Questa connessione seriale viene stabilita con la porta seriale COM1 della macchina virtuale e fornisce l'accesso alla macchina virtuale che è indipendente dallo stato del sistema operativo o della rete della macchina virtuale. L'accesso alla console seriale per una macchina virtuale attualmente può essere eseguito solo tramite il portale di Azure ed è consentito solo agli utenti che dispongono dell'accesso Collaboratore Macchina virtuale o di livello superiore alla VM. 

Per la documentazione della console seriale per macchine virtuali Windows [fare clic qui](../windows/serial-console.md).

> [!Note] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure]. (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Questo servizio è attualmente in **anteprima pubblica** e l'accesso alla console seriale per le macchine virtuali è disponibile per le aree di Azure globali. La console seriale per il momento non è disponibile per il cloud Azure per enti pubblici, Azure Germania e Azure Cina.


## <a name="prerequisites"></a>Prerequisiti 

* È necessario usare il modello di distribuzione di Gestione risorse. Non sono supportate le distribuzioni classiche. 
* La macchina virtuale DEVE avere la [diagnostica di avvio](boot-diagnostics.md) abilitata, vedere la schermata seguente.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* L'account di Azure che usa la console seriale deve avere il [ruolo Collaboratore](../../role-based-access-control/built-in-roles.md) per la VM e l'account di archiviazione della [diagnostica di avvio](boot-diagnostics.md). 
* La macchina virtuale su cui si sta cercando di accedere alla console seriale deve avere anche un account basato su password. È possibile crearne uno tramite la funzionalità di [reimpostazione della password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione dell'accesso alla macchina virtuale; vedere la schermata seguente.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Per le impostazioni specifiche per le distribuzioni di Linux, vedere [Accedere alla console seriale per Linux](#access-serial-console-for-linux)



## <a name="get-started-with-serial-console"></a>Introduzione alla console seriale
La console seriale per le macchine virtuali è accessibile solo tramite il [portale di Azure](https://portal.azure.com). Di seguito sono riportati i passaggi per accedere alla console seriale per macchine virtuali tramite il portale 

  1. Aprire il portale di Azure
  2. Nel menu a sinistra selezionare Macchine virtuali.
  3. Fare clic sulla macchina virtuale nell'elenco. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.
  4. Scorrere verso il basso fino alla sezione Supporto e risoluzione dei problemi e fare clic sull'opzione Console seriale (anteprima). Verrà aperto un nuovo riquadro con la console seriale e verrà avviata la connessione.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> La console seriale richiede un utente locale configurato con una password. Le macchine virtuali configurate solo con una chiave pubblica SSH attualmente non hanno accesso alla console seriale. Per creare un utente locale con password, usare l'[estensione Vmaccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) (disponibile anche nel portale facendo clic su "Reimposta password") e creare un utente locale con una password.

## <a name="access-serial-console-for-linux"></a>Accedere alla console seriale per Linux
Affinché la console seriale funzioni correttamente, il sistema operativo guest deve essere configurato per leggere e scrivere i messaggi della console nella porta seriale. La maggior parte delle [distribuzioni di Azure per Linux approvate](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ha la console seriale configurata per impostazione predefinita. È sufficiente fare clic nella sezione della Console seriale nel portale di Azure fornirà l'accesso alla console. 

Distribuzione      | Accesso alla Console seriale
:-----------|:---------------------
Red Hat Enterprise Linux.    | Le immagini Red Hat Enterprise Linux disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. 
CentOS      | Le immagini CentOS disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. 
Ubuntu      | Le immagini Ubuntu disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita.
CoreOS      | Le immagini CoreOS disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita.
SUSE        | Le immagini SLES più recenti disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. Se si usano versioni precedenti (fino alla 10) di SLES in Azure, seguire le istruzioni riportate nell'[articolo della knowledge base](https://www.novell.com/support/kb/doc.php?id=3456486) per abilitare la console seriale. 
Oracle Linux        | Le immagini Oracle Linux disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita.
Immagini personalizzate di Linux     | Per abilitare la console seriale per l'immagine personalizzata della VM Linux, abilitare l'accesso alla console in /etc/inittab per l'esecuzione di un terminale in ttyS0. Qui è riportato un esempio di aggiunta nel file `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Per altre informazioni su come creare correttamente immagini personalizzate, vedere [Creazione e caricamento di un file VHD Linux in Azure](https://aka.ms/createuploadvhd).

## <a name="common-scenarios-for-accessing-serial-console"></a>Scenari comuni per l'accesso alla console seriale 
Scenario          | Azioni nella console seriale                
:------------------|:-----------------------------------------
File FSTAB danneggiato | Premere `Enter` per continuare e correggere il file fstab usando un editor di testo. Per farlo potrebbe essere necessario essere in modalità utente singolo. Per iniziare, vedere la pagina relativa alla [risoluzione dei problemi dei file fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [Uso della console seriale per accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md).
Regole del firewall non corrette | Accedere alla console seriale e correggere gli iptable. 
Danneggiamento/Controllo del file system | Accedere alla console seriale e recuperare il file system. 
Problemi di configurazione SSH/RDP | Accedere alla console seriale e modificare le impostazioni. 
Sistema di blocco della rete| Accedere alla console seriale tramite il portale per gestire il sistema. 
Interazione con bootloader | Accedere a GRUB tramite la console seriale. Per iniziare, vedere [Uso della console seriale per accedere a GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md). 

## <a name="disable-serial-console"></a>Disabilitare la console seriale
Per impostazione predefinita, tutte le sottoscrizioni hanno accesso alla console seriale in tutte le macchine virtuali. È possibile disabilitare la console seriale a livello di sottoscrizione o a livello di macchina virtuale.

### <a name="subscription-level-disable"></a>Disattivazione a livello di sottoscrizione
La console seriale può essere disattivata per un'intera sottoscrizione tramite la [chiamata all'API REST di Disabilita console](https://aka.ms/disableserialconsoleapi). È possibile usare la funzionalità "Try It" disponibile nella pagina della documentazione API per disabilitare e abilitare la console seriale per una sottoscrizione. Inserire `subscriptionId`, "predefinito" nel `default` campo e fare clic su Esegui. I comandi dell'interfaccia della riga di comando di Azure non sono ancora disponibili e verranno visualizzati in un secondo momento. [Ripetere la chiamata all'API REST qui](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

In alternativa, è possibile usare il set di comandi in Cloud Shell seguente (comandi bash mostrati) per disabilitare, abilitare e visualizzare lo stato disabilitato della console seriale per una sottoscrizione. 

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
La console seriale può essere disabilitata per macchine virtuali specifiche disabilitando l'impostazione della diagnostica di avvio per tali macchine virtuali. È sufficiente disattivare la diagnostica di avvio dal portale di Azure e verrà disabilitata una console seriale per la macchina virtuale.

## <a name="serial-console-security"></a>Sicurezza della console seriale 

### <a name="access-security"></a>Sicurezza dell'accesso 
L'accesso alla console seriale è limitato agli utenti che hanno l'accesso [Collaboratore macchine virtuali](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) o superiore per la macchina virtuale. Se il tenant di AAD richiede Multi-Factor Authentication, MFA sarà necessario anche per l'accesso alla console seriale perché l'accesso viene stabilito tramite il [portale di Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sicurezza del canale
Tutti i dati inviati in entrambe le direzioni vengono crittografati durante il transito.

### <a name="audit-logs"></a>Log di controllo
Ogni accesso alla console seriale viene attualmente registrato nei log di [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) della macchina virtuale. L'accesso a questi log è di proprietà dell'amministratore della macchina virtuale di Azure, che ne ha anche il controllo.  

>[!CAUTION] 
Anche se non vengono registrate password di accesso per la console, se i comandi eseguiti nella console contengono o visualizzano password, segreti, nomi utente o qualsiasi altra forma di informazioni personali, tali elementi verranno scritti nei log di diagnostica di avvio della macchina virtuale, con qualsiasi altro testo visibile, durante l'implementazione della funzionalità di scorrimento all'indietro della console seriale. Questi log sono circolari e solo gli utenti con autorizzazioni di lettura per l'account di archiviazione di diagnostica possono accedervi, ma è preferibile seguire la procedura consigliata che prevede l'uso della console SSH per tutto ciò che può implicare segreti e/o informazioni personali. 

### <a name="concurrent-usage"></a>Utilizzo simultaneo
Se un utente è connesso alla console seriale e un altro utente ottiene l'accesso alla stessa macchina virtuale, il primo utente verrà disconnesso, mentre il secondo verrà connesso, come se il primo utente si alzasse e lasciasse la console fisica e un nuovo utente ne prendesse il posto.

>[!CAUTION] 
Questo significa che la sessione dell'utente che viene disconnesso non verrà chiusa. Non è ancora possibile imporre la chiusura della sessione in caso di disconnessione (tramite SIGHUP o un meccanismo simile). Nella console SAC è abilitato un timeout automatico per Windows, mentre per Linux è possibile configurare l'impostazione di timeout del terminale. Per impostare il timeout della sessione dopo 10 minuti, aggiungere semplicemente `export TMOUT=600` nel file con estensione bash_profile o profile per l'utente con cui si esegue l'accesso alla console.

## <a name="accessibility"></a>Accessibilità
L'accessibilità è un obiettivo chiave della console seriale di Azure. A tale scopo, è stato garantito che la console seriale sia accessibile a utenti con problemi di vista e di udito, nonché a utenti che potrebbero non essere in grado di usare il mouse.

### <a name="keyboard-navigation"></a>Navigazione da tastiera
Usare il tasto `tab` sulla tastiera per eseguire la navigazione nell'interfaccia della console seriale nel portale di Azure. La posizione verrà evidenziata sullo schermo. Per lasciare lo stato attivo del pannello della console seriale, premere `Ctrl + F6` sulla tastiera.

### <a name="use-serial-console-with-a-screen-reader"></a>Usare la console seriale con un'utilità per la lettura dello schermo
La console seriale viene fornita con il supporto dell'utilità per la lettura dello schermo incorporato. La navigazione con un'utilità per la lettura dello schermo attivata consentirà la lettura a voce alta del testo alternativo del pulsante attualmente selezionato da parte dell'utilità per la lettura dello schermo.

## <a name="errors"></a>Errors
La maggior parte degli errori è di natura temporanea e, per risolverli, è spesso sufficiente stabilire di nuovo la connessione alla console seriale. La tabella seguente contiene un elenco di errori e il modo per prevenirli

Tipi di errore                            |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica per "<VMNAME>". Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata. 
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale
Non si hanno le autorizzazioni necessarie per usare questa console seriale per la VM. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore macchine virtuali.| L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio "<STORAGEACCOUNTNAME>". Verificare che la diagnostica di avvio sia abilitata per questa macchina virtuale e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario usare l'elenco elementi consentiti `*.console.azure.com`. Un approccio più dettagliato, ma a lungo termine è l'uso di un elenco elementi consentiti per gli [intervalli IP dei data center di Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), che cambiano piuttosto regolarmente.
## <a name="known-issues"></a>Problemi noti 
Poiché l'accesso alla console seriale è ancora in fase di anteprima, è in corso la soluzione di alcuni problemi noti. Di seguito è riportato un elenco di questi problemi con le possibili soluzioni alternative:

Problema                           |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Non sono disponibili opzioni per la console seriale dell'istanza del set di scalabilità di macchine virtuali |  In fase di anteprima l'accesso alla console seriale per le istanze del set di scalabilità di macchine virtuali non è supportato.
Premendo INVIO dopo il banner della connessione, non viene visualizzato un prompt di accesso | Vedere questa pagina: [L'inserimento non esegue alcuna operazione](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Questo può verificarsi se è in esecuzione una macchina virtuale personalizzata, un'appliance con protezione avanzata o una configurazione GRUB che non consente a Linux di connettersi correttamente alla porta seriale.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.
Il testo della console seriale occupa solo una parte delle dimensioni dello schermo (spesso dopo l'uso di un editor di testo) | Si tratta di un problema noto con dimensioni dello schermo sconosciute su connessioni seriali. Si consiglia di installare xterm o un'altra utilità simile che fornisca il comando 'resize'. L'esecuzione di 'resize' risolverà il problema.


## <a name="frequently-asked-questions"></a>Domande frequenti 
**D. Come è possibile inviare commenti e suggerimenti?**

R. Per inviare commenti e suggerimenti, andare a https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di http://feedback.azure.com

**D. Impossibile accedere alla console seriale. Dove è possibile inserire un caso di supporto?**

R. Questa funzionalità di anteprima è soggetta alle condizioni per l'anteprima di Azure. Per ottenere supporto, è consigliabile usare i canali indicati in precedenza. 

**D. È possibile usare la console seriale anziché una connessione SSH?**

R. Anche se ciò potrebbe essere tecnicamente possibile, la console seriale è destinata a essere usata principalmente come strumento di risoluzione dei problemi nelle situazioni in cui la connettività tramite SSH non è possibile. Non è consigliabile usare la console seriale in sostituzione di SSH per due motivi:

1. La console seriale non dispone della larghezza di banda di SSH: è una connessione di solo testo e pertanto le interazioni con intensa attività GUI saranno difficili nella console seriale.
1. L'accesso alla console seriale avviene attualmente solo tramite nome utente e password. Le chiavi SSH sono molto più sicure rispetto alle combinazioni di nome utente/password. Dal punto di vista della sicurezza di accesso è consigliabile usare SSH con la console seriale.



## <a name="next-steps"></a>Passaggi successivi
* Usare la console seriale per accedere a [GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md)
* Usare la console seriale per [NMI e SysRq chiamate](serial-console-nmi-sysrq.md)
* La console seriale è disponibile anche per macchine virtuali [Windows](../windows/serial-console.md)
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md)