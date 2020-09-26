---
title: Configurazione del GRUB proattivo della console seriale di Azure | Microsoft Docs
description: Configurare GRUB in diverse distribuzioni consentendo l'accesso alle modalità singolo utente e ripristino in macchine virtuali di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: aba47500400004c1d6a7044a266bad6f20d5d9c9
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360549"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>È possibile risparmiare molto tempo assicurandosi in modo proattivo di avere accesso a GRUB e SysRq

Nella maggior parte dei casi la possibilità di accedere alla console seriale e a GRUB consente di migliorare i tempi di ripristino della macchina virtuale IaaS Linux. GRUB offre opzioni di ripristino che altrimenti potrebbero richiedere più tempo per il ripristino della macchina virtuale. 


I motivi per eseguire un ripristino della macchina virtuale sono molti e possono essere attribuiti a scenari come:

   - File system/kernel/MBR (Record di avvio principale) danneggiati
   - Aggiornamenti del kernel non riusciti
   - Parametri del kernel GRUB non corretti
   - Configurazione di fstab non corrette
   - Configurazioni del firewall
   - Password persa
   - File di configurazione sshd modificati
   - Configurazioni di rete

 Molti altri scenari descritti in maniera dettagliata [qui](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)

Verificare che sia possibile accedere a GRUB e alla console seriale nelle macchine virtuali distribuite in Azure. 

Se non si ha familiarità con la console seriale, fare riferimento a [questo collegamento](./serial-console-linux.md).

> [!TIP]
> Assicurarsi di eseguire i backup dei file prima di apportare modifiche

Guardare il video seguente per scoprire come è possibile ripristinare rapidamente la macchina virtuale Linux quando si ha accesso a GRUB

[Video sul ripristino della macchina virtuale Linux](https://youtu.be/KevOc3d_SG4)

Sono disponibili diversi metodi per agevolare il ripristino delle macchine virtuali Linux. In un ambiente cloud, questo processo è complesso.
Lo stato di avanzamento viene eseguito continuamente per gli strumenti e le funzionalità per garantire il ripristino rapido dei servizi.

Con la console seriale di Azure è possibile interagire con la macchina virtuale Linux come se si trattasse di una console di sistema.

È possibile modificare molti file di configurazione, tra cui la modalità di avvio del kernel. 

Gli amministratori dei sistemi Linux/Unix più esperti apprezzeranno le modalità **singolo utente** e **di emergenza** accessibili tramite la console seriale di Azure, che rendono ridondante lo scambio di dischi e l'eliminazione della macchina virtuale per molti scenari di ripristino.

Il metodo di recupero dipende dal problema riscontrato, ad esempio è possibile reimpostare una password persa o posizionata erroneamente tramite le opzioni del portale di Azure -> **Reimposta la password**. La funzionalità **Reimposta password** è nota come estensione e comunica con l'agente guest di Linux.

Sono disponibili altre estensioni, ad esempio script personalizzati, ma queste opzioni richiedono che il **waagent** di Linux sia attivo e in uno stato di integrità, cosa non sempre possibile.

![stato dell'agente](./media/virtual-machines-serial-console/agent-status.png)


La possibilità di accedere alla console seriale di Azure e a GRUB consente di correggere una modifica della password o una configurazione errata in pochi minuti anziché in ore. È anche possibile forzare l'avvio della macchina virtuale da un kernel alternativo se si dispone di più kernel su disco nello scenario in cui il kernel primario risulti danneggiato.

![multikernel](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Ordine dei metodi di ripristino suggerito:

- Console seriale di Azure

- Scambio del disco - può essere automatizzato usando:

   - [Script di ripristino di PowerShell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Script di ripristino bash](https://github.com/sribs/azure-support-scripts)

- Metodo legacy

## <a name="disk-swap-video"></a>Video sullo scambio del disco:

Se non si ha accesso a GRUB guardare [questo](https://youtu.be/m5t0GZ5oGAc) video per scoprire come è possibile automatizzare facilmente la procedura di scambio del disco per ripristinare la macchina virtuale

## <a name="challenges"></a>Problematiche:

Non tutte le macchine virtuali Azure Linux sono configurate per impostazione predefinita per l'accesso a GRUB o configurate per essere interrotte con i comandi SysRq. Alcune distribuzioni precedenti, ad esempio SLES 11, non sono configurate per la visualizzazione della richiesta di accesso nella console seriale di Azure

In questo articolo verranno esaminate le diverse distribuzioni di Linux e le configurazioni di documenti su come rendere disponibile GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Come configurare una macchina virtuale Linux per accettare le chiavi SysRq
Per impostazione predefinita, la chiave SysRq è abilitata in alcune distribuzioni Linux più recenti, anche se in altre potrebbe essere configurata per accettare valori solo per determinate funzioni SysRq.
Nelle distribuzioni precedenti, potrebbe essere disabilitata completamente.

La funzionalità SysRq è utile per il riavvio di una macchina virtuale arrestata in modo anomalo o che non risponde direttamente dalla console seriale di Azure, utile anche per accedere al menu GRUB; in alternativa riavviando una macchina virtuale da un'altra finestra del portale o da una sessione ssh si potrebbe perdere la connessione corrente della console, facendo scadere i timeout di GRUB su cui viene visualizzato il menu GRUB.
La macchina virtuale deve essere configurata in modo da accettare un valore pari a 1 per il parametro kernel, che abilita tutte le funzioni di SysRq o 128, che consente il riavvio/spegnimento


[Video sull'abilitazione di SysRw](https://youtu.be/0doqFRrHz_Mc)


Per configurare la macchina virtuale in modo che accetti un riavvio tramite i comandi SysRq nel portale di Azure, è necessario impostare il valore 1 per il parametro kernel kernel.sysrq

Affinché questa configurazione renda permanente il riavvio, aggiungere una voce al file **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Per configurare dinamicamente il parametro kernel

`sysctl -w kernel.sysrq=1`

Se non si dispone di accesso **radice** o se il sudo è danneggiato, non sarà possibile configurare SysRq da un prompt della shell.

In questo scenario è possibile abilitare SysRq usando il portale di Azure. Questo metodo può risultare vantaggioso se il file **sudoers.d/waagent** è stato danneggiato o è stato eliminato.

Usando la funzionalità del portale di Azure Operazioni-> Esegui comando -> RunShellScript, è necessario che il processo waagent sia integro, quindi è possibile inserire questo comando per abilitare SysRq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Come illustrato qui: ![abilitare SysRq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Al termine, è possibile provare ad accedere a **SysRq** e verificare che sia possibile riavviare il computer.

![abilitare SysRq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selezionare **Riavvia** e il comando **Invia SysRq**

![abilitare SysRq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Il sistema deve registrare un messaggio di reimpostazione, ad esempio

![abilitare SysRq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configurazione di Ubuntu GRUB

Per impostazione predefinita, si dovrebbe essere in grado di accedere a GRUB tenendo premuto il tasto **ESC** durante l'avvio della macchina virtuale. Se il menu GRUB non è visualizzato, è possibile forzare e mantenere il menu GRUB sullo schermo nella console seriale di Azure usando una di queste opzioni.

**Opzione 1**: forza la visualizzazione di GRUB sullo schermo 

Aggiornare il file /etc/default/grub.d/50-cloudimg-Settings.cfg per mantenere il menu GRUB sullo schermo per il TIMEOUT specificato.
Non è necessario premere **ESC** perché GRUB verrà visualizzato immediatamente

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opzione 2**: consente la pressione di **ESC** prima dell'avvio

Un comportamento simile può essere sperimentato apportando modifiche al file /etc/default/grub e osservando un timeout di 3 secondi per premere **ESC**


Impostare come commento le due righe seguenti:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
e aggiungere questa riga:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 consente l'accesso alla console seriale, ma non offre la possibilità di interagire. Un prompt **account di accesso:** non è visibile


Per 12.04 ottenere un prompt **account di accesso:** :
1. Creare un file denominato /etc/init/ttyS0.conf contenente il testo seguente:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Richiedere l'avvio per avviare il Getty     
    ```
    sudo start ttyS0
    ```
 
È possibile trovare le impostazioni necessarie per configurare la console seriale per le versioni di Ubuntu [qui](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Modalità di ripristino Ubuntu

Sono disponibili opzioni aggiuntive per il ripristino e la pulizia per Ubuntu tramite GRUB, tuttavia queste impostazioni sono accessibili solo se si configurano i parametri del kernel di conseguenza.
Se non si configura questo parametro di avvio del kernel, il menu di ripristino verrà inviato alla Diagnostica di Azure e non alla console seriale di Azure.
È possibile ottenere l'accesso al menu ripristino di Ubuntu attenendosi alla procedura seguente:

Interrompere il processo di avvio e accedere al menu GRUB

Selezionare Opzioni avanzate per Ubuntu e premere Invio

![Screenshot mostra il console seriale con le opzioni avanzate per Ubuntu selezionato.](./media/virtual-machines-serial-console/ubunturec1.png)

Selezionare la riga che visualizza *(modalità di ripristino)* non premere Invio ma premere "e"

![Screenshot mostra la console seriale con una versione della modalità di ripristino selezionata.](./media/virtual-machines-serial-console/ubunturec2.png)

Individuare la riga che caricherà il kernel e sostituire l'ultimo parametro **nomodeset** con destinazione **console=ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![Screenshot mostra la console seriale con il valore modificato.](./media/virtual-machines-serial-console/ubunturec3.png)

Premere **CTRL+x** per avviare e caricare il kernel.
Se tutto viene eseguito correttamente, vengono visualizzate le opzioni aggiuntive che consentono di eseguire altre opzioni di ripristino

![Screenshot mostra la console seriale dal menu ripristino, che offre opzioni di ripristino aggiuntive.](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configurazione di Red Hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Configurazione di Red Hat 7\.4\+ GRUB
La configurazione predefinita di /etc/default/grub in queste versioni è configurata adeguatamente

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Abilitare la chiave SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Configurazione di Red Hat 7\.2 e 7\.3 GRUB
Il file da modificare è /etc/default/grub. Una configurazione predefinita è simile a questo esempio:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Modificare le righe seguenti in /etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Aggiungere questa riga:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub dovrebbe ora essere simile a questo esempio:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Completare e aggiornare la configurazione di GRUB usando

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Impostare il parametro del kernel SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

In alternativa, è possibile configurare GRUB e SysRq usando una singola riga nella shell o tramite il comando Esegui. Eseguire il backup dei file prima di eseguire questo comando:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Configurazione di Red Hat 6\.x GRUB
Il file da modificare è /boot/grub/grub.conf. Il valore `timeout` determinerà per quanto tempo viene visualizzato GRUB.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


L'ultima riga *terminal –-timeout=5 serial console* aumenterà ulteriormente il timeout di **GRUB** aggiungendo un prompt di 5 secondi che visualizza **Premere un tasto qualsiasi per continuare.**

![Screenshot mostra una console con output.](./media/virtual-machines-serial-console/rh6-1.png)

Il menu GRUB verrà visualizzato sullo schermo per il timeout configurato (15) senza la necessità di premere il tasto ESC. Assicurarsi di fare clic nella console del browser per rendere attivo il menu e selezionare il kernel necessario

![Screenshot mostra una console con due opzioni di Linux.](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
Usare il bootloader di YaST secondo quanto indicato nella [documentazione ufficiale](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles)

In alternativa, modificare /etc/default/grub o aggiungervi i parametri seguenti:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Verificare che ttyS0 sia usato in GRUB_CMDLINE_LINUX o GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Ricreare il grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Viene visualizzata la console seriale con i messaggi di avvio ma non viene visualizzato un prompt **login:**

Aprire una sessione ssh nella macchina virtuale e aggiornare il file **/etc/inittab** mediante l'annullamento del commento di questa riga:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Successivamente, eseguire il comando 

`telinit q`

Per abilitare GRUB, è necessario apportare le modifiche seguenti a /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Questa configurazione consentirà al messaggio **Premere un tasto qualsiasi per continuare** di essere visualizzato nella console per 5 secondi 

Il menu GRUB verrà visualizzato per altri 5 secondi; premendo la freccia giù si interrompe il contatore e si seleziona un kernel che si vuole avviare aggiungendo la parola chiave **singolo** per la modalità utente singolo che richiede l'impostazione della password radice.

Se si aggiunge il comando **init =/bin/bash** il kernel verrà caricato, ma si garantisce che il programma init sia sostituito da una shell bash.

Si otterrà l'accesso a una shell senza dover immettere una password. È quindi possibile procedere con l'aggiornamento della password per gli account Linux o apportare altre modifiche alla configurazione.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Forzare il kernel a una richiesta bash
L'accesso a GRUB consente di interrompere il processo di inizializzazione. Questa interazione è utile per molte procedure di ripristino.
Se non si dispone della password radice e l'utente singolo richiede una password radice, è possibile avviare il kernel sostituendo il programma init con un prompt bash. Per ottenere questa interruzione, aggiungere init =/bin/bash alla riga di avvio del kernel

![Screenshot mostra una console con la riga di avvio aggiornata.](./media/virtual-machines-serial-console/bash1.png)

Rimontare l'RW del file system (radice) usando il comando

`mount -o remount,rw /`

![Screenshot mostra una console con un'azione di rimontaggio.](./media/virtual-machines-serial-console/bash2.png)


A questo punto è possibile eseguire una modifica della password radice o molte altre modifiche alla configurazione di Linux

![Screenshot mostra una console in cui è possibile modificare la password radice e altre configurazioni.](./media/virtual-machines-serial-console/bash3.png)

Riavviare la macchina virtuale con 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modalità utente singolo

In alternativa, potrebbe essere necessario accedere alla macchina virtuale in modalità utente singolo o di emergenza. Selezionare il kernel che si vuole avviare o interrompere usando i tasti di direzione.
Immettere la modalità desiderata aggiungendo la parola chiave **singolo** o **1** alla riga di avvio del kernel. Nei sistemi RHEL è inoltre possibile aggiungere **rd.break**.

Per altre informazioni su come accedere alla modalità utente singolo, vedere [questo documento](./serial-console-grub-single-user-mode.md#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [console seriale di Azure]( ./serial-console-linux.md)