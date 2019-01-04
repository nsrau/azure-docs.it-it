---
title: Console seriale per macchine virtuali di Azure per Windows | Microsoft Docs
description: Console seriale bidirezionale per macchine virtuali Windows di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: 535c65f58ac9a3f39faa347ca853bfa410b7f182
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185333"
---
# <a name="virtual-machine-serial-console-for-windows"></a>Console seriale per macchine virtuali per Windows

La console seriale per macchine virtuali nel portale di Azure fornisce l'accesso a una console basata su testo per macchine virtuali Windows. Questa connessione seriale viene stabilita con la porta seriale COM1 della macchina virtuale e fornisce l'accesso alla macchina virtuale che è indipendente dallo stato del sistema operativo o della rete della macchina virtuale. È possibile accedere alla console seriale per una macchina virtuale solo tramite il portale di Azure. L'accesso è consentito solo agli utenti che hanno un ruolo di accesso di Collaboratore Macchina virtuale o un ruolo superiore nella macchina virtuale.

Per la documentazione della console seriale per macchine virtuali Linux, vedere [Console seriale per macchine virtuali per Linux](serial-console-linux.md).

> [!NOTE]
> La console seriale per macchine virtuali è disponibile in genere nelle aree globali di Azure. Non è al momento disponibile nei cloud di Azure per enti pubblici o di Azure Cina.


## <a name="prerequisites"></a>Prerequisiti

* La macchina virtuale da cui si accede a una console seriale deve usare il modello di distribuzione Resource Manager. Le distribuzioni classiche non sono supportate.

* La macchina virtuale da cui si accede a una console seriale deve avere la [diagnostica di avvio](boot-diagnostics.md) abilitata.

    ![Impostazioni di diagnostica di avvio](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Un account che usa la console seriale deve avere il [ruolo Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) per la macchina virtuale e l'account di archiviazione della [diagnostica di avvio](boot-diagnostics.md).

* La macchina virtuale da cui si accede a una console seriale deve avere un account basato su password. È possibile crearne uno con la funzione di [reimpostazione della password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**.


## <a name="get-started-with-the-serial-console"></a>Introduzione alla console seriale
La console seriale per le macchine virtuali è accessibile solo tramite il portale di Azure:

  1. Aprire il [portale di Azure](https://portal.azure.com).
  1. Nel menu a sinistra selezionare **Macchine virtuali**.
  1. Selezionare una macchina virtuale nell'elenco. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.
  1. Scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi** e selezionare **Console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

## <a name="enable-serial-console-functionality"></a>Abilitare la funzionalità della console seriale

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Abilitare la console seriale nelle immagini personalizzate o precedenti
Per le immagini di Windows Server più recenti in Azure la [console amministrativa speciale](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC, Special Administrative Console) è abilitata per impostazione predefinita. La console SAC è supportata nelle versioni server di Windows, ma non è disponibile nelle versioni client (ad esempio, Windows 10, Windows 8 o Windows 7).

Per le immagini di Windows Server precedenti, ovvero create prima di febbraio 2018, è possibile abilitare automaticamente la console seriale tramite la funzionalità dei comandi di esecuzione nel portale di Azure. Nel portale di Azure, selezionare **Esegui comandi**, quindi selezionare il comando denominato **EnableEM** dall'elenco.

![Eseguire l'elenco comandi](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

In alternativa, per abilitare manualmente la console seriale per le macchine virtuali Windows create prima di febbraio 2018, seguire questi passaggi:

1. Connettersi alla macchina virtuale Windows utilizzando Desktop remoto
1. Da un prompt dei comandi amministrativo eseguire i comandi seguenti:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Riavviare il sistema per abilitare la console SAC.

    ![Console SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessario, la console SAC può anche essere abilitata offline:

1. Collegare alla macchina virtuale esistente il disco di Windows per cui si vuole configurare la console SAC come disco dati.

1. Da un prompt dei comandi amministrativo eseguire i comandi seguenti:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Come capire se la console SAC è abilitata?

Se la [console SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) non è abilitata, nella console seriale non verrà mostrato il prompt SAC. In alcuni casi, vengono visualizzate le informazioni di integrità della macchina virtuale e in altri casi è vuota. Se si usa un'immagine di Windows Server creata prima di febbraio 2018, la console SAC probabilmente non verrà abilitata.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Abilitare il menu di avvio di Windows nella console seriale

Se è necessario abilitare i prompt del caricatore di avvio di Windows da visualizzare nella console seriale, è possibile aggiungere le opzioni seguenti ai dati di configurazione di avvio. Per ulteriori informazioni, vedere [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Connettersi alla macchina virtuale Windows utilizzando Desktop remoto.

1. Da un prompt dei comandi amministrativo eseguire i comandi seguenti:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Riavviare il sistema per abilitare il menu di avvio

> [!NOTE]
> Il timeout impostato per la visualizzazione del menu di gestione dell'avvio influisce sul tempo di avvio del sistema operativo. Se si ritiene che il valore di timeout di 10 secondi sia troppo lungo o troppo corto, impostarlo su un valore diverso.

## <a name="use-serial-console"></a>Usare la console seriale

### <a name="use-cmd-or-powershell-in-serial-console"></a>Usare CMD o PowerShell nella console seriale

1. Connettersi alla console seriale. Se ci si connette correttamente, la richiesta è **SAC >**:

    ![Connettersi a SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Digitare `cmd` per creare un canale con un'istanza di CMD.

1.  Digitare `ch -si 1` per passare al canale che esegue l'istanza di CMD.

1.  Premere **Invio**, quindi immettere le credenziali di accesso con autorizzazioni amministrative.

1.  Dopo avere immesso credenziali valide, verrà aperta l'istanza di CMD.

1.  Per avviare un'istanza di PowerShell, inserire `PowerShell` nell'istanza di CMD e quindi premere **Invio**.

    ![Aprire l'istanza di PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Usare la console seriale per le chiamate NMI
Un interrupt non mascherabile (NMI) è progettato per creare un segnale che il software in una macchina virtuale non ignora. In passato, gli NMI sono stati usati per verificare la presenza di problemi hardware in sistemi che necessitavano di tempi di risposta specifici. Oggi, gli amministratori di sistema e i programmatori usano spesso gli NMI come un meccanismo per eseguire il debug o risolvere i problemi di sistemi che sono bloccati.

La console seriale può essere usata per inviare un interrupt non mascherabile (NMI) a una macchina virtuale di Azure usando l'icona della tastiera sulla barra dei comandi. Dopo che l'interrupt non mascherabile viene recapitato, la configurazione della macchina virtuale potrà controllare la modalità di risposta del sistema. Windows può essere configurato per l'arresto anomalo del sistema e per la creazione di un file dump di arresto anomalo del sistema di memoria quando si riceve un NMI.

![Inviare NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Per informazioni sulla configurazione di Windows per creare un file dump di arresto anomalo del sistema quando riceve un NMI, vedere: [Come generare un file di dump di arresto anomalo del sistema usando un NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Usare le chiavi di funzione nella console seriale
Le chiavi di funzione sono abilitate per essere usate per la console seriale nelle macchine virtuali di Windows. L'opzione F8 nell'elenco a discesa della console seriale consente di accedere facilmente al menu delle impostazioni di avvio avanzate, ma la console seriale è compatibile con tutte le altre chiavi di funzione. Potrebbe essere necessario premere **Fn** + **F1** (o F2, F3 e così via) sulla tastiera a seconda del tipo di computer da cui si sta usando la console seriale.

### <a name="use-wsl-in-serial-console"></a>Usare WSL nella console seriale
Windows Subsystem for Linux (WSL) è stato abilitato per Windows Server 2019 e versioni successive ed è quindi possibile abilitare WSL per l'utilizzo nella console seriale se si esegue Windows Server 2019 o versione successiva. Questa possibilità potrebbe essere particolarmente utile per gli utenti che hanno familiarità anche con i comandi Linux. Per istruzioni su come abilitare WSL per Windows Server, vedere la [Guida all'installazione](https://docs.microsoft.com/windows/wsl/install-on-server).

## <a name="disable-serial-console"></a>Disabilitare la console seriale
Per impostazione predefinita, tutte le sottoscrizioni hanno accesso alla console seriale in tutte le macchine virtuali. È possibile disabilitare la console seriale a livello di sottoscrizione o a livello di macchina virtuale.

> [!NOTE]
> Per abilitare o disabilitare la console seriale per una sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. Queste autorizzazioni includono quelle di amministratore o proprietario, ma anche altri. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

### <a name="subscription-level-disable"></a>Disattivazione a livello di sottoscrizione
La console seriale può essere disabilitata per un'intera sottoscrizione tramite la [chiamata all'API REST di Disabilita console](/rest/api/serialconsole/console/disableconsole). È possibile usare la funzionalità **Try It** disponibile nella pagina della documentazione API per disabilitare e abilitare la console seriale per una sottoscrizione. Immettere l'ID sottoscrizione per **subscriptionId**, immettere "default" per **default** e quindi selezionare **Esegui**. I comandi dell'interfaccia della riga di comando di Azure non sono ancora disponibili.

![API REST Try It](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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
Questo significa che la sessione dell'utente che viene disconnesso non verrà chiusa. Non è ancora possibile imporre la chiusura della sessione in caso di disconnessione (tramite SIGHUP o un meccanismo simile). Nella console SAC è abilitato un timeout automatico per Windows; per Linux è possibile configurare l'impostazione di timeout del terminale.

## <a name="accessibility"></a>Accessibilità
L'accessibilità è un obiettivo chiave della console seriale di Azure. A tale scopo, è stato garantito che la console seriale sia accessibile a utenti con problemi di vista e di udito, nonché a utenti che potrebbero non essere in grado di usare il mouse.

### <a name="keyboard-navigation"></a>Navigazione da tastiera
Usare il tasto **TAB** della tastiera per eseguire la navigazione nell'interfaccia della console seriale nel portale di Azure. La posizione verrà evidenziata sullo schermo. Per lasciare lo stato attivo della finestra della console seriale, premere **CTRL**+**F6** sulla tastiera.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Usare la console seriale con un'utilità per la lettura dello schermo
La console seriale viene fornita con il supporto dell'utilità per la lettura dello schermo incorporato. La navigazione con un'utilità per la lettura dello schermo attivata consentirà la lettura a voce alta del testo alternativo del pulsante attualmente selezionato da parte dell'utilità per la lettura dello schermo.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Scenari comuni per l'accesso alla console seriale
Scenario          | Azioni nella console seriale
:------------------|:-----------------------------------------
Regole del firewall non corrette | Accedere alla console seriale e correggere le regole di Windows Firewall.
Danneggiamento/Controllo del file system | Accedere alla console seriale e recuperare il file system.
Problemi di configurazione RDP | Accedere alla console seriale e modificare le impostazioni. Per altre informazioni, vedere la [documentazione di Remote Desktop Protocol](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Sistema di blocco della rete | Accedere alla console seriale dal portale di Azure per gestire il sistema. Alcuni comandi di rete sono elencati in [Comandi di Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
Interazione con bootloader | Accedere ai dati configurazione di avvio tramite console seriale. Per altre informazioni, vedere [Abilitare il menu di avvio di Windows nella console seriale](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Errors
Poiché la maggior parte degli errori è temporanea, riprovare la connessione può spesso risolverli. La tabella seguente contiene un elenco di errori e il modo per risolverli.

Tipi di errore                            |   Mitigazione
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica di avvio per *&lt;VMNAME&gt;*. Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata.
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale
Non si hanno le autorizzazioni necessarie per usare questa console seriale per la VM. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede determinate autorizzazioni. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio *&lt;STORAGEACCOUNTNAME&gt;*. Verificare che la diagnostica di avvio sia abilitata per questa VM e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario usare l'elenco elementi consentiti `*.console.azure.com`. Un approccio più dettagliato, ma a lungo termine è l'uso di un elenco elementi consentiti per gli [intervalli IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), che cambiano piuttosto regolarmente.
Durante la connessione a una macchina virtuale Windows, vengono visualizzate solo informazioni sull'integrità| Questo errore viene visualizzato se la Special Administration Console non è stata abilitata per l'immagine di Windows. Visualizzare [Abilitare la console seriale nelle immagini personalizzate o precedenti](#enable-the-serial-console-in-custom-or-older-images) per istruzioni su come attivare manualmente la Special Administration Console (SAC) nella macchina virtuale di Windows. Per altre informazioni, vedere [Segnali di integrità di Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Problemi noti
La console seriale presenta alcuni problemi. Di seguito è riportato un elenco dei problemi riscontrati e delle procedure necessarie per risolverli.

Problema                             |   Mitigazione
:---------------------------------|:--------------------------------------------|
Se si preme il tasto **INVIO** dopo il banner della connessione, non viene visualizzato un prompt di accesso. | Per altre informazioni, vedere [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Questo errore può verificarsi se è in esecuzione una macchina virtuale personalizzata, un'appliance con protezione avanzata o una configurazione di avvio che non consente a Windows di connettersi correttamente alla porta seriale. Questo errore si verifica anche se si esegue una macchina virtuale client Windows 10, perché solo le macchine virtuali Windows Server sono configurate per l'abilitazione di EMS.
Non è possibile digitare nel prompt SAC se è abilitato il debug del kernel. | Effettuare una connessione RDP ed eseguire `bcdedit /debug {current} off` da un prompt dei comandi con privilegi elevati. Se non è possibile effettuare una connessione RDP, è invece possibile collegare il disco del sistema operativo a un'altra macchina virtuale di Azure e modificarlo mentre è collegato come disco dati eseguendo `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, quindi effettuando lo swapping del disco.
Incollare nei risultati SAC di PowerShell usando un terzo carattere se il contenuto originale aveva un carattere ripetuto. | Una soluzione alternativa consiste nell’eseguire `Remove-Module PSReadLine` per scaricare il modulo PSReadLine dalla sessione corrente. Questa azione non eliminerà o disinstallerà il modulo.
Alcuni input della tastiera producono output di configurazione SAC particolari (ad esempio, **[A**, **[3~**). | Le sequenze di escape [VT100](https://aka.ms/vtsequences) non sono supportate per il prompt SAC.
L'operazione di incollare le stringhe lunghe non funziona. | La console seriale limita la lunghezza delle stringhe incollate nel terminale a 2048 caratteri per impedire il sovraccarico della larghezza di banda della porta seriale.


## <a name="frequently-asked-questions"></a>Domande frequenti

**D. Come è possibile inviare commenti e suggerimenti?**

R. Fornire commenti e suggerimenti segnalando un problema GitHub in https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), è possibile inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di http://feedback.azure.com.

**D. La console seriale supporta le operazioni di copia e incolla?**

R. Sì. Usare **CTRL**+**MAIUSC**+**C** e **CTRL**+**MAIUSC**+**V** per copiare e incollare nel terminale.

**D. Chi può abilitare o disabilitare la console seriale per una sottoscrizione?**

R. Per abilitare o disabilitare la console seriale a livello di sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. I ruoli con autorizzazione di scrittura sono quelli di amministratore o proprietario. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

**D. Chi può accedere alla console seriale per la macchina virtuale?**

R. È necessario avere il ruolo di Collaboratore Macchina virtuale o un ruolo superiore per poter accedere alla console seriale della macchina virtuale.

**D. La console seriale non visualizza nulla, cosa bisogna fare?**

R. L'immagine probabilmente non è configurata correttamente per l'accesso alla console seriale. Per informazioni sulla configurazione dell'immagine per abilitare la console seriale, vedere [Abilitare la console seriale nelle immagini personalizzate o precedenti](#enable-the-serial-console-in-custom-or-older-images).

**D. La console seriale è disponibile per i set di scalabilità di macchine virtuali?**

R. L'accesso alla console seriale per le istanze del set di scalabilità di macchine virtuali non è attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi
* Per una guida approfondita su comandi di PowerShell e CMD che è possibile usare in Windows SAC, vedere [Comandi Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
* Questa console seriale è disponibile anche per le macchine virtuali [Linux](serial-console-linux.md).
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md).
