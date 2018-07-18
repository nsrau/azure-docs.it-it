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
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 00a776131321500386b507f45ea84817b08147f7
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867862"
---
# <a name="virtual-machine-serial-console-preview"></a>Console seriale per macchine virtuali (anteprima) 


La console seriale per macchine virtuali in Azure fornisce l'accesso a una console basata su testo per macchine virtuali Linux e Windows. Questa connessione seriale viene stabilita con la porta seriale COM1 della macchina virtuale, fornisce l'accesso alla macchina virtuale e non è correlata allo stato del sistema operativo o della rete della macchina virtuale. L'accesso alla console seriale per una macchina virtuale attualmente può essere eseguito solo tramite il portale di Azure ed è consentito solo agli utenti che hanno l'accesso Collaboratore macchine virtuali o superiore alla VM. 

> [!Note] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure]. (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Questo servizio è attualmente in **anteprima pubblica** e l'accesso alla console seriale per le macchine virtuali è disponibile per le aree di Azure globali. La console seriale per il momento non è disponibile per il cloud Azure per enti pubblici, Azure Germania e Azure Cina.


## <a name="prerequisites"></a>prerequisiti 

* È necessario usare il modello di distribuzione di Gestione risorse. Non sono supportate le distribuzioni classiche. 
* La macchina virtuale DEVE avere la [diagnostica di avvio](boot-diagnostics.md) abilitata 
* L'account che usa la console seriale deve avere il [ruolo Collaboratore](../../role-based-access-control/built-in-roles.md) per la VM e l'account di archiviazione della [diagnostica di avvio](boot-diagnostics.md). 
* Per le impostazioni specifiche per le distribuzioni di Linux, vedere [Accessing the serial console for Linux](#accessing-serial-console-for-linux) (Accedere alla console seriale per Linux)


## <a name="open-the-serial-console"></a>Aprire la console seriale
La console seriale per le macchine virtuali è accessibile solo tramite il [portale di Azure](https://portal.azure.com). Di seguito sono riportati i passaggi per accedere alla console seriale per macchine virtuali tramite il portale 

  1. Aprire il portale di Azure
  2. Nel menu a sinistra selezionare Macchine virtuali.
  3. Fare clic sulla macchina virtuale nell'elenco. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.
  4. Scorrere verso il basso fino alla sezione Supporto e risoluzione dei problemi e fare clic sull'opzione Console seriale (anteprima). Verrà aperto un nuovo riquadro con la console seriale e verrà avviata la connessione.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> La console seriale richiede un utente locale configurato con una password. Le macchine virtuali configurate solo con la chiave pubblica SSH attualmente non hanno accesso alla console seriale. Per creare un utente locale con password, seguire la procedura per l'uso dell'[estensione VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension).

### <a name="disable-feature"></a>Disabilitare la funzionalità
È possibile disattivare la funzionalità della console seriale per macchine virtuali specifiche disabilitando l'impostazione della diagnostica di avvio per tali macchine virtuali.

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

### <a name="disable-feature"></a>Disabilitare la funzionalità
È possibile disattivare la funzionalità della console seriale per macchine virtuali specifiche disabilitando l'impostazione della diagnostica di avvio per tali macchine virtuali.

## <a name="common-scenarios-for-accessing-serial-console"></a>Scenari comuni per l'accesso alla console seriale 
Scenario          | Azioni nella console seriale                |  Applicabilità del sistema operativo 
:------------------|:-----------------------------------------|:------------------
File FSTAB danneggiato | Premere INVIO per continuare e correggere il file fstab usando un editor di testo. Vedere la pagina relativa alla [risoluzione dei problemi dei file fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Regole del firewall non corrette | Accedere alla console seriale e correggere le tabelle IP o le regole di Windows Firewall | Linux/Windows 
Danneggiamento/Controllo del file system | Accedere alla console seriale e recuperare il file system | Linux/Windows 
Problemi di configurazione SSH/RDP | Accedere alla console seriale e modificare le impostazioni | Linux/Windows 
Sistema di blocco della rete| Accedere alla console seriale tramite il portale per gestire il sistema | Linux/Windows 
Interazione con bootloader | Accedere a GRUB/BCD tramite la console seriale | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Accedere alla console seriale per Linux
Affinché la console seriale funzioni correttamente, il sistema operativo guest deve essere configurato per leggere e scrivere i messaggi della console nella porta seriale. La maggior parte delle [distribuzioni di Azure per Linux approvate](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ha la console seriale configurata per impostazione predefinita. Facendo clic nella sezione della console seriale nel portale sarà possibile accedere alla console. 

### <a name="access-for-red-hat"></a>Accesso per Red Hat 
Le immagini Red Hat disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. La modalità utente singolo in Red Hat richiede che sia abilitato l'utente ROOT, che è disabilitato per impostazione predefinita. Per abilitare la modalità utente singolo, seguire le istruzioni seguenti:

1. Accedere al sistema Red Hat tramite SSH
2. Abilitare la password per l'utente ROOT 
 * `passwd root` (impostare una password complessa per l'utente ROOT)
3. Verificare che l'utente ROOT possa eseguire l'accesso solo tramite ttyS0
 * `edit /etc/ssh/sshd_config` e assicurarsi che l'opzione PermitRootLog in sia impostata su no
 * `edit /etc/securetty file` per consentire l'accesso solo tramite ttyS0 

Se il sistema viene ora avviato in modalità utente singolo, è possibile accedervi tramite la password ROOT.

In alternativa per RHEL 7.4+ o 6.9+ è possibile abilitare la modalità utente singolo nei prompt GRUB, come illustrato nelle istruzioni riportate [in questa pagina](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Accesso per Ubuntu 
Le immagini Ubuntu disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. Se il sistema viene ora avviato in modalità utente singolo, è possibile accedervi senza credenziali aggiuntive. 

### <a name="access-for-coreos"></a>Accesso per CoreOS
Le immagini CoreOS disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. Se necessario, il sistema può essere avviato in modalità utente singolo tramite la modifica dei parametri GRUB. L'aggiunta di `coreos.autologin=ttyS0` consentirebbe all'utente core di accedere alla console seriale. 

### <a name="access-for-suse"></a>Accesso per SUSE
Le immagini SLES disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. Se si usano versioni precedenti di SLES in Azure, seguire le istruzioni riportate nell'[articolo della knowledge base](https://www.novell.com/support/kb/doc.php?id=3456486) per abilitare la console seriale. Le immagini di SLES 12 SP3+ più recenti consentono anche l'accesso tramite la console seriale nel caso in cui il sistema venga avviato in modalità di emergenza.

### <a name="access-for-centos"></a>Accesso per CentOS
Le immagini CentOS disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. Per la modalità utente singolo, seguire le istruzioni riportate in precedenza per le immagini Red Hat. 

### <a name="access-for-oracle-linux"></a>Accesso per Oracle Linux
Le immagini Oracle Linux disponibili in Azure hanno l'accesso alla console abilitato per impostazione predefinita. Per la modalità utente singolo, seguire le istruzioni riportate in precedenza per le immagini Red Hat.

### <a name="access-for-custom-linux-image"></a>Accesso per l'immagine personalizzata di Linux
Per abilitare la console seriale per l'immagine personalizzata della VM Linux, abilitare l'accesso alla console in /etc/inittab per l'esecuzione di un terminale in ttyS0. Di seguito è riportato un esempio di aggiunta nel file inittab 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Errors
La maggior parte degli errori è di natura temporanea e, per risolverli, è sufficiente stabilire di nuovo la connessione. La tabella seguente contiene un elenco di errori e il modo per prevenirli 

Tipi di errore                            |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica per "<VMNAME>". Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. | Assicurarsi che la VM abbia la [diagnostica di avvio](boot-diagnostics.md) abilitata. 
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. | La macchina virtuale deve essere in uno stato avviato per accedere alla console seriale
Non si hanno le autorizzazioni necessarie per usare questa console seriale per la VM. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore macchine virtuali.| L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio "<STORAGEACCOUNTNAME>". Verificare che la diagnostica di avvio sia abilitata per questa VM e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede determinate autorizzazioni. Per informazioni dettagliate, vedere i [requisiti di accesso](#prerequisites)

## <a name="known-issues"></a>Problemi noti 
Poiché l'accesso alla console seriale è ancora in fase di anteprima, è in corso la soluzione di alcuni problemi noti. Di seguito è riportato un elenco di questi problemi con le possibili soluzioni alternative 

Problema                           |   Mitigazione 
:---------------------------------|:--------------------------------------------|
Non sono disponibili opzioni per la console seriale dell'istanza del set di scalabilità di macchine virtuali |  In fase di anteprima l'accesso alla console seriale per le istanze del set di scalabilità di macchine virtuali non è supportato.
Premendo INVIO dopo il banner della connessione, non viene visualizzato un prompt di accesso | [Premendo INVIO, non accade nulla](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Domande frequenti 
**D. Come è possibile inviare commenti e suggerimenti?**

R. Per inviare commenti e suggerimenti, andare a https://aka.ms/serialconsolefeedback. In alternativa (meno consigliabile), inviare commenti e suggerimenti tramite azserialhelp@microsoft.com o nella categoria della macchina virtuale di http://feedback.azure.com

**D.Viene visualizzato l'errore "La console esistente presenta un tipo di sistema operativo "Windows" in conflitto con il tipo di sistema operativo Linux richiesto".**

R. Si tratta di un problema noto. Per risolverlo, è sufficiente aprire [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) in modalità Bash e riprovare.

**D. Impossibile accedere alla console seriale. Dove è possibile inserire un caso di supporto?**

R. Questa funzionalità di anteprima è soggetta alle condizioni per l'anteprima di Azure. Per ottenere supporto, è consigliabile usare i canali indicati in precedenza. 

## <a name="next-steps"></a>Passaggi successivi
* La console seriale è disponibile anche per macchine virtuali [Windows](../windows/serial-console.md)
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md)