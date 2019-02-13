---
title: Console seriale di Azure per le chiamate SysRq e NMI | Microsoft Docs
description: Uso della console seriale per chiamate SysRq e NMI nelle macchine virtuali di Azure.
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
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 6ca4156c19adbeea72ae268fe62638d40919b08f
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699617"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Console seriale per le chiamate SysRq e NMI

## <a name="system-request-sysrq"></a>Richiesta del sistema (SysRq)
Un SysRq è una sequenza di tasti riconosciuta dal kernel del sistema operazione Linux, che può attivare un set di azioni predefinite. Questi comandi vengono spesso usati durante la risoluzione dei problemi relativi alla macchina virtuale o quando il ripristino non può essere eseguito tramite l’amministrazione tradizionale (ad esempio, se la macchina virtuale è bloccata). Usando la funzionalità SysRq della console seriale di Azure si imita la pressione della chiave di SysRq e i caratteri inseriti su una tastiera fisica.

Dopo che la sequenza SysRq viene recapitata, la configurazione del kernel potrà controllare la modalità di risposta del sistema. Per informazioni sull'abilitazione e disabilitazione di SysRq, vedere la *Guida dell'amministratore SysRq* [testo](https://aka.ms/kernelorgsysreqdoc) | [markdown](https://aka.ms/linuxsysrq).  

La console seriale di Azure può essere usata per inviare una SysRq a una macchina virtuale di Azure usando l'icona della tastiera nella barra dei comandi, mostrata di seguito.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Scegliendo "Invia comando SysRq" verrà aperta una finestra di dialogo, che offre opzioni di SysRq comune oppure accetta una sequenza di comandi di SysRq immessi nella finestra di dialogo.  Questo permette alle serie di SysRq di eseguire un'operazione di alto livello, ad esempio un riavvio sicuro usando: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Il comando SysRq non può essere usato nelle macchine virtuali che vengono arrestate o il cui kernel è in uno stato di non risposta. (ad esempio un kernel panic).

### <a name="enable-sysrq"></a>Abilitare SysRq 
Come descritto nella *Guida dell'amministratore SysRq* sopra, SysRq può essere configurato in modo che tutti, nessuno o solo alcuni comandi siano disponibili. È possibile abilitare tutti i comandi SysRq utilizzando la seguente operazione, ma non verrà conservata dopo il riavvio:
```
echo "1" >/proc/sys/kernel/sysrq
```
Per rendere persistente la configurazione SysReq, è possibile eseguire il comando seguente per abilitare tutti i comandi SysRq
1. Aggiungere questa riga in */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Riavviare o aggiornare sysctl eseguendo <br>
    `sysctl -p`

### <a name="command-keys"></a>Chiavi di comando 
Dalla Guida dell'amministratore SysRq precedente:

|Comando| Funzione
| ------| ----------- |
|``b``  |   Il sistema verrà riavviato immediatamente senza la sincronizzazione oppure lo smontaggio dei dischi.
|``c``  |   Esegue un arresto anomalo del sistema attraverso una dereferenziazione del puntatore NULL. Verrà eseguito un dump di arresto anomalo del sistema, se configurato.
|``d``  |   Mostra tutti i blocchi che sono utilizzati.
|``e``  |   Inviare un SIGTERM a tutti i processi, ad eccezione di init.
|``f``  |   In casi di memoria insufficiente verrà terminato un processo che occupa troppa memoria, ma non allarmarsi se non è possibile terminare alcun processo.
|``g``  |   Usato da kgdb (debugger del kernel)
|``h``  |   Verrà visualizzata la Guida (anche qualsiasi altra chiave rispetto a quelle elencate di seguito visualizza la Guida, ma ``h`` è facile da ricordare :-)
|``i``  |    Inviare un SIGKILL a tutti i processi, ad eccezione di init.
|``j``  |    "Semplicemente sbloccalo" in modo forzato - file system bloccato dall’ioctl FIFREEZE.
|``k``  |    Chiave di accesso sicuro (SAK) termina tutti i programmi nella console virtuale corrente. NOTA:  vedere i commenti importanti nella sezione SAK.
|``l``  |    Mostra una traccia dello stack per tutte le CPU attive.
|``m``  |    Viene eseguito il dump delle informazioni sulla memoria corrente per la console.
|``n``  |    Consente di eseguire attività RT interessanti
|``o``  |    Spegne il sistema (se configurata e supportata).
|``p``  |    Viene eseguito il dump dei registri correnti e i flag per la console.
|``q``  |    Viene eseguito il dump per gli elenchi di CPU di tutti gli hrtimers armati (ma non i timer timer_list regolari) e fornisce informazioni dettagliate su tutti i dispositivi clockevent.
|``r``  |    Disattiva la modalità raw da tastiera e la imposta su XLATE.
|``s``  |    Prova a sincronizzare tutti i file System montati.
|``t``  |    Viene eseguito il dump di un elenco di attività correnti e le relative informazioni alla console.
|``u``  |    Tenta di rimontare i file system di sola lettura montati.
|``v``  |    Consente di ripristinare in modo forzato la console buffer frame
|``v``  |    Causa il dump di buffer ETM [specifcio per ARM]
|``w``  |    Esegue il dump di attività con stato non interrompibile (bloccato).
|``x``  |    Usato dall'interfaccia xmon su piattaforme ppc/powerpc. Mostra registri PMU globali su sparc64. Eseguire il dump di tutte le voci TLB su MIPS.
|``y``  |    Visualizzare i registri delle CPU globali [specifico per 64 SPARC]
|``z``  |    Scarica il buffer ftrace
|``0``-``9`` | Imposta il livello di log della console, controllando quali messaggi kernel verranno stampati nella console. (``0``, ad esempio potrebbe impostarlo in modo che vengano visualizzati solo i messaggi di emergenza, ad esempio PANIC o OOPS.)

### <a name="distribution-specific-documentation"></a>Documentazione specifica per la distribuzione ###
Per la documentazione specifica per la distribuzione in SysRq e per i passaggi per configurare Linux per creare un dump di arresto anomalo del sistema quando riceve un comando "Crash" SysRq, vedere i collegamenti seguenti:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Dump di arresto anomalo del kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Che cos'è la funzionalità SysRq e come si usa?](https://access.redhat.com/articles/231663)
- [Come usare la funzionalità SysRq per raccogliere informazioni da un server RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Configurare l'acquisizione di core dump kernel](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Raccolta dei log di arresto anomalo del sistema](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Interrupt non mascherabile (NMI) 
Un interrupt non mascherabile (NMI) è progettato per creare un segnale che il software in una macchina virtuale non ignorerà. In passato, gli NMI sono stati usati per verificare la presenza di problemi hardware in sistemi che necessitavano di tempi di risposta specifici.  Oggi, gli amministratori di sistema e i programmatori usano spesso gli NMI come un meccanismo per eseguire il debug o risolvere i problemi di sistemi che sono bloccati.

La console seriale può essere usata per inviare un NMI a una macchina virtuale di Azure usando l'icona della tastiera nella barra dei comandi, mostrata di seguito. Dopo che l'interrupt non mascherabile viene recapitato, la configurazione della macchina virtuale potrà controllare la modalità di risposta del sistema.  I sistemi operativi Linux possono essere configurati per l'arresto anomalo del sistema e per creare un dump della memoria quando il sistema operativo riceve un NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

Per i sistemi Linux che supportano sysctl per la configurazione dei parametri del kernel, è possibile abilitare un panic quando si riceve questo NMI usando il comando seguente:
1. Aggiungere questa riga in */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Riavviare o aggiornare sysctl eseguendo <br>
    `sysctl -p`

Per altre informazioni sulle configurazioni di kernel di Linux, inclusi `unknown_nmi_panic`, `panic_on_io_nmi` e `panic_on_unrecovered_nmi`, consultare: [Documentation for /proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt) (Documentazione per /proc/sys/kernel/*). Per la documentazione specifica per la distribuzione in NMI e per i passaggi per configurare Linux per creare un dump di arresto anomalo del sistema quando riceve un NMI, vedere i collegamenti seguenti:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Dump di arresto anomalo del kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [Che cos'è un NMI e per cosa è utilizzabile?](https://access.redhat.com/solutions/4127)
 - [Come è possibile configurare il sistema per l’arresto anomalo quando viene eseguito il commutatore NMI?](https://access.redhat.com/solutions/125103)
 - [Guida dell'amministratore per il dump di arresto anomalo del sistema](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Configurare l'acquisizione di core dump kernel](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Raccolta dei log di arresto anomalo del sistema](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Passaggi successivi
* La pagina principale della documentazione di Linux relativa alla console seriale è disponibile [qui](serial-console.md).
* Usare la console seriale per accedere a [GRUB e alla modalità utente singolo](serial-console-grub-single-user-mode.md)
* La console seriale è disponibile anche per macchine virtuali [Windows](../windows/serial-console.md)
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md)