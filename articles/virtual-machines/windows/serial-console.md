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
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: 66354db65d5e615780ec49683fbc72f1156ac5e1
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2018
ms.locfileid: "42142458"
---
# <a name="virtual-machine-serial-console-preview"></a>Console seriale per macchine virtuali (anteprima) 


La console seriale per macchine virtuali di Azure fornisce l'accesso a una console basata su testo per macchine virtuali Linux e Windows. Questa connessione seriale viene stabilita con la porta seriale COM1 della macchina virtuale, fornisce l'accesso alla macchina virtuale e non è correlata allo stato del sistema operativo o della rete della macchina virtuale. L'accesso alla console seriale per una macchina virtuale attualmente può essere eseguito solo tramite il portale di Azure ed è consentito solo agli utenti che hanno l'accesso Collaboratore macchine virtuali o superiore alla VM. 

Per la documentazione della console seriale per macchine virtuali Linux [fare clic qui](../linux/serial-console.md).

> [!Note] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure]. (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Questo servizio è attualmente in **anteprima pubblica** e l'accesso alla console seriale per le macchine virtuali è disponibile per le aree di Azure globali. La console seriale per il momento non è disponibile per il cloud Azure per enti pubblici, Azure Germania e Azure Cina.

 

## <a name="prerequisites"></a>Prerequisiti 

* È necessario usare il modello di distribuzione di Gestione risorse. Non sono supportate le distribuzioni classiche. 
* La macchina virtuale DEVE avere la [diagnostica di avvio](boot-diagnostics.md) abilitata 
* L'account che usa la console seriale deve avere il [ruolo Collaboratore](../../role-based-access-control/built-in-roles.md) per la VM e l'account di archiviazione della [diagnostica di avvio](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Aprire la console seriale
La console seriale per le macchine virtuali è accessibile solo tramite il [portale di Azure](https://portal.azure.com). Di seguito sono riportati i passaggi per accedere alla console seriale per macchine virtuali tramite il portale 

  1. Aprire il portale di Azure
  2. Nel menu a sinistra selezionare Macchine virtuali.
  3. Fare clic sulla macchina virtuale nell'elenco. Viene visualizzata la pagina Panoramica relativa alla VM.
  4. Scorrere verso il basso fino alla sezione Supporto e risoluzione dei problemi e fare clic sull'opzione Console seriale (anteprima). Verrà aperto un nuovo riquadro con la console seriale e verrà avviata la connessione.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

## <a name="disable-serial-console"></a>Disabilitare la console seriale
Per impostazione predefinita, tutte le sottoscrizioni hanno accesso alla console seriale in tutte le macchine virtuali. È possibile disabilitare la console seriale a livello di sottoscrizione o a livello di macchina virtuale.

### <a name="subscription-level-disable"></a>Disattivazione a livello di sottoscrizione
La console seriale può essere disattivata per un'intera sottoscrizione tramite la [chiamata all'API REST di Disabilita console](https://aka.ms/disableserialconsoleapi). È possibile usare la funzionalità "Try It" disponibile nella pagina della documentazione API per disabilitare e abilitare la console seriale per una sottoscrizione. Inserire `subscriptionId`, "predefinito" nel `default` campo e fare clic su Esegui. I comandi dell'interfaccia della riga di comando di Azure non sono ancora disponibili e verranno visualizzati in un secondo momento. [Ripetere la chiamata all'API REST qui](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

In alternativa, è possibile usare il set di comandi in Cloud Shell seguente (comandi bash mostrati) per disabilitare, abilitare e visualizzare lo stato disabilitato della console seriale per una sottoscrizione. 

* Per ottenere lo stato disabilitato della console seriale per una sottoscrizione:
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Per disabilitare la console seriale per una sottoscrizione:
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Per abilitare la console seriale per una sottoscrizione:
    ```
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


## <a name="access-serial-console-for-windows"></a>Accesso alla console seriale per Windows 
Per le immagini di Windows Server più recenti in Azure la [console amministrativa speciale](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC, Special Administrative Console) è abilitata per impostazione predefinita. La console SAC è supportata nelle versioni server di Windows, ma non è disponibile nelle versioni client (ad esempio, Windows 10, Windows 8 o Windows 7). Per abilitare la console seriale per le macchine virtuali Windows create usando immagini di febbraio 2018 o precedenti, seguire questa procedura: 

1. Connettersi alla macchina virtuale Windows tramite Desktop remoto
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Riavviare il sistema per abilitare la console SAC

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessario, la console SAC può essere abilitata anche offline: 

1. Collegare alla macchina virtuale esistente il disco di Windows per cui si vuole configurare la console SAC come disco dati. 
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Come capire se la console SAC è abilitata 

Se la console [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) non è abilitata, la console seriale non visualizzerà il prompt SAC. In alcuni casi è possibile che vengano visualizzate informazioni sull'integrità della VM oppure nessuna informazione.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Abilitazione del menu di avvio da visualizzare nella console seriale 

Se è necessario abilitare i prompt del caricatore di avvio di Windows da visualizzare nella console seriale, è possibile aggiungere le opzioni seguenti al caricatore di avvio di Windows.

1. Connettersi alla macchina virtuale Windows tramite Desktop remoto
2. Da un prompt dei comandi amministrativo eseguire i comandi seguenti 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Riavviare il sistema per abilitare il menu di avvio

> [!NOTE] 
> A questo punto, il supporto per i tasti funzione non è abilitato. Se sono necessarie opzioni di avvio avanzate, usare bcdedit /set {current} onetimeadvancedoptions on. Per altre informazioni dettagliate, vedere [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)

## <a name="using-serial-console-for-nmi-calls-in-windows-vms"></a>Usando la console seriale per le chiamate NMI nelle macchine virtuali Windows
Un interrupt non mascherabile (NMI) è progettato per creare un segnale che il software in una macchina virtuale non ignorerà. In passato, gli NMI sono stati usati per verificare la presenza di problemi hardware in sistemi che necessitavano di tempi di risposta specifici.  Oggi, gli amministratori di sistema e i programmatori usano spesso gli NMI come un meccanismo per eseguire il debug o risolvere i problemi di sistemi che sono bloccati.

La console seriale può essere usata per inviare un NMI a una macchina virtuale di Azure usando l'icona della tastiera nella barra dei comandi, mostrata di seguito. Dopo che l'interrupt non mascherabile viene recapitato, la configurazione della macchina virtuale potrà controllare la modalità di risposta del sistema. Windows può essere configurato per l'arresto anomalo del sistema e per la creazione di un dump di arresto anomalo del sistema di memoria quando si riceve un NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Per informazioni sulla configurazione di Windows per creare un dump di arresto anomalo del sistema quando riceve un NMI, vedere: [come generare un file di dump di arresto anomalo del sistema completo o un file di dump di arresto anomalo del sistema del kernel usando un NMI su un sistema basato su Windows](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)


## <a name="errors"></a>Errors
La maggior parte degli errori è di natura temporanea e, per risolverli, è sufficiente stabilire di nuovo la connessione. La tabella seguente contiene un elenco di errori e il modo per prevenirli 

Tipi di errore                            |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica per "<VMNAME>". Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata. 
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale
Non si hanno le autorizzazioni necessarie per usare questa console seriale per la VM. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio "<STORAGEACCOUNTNAME>". Verificare che la diagnostica di avvio sia abilitata per questa macchina virtuale e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario usare l'elenco elementi consentiti `*.console.azure.com`. Un approccio più dettagliato, ma a lungo termine è l'uso di un elenco elementi consentiti per gli [intervalli IP dei datacenter di Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), che cambiano piuttosto regolarmente.

## <a name="known-issues"></a>Problemi noti 
Poiché l'accesso alla console seriale è ancora in fase di anteprima, è in corso la soluzione di alcuni problemi noti. Di seguito è riportato un elenco di questi problemi con le possibili soluzioni alternative 

Problema                             |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Non sono disponibili opzioni per la console seriale dell'istanza del set di scalabilità di macchine virtuali | In fase di anteprima l'accesso alla console seriale per le istanze del set di scalabilità di macchine virtuali non è supportato.
Premendo INVIO dopo il banner della connessione, non viene visualizzato un prompt di accesso | [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Durante la connessione a una macchina virtuale Windows, vengono visualizzate solo informazioni sull'integrità| [Segnali di integrità di Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Non è possibile digitare nel prompt SAC se è abilitato il debug del kernel | Effettuare una connessione RDP ed eseguire `bcdedit /debug {current} off` da un prompt dei comandi con privilegi elevati. Se non è possibile effettuare una connessione RDP, è invece possibile collegare il disco del sistema operativo a un'altra macchina virtuale di Azure e modificarlo mentre è collegato come disco dati usando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, quindi effettuare lo swapping del disco.
Incollare nei risultati SAC di PowerShell usando un terzo carattere se il contenuto originale aveva un carattere ripetuto | Una soluzione alternativa consiste nel rimuovere il modulo PSReadLine. `Remove-Module PSReadLine` rimuoverà il modulo PSReadLine dalla sessione corrente.
Alcuni input della tastiera producono output di configurazione SAC particolari (es `[A`, `[3~`) | Le sequenze di escape [VT100](https://aka.ms/vtsequences) non sono supportate per il prompt SAC.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.

## <a name="frequently-asked-questions"></a>Domande frequenti 
**D. Come è possibile inviare commenti e suggerimenti?**

R. Per inviare commenti e suggerimenti, andare a https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di http://feedback.azure.com

**D. Impossibile accedere alla console seriale. Dove è possibile inserire un caso di supporto?**

R. Questa funzionalità di anteprima è soggetta alle condizioni per l'anteprima di Azure. Per ottenere supporto, è consigliabile usare i canali indicati in precedenza. 

## <a name="next-steps"></a>Passaggi successivi
* Per una guida approfondita su comandi di PowerShell e CMD che è possibile usare in Windows SAC, fare clic [qui](serial-console-cmd-ps-commands.md).
* Questa console seriale è disponibile anche per le VM di [Linux](../linux/serial-console.md).
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md).
