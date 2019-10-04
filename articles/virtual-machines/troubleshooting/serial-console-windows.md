---
title: Console seriale di Azure per Windows | Microsoft Docs
description: Console seriale bidirezionale per macchine virtuali di Azure e set di scalabilità di macchine virtuali.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: ebf7b712dda19b396b044235bf194a5dd402ffac
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996426"
---
# <a name="azure-serial-console-for-windows"></a>Console seriale di Azure per Windows

La console seriale nel portale di Azure fornisce l'accesso a una console basata su testo per le macchine virtuali Windows e le istanze del set di scalabilità di macchine virtuali. Questa connessione seriale si connette alla porta seriale COM1 della VM o dell'istanza del set di scalabilità di macchine virtuali, garantendo l'accesso indipendentemente dalla rete o dallo stato del sistema operativo. È possibile accedere alla console seriale solo usando la portale di Azure ed è consentita solo per gli utenti che hanno un ruolo accesso di collaboratore o superiore alla macchina virtuale o al set di scalabilità di macchine virtuali.

La console seriale funziona allo stesso modo per le macchine virtuali e le istanze del set di scalabilità di macchine virtuali. In questo documento, tutte le menzioni delle macchine virtuali includeranno in modo implicito le istanze del set di scalabilità di macchine virtuali, se non diversamente specificato.

Per la documentazione sulla console seriale per Linux, vedere la pagina relativa alla [console seriale di Azure per Linux](serial-console-linux.md).

> [!NOTE]
> La console seriale è disponibile a livello generale nelle aree globali di Azure. Non è al momento disponibile nei cloud di Azure per enti pubblici o di Azure Cina.


## <a name="prerequisites"></a>Prerequisiti

* L'istanza della VM o del set di scalabilità di macchine virtuali deve usare il modello di distribuzione di gestione delle risorse. Le distribuzioni classiche non sono supportate.

- L'account che usa la console seriale deve avere il [ruolo Collaboratore macchina virtuale per la macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e l'account di archiviazione di [diagnostica di avvio](boot-diagnostics.md)

- L'istanza della VM o del set di scalabilità di macchine virtuali deve avere un utente basato su password. È possibile crearne uno con la funzione di [reimpostazione della password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**.

* Per la VM per l'istanza del set di scalabilità di macchine virtuali è necessario abilitare la [diagnostica di avvio](boot-diagnostics.md) .

    ![Impostazioni di diagnostica di avvio](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Abilitare la funzionalità della console seriale per Windows Server

> [!NOTE]
> Se non viene visualizzato alcun elemento nella console seriale, assicurarsi che la diagnostica di avvio sia abilitata nella VM o nel set di scalabilità di macchine virtuali.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Abilitare la console seriale nelle immagini personalizzate o precedenti
Per le immagini di Windows Server più recenti in Azure è abilitata la [console di amministrazione speciale](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) per impostazione predefinita. La console SAC è supportata nelle versioni server di Windows, ma non è disponibile nelle versioni client (ad esempio, Windows 10, Windows 8 o Windows 7).

Per le immagini di Windows Server precedenti, ovvero create prima di febbraio 2018, è possibile abilitare automaticamente la console seriale tramite la funzionalità dei comandi di esecuzione nel portale di Azure. Nella portale di Azure selezionare **Esegui comando**, quindi selezionare il comando denominato **EnableEMS** dall'elenco.

![Eseguire l'elenco comandi](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

In alternativa, per abilitare manualmente la console seriale per le macchine virtuali Windows o il set di scalabilità di macchine virtuali creato prima del 2018 febbraio, attenersi alla procedura seguente:

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

1. Connettersi alla VM Windows o all'istanza del set di scalabilità di macchine virtuali usando Desktop remoto.

1. Da un prompt dei comandi amministrativo eseguire i comandi seguenti:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Riavviare il sistema per abilitare il menu di avvio

> [!NOTE]
> Il timeout impostato per la visualizzazione del menu di gestione dell'avvio influisce sul tempo di avvio del sistema operativo. Se si ritiene che il valore di timeout di 10 secondi sia troppo lungo o troppo corto, impostarlo su un valore diverso.

## <a name="use-serial-console"></a>Usare la console seriale

### <a name="use-cmd-or-powershell-in-serial-console"></a>Usare CMD o PowerShell nella console seriale

1. Connettersi alla console seriale. Se ci si connette correttamente, la richiesta è **SAC >** :

    ![Connettersi a SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Digitare `cmd` per creare un canale con un'istanza di CMD.

1.  Immettere `ch -si 1` o premere `<esc>+<tab>` i tasti di scelta rapida per passare al canale che esegue l'istanza di cmd.

1.  Premere **Invio**, quindi immettere le credenziali di accesso con autorizzazioni amministrative.

1.  Dopo avere immesso credenziali valide, verrà aperta l'istanza di CMD.

1.  Per avviare un'istanza di PowerShell, inserire `PowerShell` nell'istanza di CMD e quindi premere **Invio**.

    ![Aprire l'istanza di PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Usare la console seriale per le chiamate NMI
Un interrupt non mascherabile (NMI) è progettato per creare un segnale che il software in una macchina virtuale non ignora. In passato, gli NMI sono stati usati per verificare la presenza di problemi hardware in sistemi che necessitavano di tempi di risposta specifici. Attualmente, i programmatori e gli amministratori di sistema spesso utilizzano NMI come meccanismo per il debug o la risoluzione dei problemi di sistemi che non rispondono.

La console seriale può essere usata per inviare un interrupt non mascherabile (NMI) a una macchina virtuale di Azure usando l'icona della tastiera sulla barra dei comandi. Dopo che l'interrupt non mascherabile viene recapitato, la configurazione della macchina virtuale potrà controllare la modalità di risposta del sistema. Windows può essere configurato per l'arresto anomalo del sistema e per la creazione di un file dump di arresto anomalo del sistema di memoria quando si riceve un NMI.

![Inviare NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Per informazioni sulla configurazione di Windows per creare un file dump di arresto anomalo del sistema quando riceve un NMI, vedere: [Come generare un file di dump di arresto anomalo del sistema usando un NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Usare le chiavi di funzione nella console seriale
Le chiavi di funzione sono abilitate per essere usate per la console seriale nelle macchine virtuali di Windows. L'opzione F8 nell'elenco a discesa della console seriale consente di accedere facilmente al menu delle impostazioni di avvio avanzate, ma la console seriale è compatibile con tutte le altre chiavi di funzione. Potrebbe essere necessario premere **FN** + **F1** (o F2, F3 e così via) sulla tastiera a seconda del computer in cui si usa la console seriale.

### <a name="use-wsl-in-serial-console"></a>Usare WSL nella console seriale
Windows Subsystem for Linux (WSL) è stato abilitato per Windows Server 2019 e versioni successive ed è quindi possibile abilitare WSL per l'utilizzo nella console seriale se si esegue Windows Server 2019 o versione successiva. Questa possibilità potrebbe essere particolarmente utile per gli utenti che hanno familiarità anche con i comandi Linux. Per istruzioni su come abilitare WSL per Windows Server, vedere la [Guida all'installazione](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Riavviare la VM Windows o l'istanza del set di scalabilità di macchine virtuali nella console seriale
È possibile avviare un riavvio nella console seriale passando al pulsante di alimentazione e facendo clic su "Riavvia macchina virtuale". La macchina virtuale verrà riavviata e nel portale di Azure verrà visualizzata la notifica del riavvio.

Questa funzionalità è utile nelle situazioni in cui è possibile accedere al menu di avvio senza uscire dall'esperienza della console seriale.

![Riavvio della console seriale Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Disabilitare la console seriale
Per impostazione predefinita, per tutte le sottoscrizioni è abilitato l'accesso alla console seriale. È possibile disabilitare la console seriale a livello di sottoscrizione o di VM/set di scalabilità di macchine virtuali. Per istruzioni dettagliate, vedere [abilitare e disabilitare la console seriale di Azure](./serial-console-enable-disable.md).

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
> Questo significa che la sessione dell'utente che viene disconnesso non verrà chiusa. Non è ancora possibile imporre la chiusura della sessione in caso di disconnessione (tramite SIGHUP o un meccanismo simile). Nella console SAC è abilitato un timeout automatico per Windows; per Linux è possibile configurare l'impostazione di timeout del terminale.

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

## <a name="known-issues"></a>Problemi noti
La console seriale presenta alcuni problemi. Di seguito è riportato un elenco dei problemi riscontrati e delle procedure necessarie per risolverli. Questi problemi e mitigazioni si applicano sia alle macchine virtuali che alle istanze dei set di scalabilità di macchine virtuali.

Problema                             |   Attenuazione
:---------------------------------|:--------------------------------------------|
Se si preme il tasto **INVIO** dopo il banner della connessione, non viene visualizzato un prompt di accesso. | Per altre informazioni, vedere [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Questo errore può verificarsi se è in esecuzione una macchina virtuale personalizzata, un'appliance con protezione avanzata o una configurazione di avvio che non consente a Windows di connettersi correttamente alla porta seriale. Questo errore si verifica anche se si esegue una macchina virtuale Windows 10, perché solo le macchine virtuali Windows Server sono configurate per abilitare EMS.
Durante la connessione a una macchina virtuale Windows, vengono visualizzate solo informazioni sull'integrità| Questo errore si verifica se la console di amministrazione speciale non è stata abilitata per l'immagine di Windows. Visualizzare [Abilitare la console seriale nelle immagini personalizzate o precedenti](#enable-the-serial-console-in-custom-or-older-images) per istruzioni su come attivare manualmente la Special Administration Console (SAC) nella macchina virtuale di Windows. Per altre informazioni, vedere [Segnali di integrità di Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Non è possibile digitare nel prompt SAC se è abilitato il debug del kernel. | Effettuare una connessione RDP ed eseguire `bcdedit /debug {current} off` da un prompt dei comandi con privilegi elevati. Se non è possibile effettuare una connessione RDP, è invece possibile collegare il disco del sistema operativo a un'altra macchina virtuale di Azure e modificarlo mentre è collegato come disco dati eseguendo `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, quindi effettuando lo swapping del disco.
Incollare nei risultati SAC di PowerShell usando un terzo carattere se il contenuto originale aveva un carattere ripetuto. | Una soluzione alternativa consiste nell’eseguire `Remove-Module PSReadLine` per scaricare il modulo PSReadLine dalla sessione corrente. Questa azione non eliminerà o disinstallerà il modulo.
Alcuni input della tastiera producono output di configurazione SAC particolari (ad esempio, **[A**, **[3~** ). | Le sequenze di escape [VT100](https://aka.ms/vtsequences) non sono supportate per il prompt SAC.
L'operazione di incollare le stringhe lunghe non funziona. | La console seriale limita la lunghezza delle stringhe incollate nel terminale a 2048 caratteri per impedire il sovraccarico della larghezza di banda della porta seriale.
Console seriale non funziona con un account di archiviazione usando Azure Data Lake Storage Gen2 con spazi dei nomi gerarchici. | Si tratta di un problema noto con gli spazi dei nomi gerarchici. Per attenuare, verificare che l'account di archiviazione della diagnostica di avvio della macchina virtuale non venga creato con Azure Data Lake Storage Gen2. Questa opzione può essere impostata solo al momento della creazione dell'account di archiviazione. Potrebbe essere necessario creare un account di archiviazione di diagnostica di avvio separato senza Azure Data Lake Storage Gen2 abilitato per attenuare questo problema.


## <a name="frequently-asked-questions"></a>Domande frequenti

**D. Come è possibile inviare commenti e suggerimenti?**

R. Fornire commenti e suggerimenti segnalando un problema GitHub in https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), è possibile inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di https://feedback.azure.com.

**D. La console seriale supporta le operazioni di copia e incolla?**

R. Sì. Usare **CTRL**+**MAIUSC**+**C** e **CTRL**+**MAIUSC**+**V** per copiare e incollare nel terminale.

**D. Chi può abilitare o disabilitare la console seriale per una sottoscrizione?**

R. Per abilitare o disabilitare la console seriale a livello di sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. I ruoli con autorizzazione di scrittura sono quelli di amministratore o proprietario. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

**D. Chi può accedere alla console seriale per la macchina virtuale?**

R. È necessario avere il ruolo di Collaboratore Macchina virtuale o un ruolo superiore per poter accedere alla console seriale della macchina virtuale.

**D. La console seriale non visualizza nulla, cosa bisogna fare?**

R. L'immagine probabilmente non è configurata correttamente per l'accesso alla console seriale. Per informazioni sulla configurazione dell'immagine per abilitare la console seriale, vedere [Abilitare la console seriale nelle immagini personalizzate o precedenti](#enable-the-serial-console-in-custom-or-older-images).

**D. La console seriale è disponibile per i set di scalabilità di macchine virtuali?**

R. Sì lo è! Vedere [console seriale per i set di scalabilità di macchine virtuali](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Passaggi successivi
* Per una guida approfondita su comandi di PowerShell e CMD che è possibile usare in Windows SAC, vedere [Comandi Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
* Questa console seriale è disponibile anche per le macchine virtuali [Linux](serial-console-linux.md).
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md).
