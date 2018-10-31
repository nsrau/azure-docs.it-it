---
title: Console seriale per macchine virtuali di Azure | Microsoft Docs
description: Console seriale bidirezionale per macchine virtuali di Azure.
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
ms.date: 10/22/2018
ms.author: harijay
ms.openlocfilehash: facd9be037894932e516e8294e36b6b0e55374c8
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024418"
---
# <a name="virtual-machine-serial-console"></a>Console seriale per macchine virtuali


La console seriale per macchine virtuali di Azure fornisce l'accesso a una console basata su testo per le macchine virtuali Windows. Questa connessione seriale viene stabilita con la porta seriale COM1 della macchina virtuale e fornisce l'accesso alla macchina virtuale che è indipendente dallo stato del sistema operativo o della rete della macchina virtuale. L'accesso alla console seriale per una macchina virtuale attualmente può essere eseguito solo tramite il portale di Azure ed è consentito solo agli utenti che dispongono dell'accesso Collaboratore Macchina virtuale o di livello superiore alla VM. 

Per la documentazione della console seriale per macchine virtuali Linux [fare clic qui](serial-console-linux.md).

> [!NOTE] 
> Console seriale per macchine virtuali è disponibile a livello generale nelle aree globali di Azure. La console seriale per il momento non è disponibile nei cloud di Azure per enti pubblici o di Azure Cina.

 

## <a name="prerequisites"></a>Prerequisiti 

* È necessario usare il modello di distribuzione di Gestione risorse. Non sono supportate le distribuzioni classiche. 
* La macchina virtuale DEVE avere la [diagnostica di avvio](boot-diagnostics.md) abilitata 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* L'account che usa la console seriale deve avere il [ruolo Collaboratore](../../role-based-access-control/built-in-roles.md) per la VM e l'account di archiviazione della [diagnostica di avvio](boot-diagnostics.md). 
* La macchina virtuale su cui si sta cercando di accedere alla console seriale deve avere anche un account basato su password. È possibile crearne uno tramite la funzionalità di [reimpostazione della password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione dell'accesso alla macchina virtuale; vedere la schermata seguente.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Introduzione alla console seriale
La console seriale per le macchine virtuali è accessibile solo tramite il [portale di Azure](https://portal.azure.com). Di seguito sono riportati i passaggi per accedere alla console seriale per macchine virtuali tramite il portale.

  1. Aprire il portale di Azure
  2. Nel menu a sinistra selezionare Macchine virtuali.
  3. Fare clic sulla macchina virtuale nell'elenco. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.
  4. Scorrere verso il basso fino alla sezione Supporto e risoluzione dei problemi e fare clic sull'opzione "Console seriale". Verrà aperto un nuovo riquadro con la console seriale e verrà avviata la connessione.

## <a name="enable-serial-console-in-custom-or-older-images"></a>Abilitare la console seriale nelle immagini personalizzate o precedenti
Per le immagini di Windows Server più recenti in Azure la [console amministrativa speciale](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC, Special Administrative Console) è abilitata per impostazione predefinita. La console SAC è supportata nelle versioni server di Windows, ma non è disponibile nelle versioni client (ad esempio, Windows 10, Windows 8 o Windows 7). Per abilitare la console seriale per le macchine virtuali Windows create prima di febbraio 2018, seguire questa procedura: 

1. Connettersi alla macchina virtuale Windows tramite Desktop remoto
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Riavviare il sistema per abilitare la console SAC

![](/media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessario, la console SAC può anche essere abilitata offline:

1. Collegare alla macchina virtuale esistente il disco di Windows per cui si vuole configurare la console SAC come disco dati. 
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>Come capire se la console SAC è abilitata?

Se la [console SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) non è abilitata, nella console seriale non verrà visualizzato il prompt SAC. In alcuni casi, verranno visualizzate le informazioni di integrità della macchina virtuale e in altri casi sarà vuota. Se si usa un'immagine di Windows Server creata prima di febbraio 2018, la console SAC probabilmente non verrà abilitata.

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>Abilitare il menu di avvio di Windows nella console seriale 

Se è necessario abilitare i prompt del caricatore di avvio di Windows da visualizzare nella console seriale, è possibile aggiungere le opzioni seguenti ai dati di configurazione di avvio. Vedere [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) per altri dettagli

1. Connettersi alla macchina virtuale Windows tramite Desktop remoto
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 10`
* `bcdedit /set {bootmgr} bootems yes`
3. Riavviare il sistema per abilitare il menu di avvio

> [!NOTE] 
> Il timeout impostato per la visualizzazione del menu di gestione dell'avvio influirà sul tempo di avvio del sistema operativo in futuro. Anche se per alcuni utenti può essere accettabile aggiungere un timeout di 10 secondi per garantire la visualizzazione dello strumento di gestione dell'avvio tramite la console seriale, per altri utenti può essere necessario un timeout più lungo o più breve. Impostare per il timeout un valore che si ritiene adeguato.

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Usare la console seriale per le chiamate NMI nelle macchine virtuali Windows
Un interrupt non mascherabile (NMI) è progettato per creare un segnale che il software in una macchina virtuale non ignorerà. In passato, gli NMI sono stati usati per verificare la presenza di problemi hardware in sistemi che necessitavano di tempi di risposta specifici.  Oggi, gli amministratori di sistema e i programmatori usano spesso gli NMI come un meccanismo per eseguire il debug o risolvere i problemi di sistemi che sono bloccati.

La console seriale può essere usata per inviare un interrupt non mascherabile (NMI) a una macchina virtuale di Azure usando l'icona della tastiera sulla barra dei comandi, mostrata di seguito. Dopo che l'interrupt non mascherabile viene recapitato, la configurazione della macchina virtuale potrà controllare la modalità di risposta del sistema. Windows può essere configurato per l'arresto anomalo del sistema e per la creazione di un dump di arresto anomalo del sistema di memoria quando si riceve un NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Per informazioni sulla configurazione di Windows per creare un dump di arresto anomalo del sistema quando riceve un NMI, vedere: [come generare un file di dump di arresto anomalo del sistema completo o un file di dump di arresto anomalo del sistema del kernel usando un NMI su un sistema basato su Windows](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="open-cmd-or-powershell-in-serial-console"></a>Aprire CMD o Powershell nella console seriale

1. Connettersi alla console seriale. Se ci si connette correttamente alla console seriale, verrà visualizzato il prompt **SAC>**, come illustrato nello screenshot seguente:

    ![Connettersi a SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

3.  Digitare `cmd` per creare un canale con un'istanza di CMD. 
4.  Digitare `ch -si 1` per passare al canale che esegue l'istanza di CMD. 
5.  Premere INVIO e quindi immettere le credenziali di accesso con autorizzazioni amministrative.
6.  Dopo avere immesso credenziali valide, verrà aperta l'istanza di CMD.
7.  Per avviare un'istanza di PowerShell, digitare `PowerShell` nell'istanza di CMD e quindi premere INVIO. 

    ![Aprire l'istanza di PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)


## <a name="disable-serial-console"></a>Disabilitare la console seriale
Per impostazione predefinita, tutte le sottoscrizioni hanno accesso alla console seriale in tutte le macchine virtuali. È possibile disabilitare la console seriale a livello di sottoscrizione o a livello di macchina virtuale.

> [!NOTE]       
> Per abilitare o disabilitare la console seriale per una sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. Ciò include i ruoli di amministratore o proprietario, ma non è limitato a questi ultimi. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

### <a name="subscription-level-disable"></a>Disattivazione a livello di sottoscrizione
La console seriale può essere disattivata per un'intera sottoscrizione tramite la [chiamata all'API REST di Disabilita console](https://aka.ms/disableserialconsoleapi). È possibile usare la funzionalità "Try It" disponibile nella pagina della documentazione API per disabilitare e abilitare la console seriale per una sottoscrizione. Inserire `subscriptionId`, "predefinito" nel `default` campo e fare clic su Esegui. I comandi dell'interfaccia della riga di comando di Azure non sono ancora disponibili e verranno visualizzati in un secondo momento. [Ripetere la chiamata all'API REST qui](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

In alternativa, è possibile usare il set di comandi seguente in Cloud Shell (comandi Bash illustrati) per disabilitare, abilitare e visualizzare lo stato disabilitato della console seriale per una sottoscrizione. 

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
Anche se non vengono registrate password di accesso per la console, se i comandi eseguiti nella console contengono o visualizzano password, segreti, nomi utente o qualsiasi altra forma di informazioni personali, tali elementi verranno scritti nei log di diagnostica di avvio della macchina virtuale, con qualsiasi altro testo visibile, durante l'implementazione della funzionalità di scorrimento all'indietro della console seriale. Questi log sono circolari e solo gli utenti con autorizzazioni di lettura per l'account di archiviazione di diagnostica possono accedervi, ma è preferibile seguire la procedura consigliata che prevede l'uso di Desktop remoto per tutto ciò che può implicare segreti e/o informazioni personali. 

### <a name="concurrent-usage"></a>Utilizzo simultaneo
Se un utente è connesso alla console seriale e un altro utente ottiene l'accesso alla stessa macchina virtuale, il primo utente verrà disconnesso, mentre il secondo verrà connesso, come se il primo utente si alzasse e lasciasse la console fisica e un nuovo utente ne prendesse il posto.

>[!CAUTION] 
Questo significa che la sessione dell'utente che viene disconnesso non verrà chiusa. Non è ancora possibile imporre la chiusura della sessione in caso di disconnessione (tramite SIGHUP o un meccanismo simile). Nella console SAC è abilitato un timeout automatico per Windows, mentre per Linux è possibile configurare l'impostazione di timeout del terminale. 

## <a name="common-scenarios-for-accessing-serial-console"></a>Scenari comuni per l'accesso alla console seriale 
Scenario          | Azioni nella console seriale                
:------------------|:-----------------------------------------
Regole del firewall non corrette | Accedere alla console seriale e correggere le regole di Windows Firewall. 
Danneggiamento/Controllo del file system | Accedere alla console seriale e recuperare il file system. 
Problemi di configurazione RDP | Accedere alla console seriale e modificare le impostazioni. Andare alla [documentazione su RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Sistema di blocco della rete| Accedere alla console seriale tramite il portale per gestire il sistema. Alcuni comandi di rete sono elencati nella [documentazione sulla console seriale CMD e PowerShell](serial-console-cmd-ps-commands.md). 
Interazione con bootloader | Accedere a BCD tramite la console seriale. Andare a [Abilitazione del menu di avvio da visualizzare nella console seriale](#enabling-boot-menu-to-show-in-the-serial-console) per iniziare. 

## <a name="accessibility"></a>Accessibilità
L'accessibilità è un obiettivo chiave della console seriale di Azure. A tale scopo, è stato garantito che la console seriale sia accessibile a utenti con problemi di vista e di udito, nonché a utenti che potrebbero non essere in grado di usare il mouse.

### <a name="keyboard-navigation"></a>Navigazione da tastiera
Usare il tasto `tab` sulla tastiera per eseguire la navigazione nell'interfaccia della console seriale nel portale di Azure. La posizione verrà evidenziata sullo schermo. Per lasciare lo stato attivo del pannello della console seriale, premere `Ctrl + F6` sulla tastiera.

### <a name="use-serial-console-with-a-screen-reader"></a>Usare la console seriale con un'utilità per la lettura dello schermo
La console seriale viene fornita con il supporto dell'utilità per la lettura dello schermo incorporato. La navigazione con un'utilità per la lettura dello schermo attivata consentirà la lettura a voce alta del testo alternativo del pulsante attualmente selezionato da parte dell'utilità per la lettura dello schermo.

## <a name="errors"></a>Errors
La maggior parte degli errori è di natura temporanea e, per risolverli, è sufficiente stabilire di nuovo la connessione. La tabella seguente contiene un elenco di errori e il modo per prevenirli

Tipi di errore                            |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica per "<VMNAME>". Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata. 
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale
Non si hanno le autorizzazioni necessarie per usare questa console seriale per la VM. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio "<STORAGEACCOUNTNAME>". Verificare che la diagnostica di avvio sia abilitata per questa macchina virtuale e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario usare l'elenco elementi consentiti `*.console.azure.com`. Un approccio più dettagliato, ma a lungo termine è l'uso di un elenco elementi consentiti per gli [intervalli IP dei data center di Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), che cambiano piuttosto regolarmente.
Durante la connessione a una macchina virtuale Windows, vengono visualizzate solo informazioni sull'integrità| Questi errori vengono visualizzati se la Special Administration Console non è stata abilitata per l'immagine di Windows. Visualizzare [Console di accesso seriale per Windows](#access-serial-console-for-windows) per istruzioni su come attivare manualmente la Special Administration Console (SAC) nella macchina virtuale di Windows. Per altri informazioni, vedere [Segnali di integrità Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Problemi noti 
La console seriale presenta alcuni problemi. Di seguito è riportato un elenco dei problemi riscontrati e delle procedure necessarie per ridurne l'impatto.

Problema                             |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Premendo INVIO dopo il banner della connessione, non viene visualizzato un prompt di accesso | Vedere questa pagina: [Premendo INVIO non succede niente](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Questo può verificarsi se è in esecuzione una macchina virtuale personalizzata, un'appliance con protezione avanzata o una configurazione di avvio che non consente a Windows di connettersi correttamente alla porta seriale. Questo si verifica anche se si esegue una macchina virtuale client Windows 10, perché solo le macchine virtuali Windows Server sono configurate per l'abilitazione di EMS.
Non è possibile digitare nel prompt SAC se è abilitato il debug del kernel | Effettuare una connessione RDP ed eseguire `bcdedit /debug {current} off` da un prompt dei comandi con privilegi elevati. Se non è possibile effettuare una connessione RDP, è invece possibile collegare il disco del sistema operativo a un'altra macchina virtuale di Azure e modificarlo mentre è collegato come disco dati usando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, quindi effettuare lo swapping del disco.
Incollare nei risultati SAC di PowerShell usando un terzo carattere se il contenuto originale aveva un carattere ripetuto | Una soluzione alternativa consiste nello scaricare il modulo PSReadLine dalla sessione corrente. Eseguire `Remove-Module PSReadLine` per scaricare il modulo PSReadLine dalla sessione corrente. Questo non eliminerà o disinstallerà il modulo.
Alcuni input della tastiera producono output di configurazione SAC particolari (es `[A`, `[3~`) | Le sequenze di escape [VT100](https://aka.ms/vtsequences) non sono supportate per il prompt SAC.
Incollare stringhe molto lunghe non funziona | La console seriale limita la lunghezza delle stringhe incollate nel terminale a 2048 caratteri. Questo evita di sovraccaricare la larghezza di banda della porta seriale.

## <a name="frequently-asked-questions"></a>Domande frequenti 

**D. Come è possibile inviare commenti e suggerimenti?**

R. Per inviare commenti e suggerimenti, andare a https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), è possibile inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di http://feedback.azure.com

**D. La console seriale supporta le operazioni di copia e incolla?**

R. Sì. Usare CTRL + MAIUSC + C e CTRL + MAIUSC + V per copiare e incollare nel terminale.

**D. Chi può abilitare o disabilitare la console seriale per una sottoscrizione?**

R. Per abilitare o disabilitare la console seriale a livello di sottoscrizione, è necessario avere le autorizzazioni di scrittura per la sottoscrizione. I ruoli con autorizzazione di scrittura sono quelli di amministratore o proprietario, ma anche altri. Anche i ruoli personalizzati possono avere le autorizzazioni di scrittura.

**D. Chi può accedere alla console seriale per la VM?**

R. Per poter accedere alla console seriale della VM, è necessario disporre di un accesso di livello collaboratore o superiore a una VM. 

**D. La console seriale non visualizza nulla, cosa bisogna fare?**

R. L'immagine probabilmente non è configurata correttamente per l'accesso alla console seriale. Vedere [Abilitare la console seriale per immagini personalizzate o meno recenti](#enable-serial-console-in-custom-or-older-images) per informazioni dettagliate sulla configurazione dell'immagine per abilitare la console seriale.

**D. La console seriale è disponibile per i set di scalabilità di macchine virtuali?**

R. L'accesso alla console seriale per le istanze del set di scalabilità di macchine virtuali non è attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi
* Per una guida approfondita su comandi di PowerShell e CMD che è possibile usare in Windows SAC, fare clic [qui](serial-console-cmd-ps-commands.md).
* Questa console seriale è disponibile anche per le VM di [Linux](serial-console-linux.md).
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md).