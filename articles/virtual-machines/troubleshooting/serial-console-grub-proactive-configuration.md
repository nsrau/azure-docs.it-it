---
title: Configurazione del GRUB proattivo della console seriale di Azure | Microsoft Docs
description: Configurare GRUB in diverse distribuzioni consentendo l'accesso a singoli utenti e modalità di ripristino in macchine virtuali di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: vilibert
ms.openlocfilehash: a154ab4742f0d0d7acae0376bcf894bc2b62b4cd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186929"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Assicurandosi in modo proattivo che si abbia accesso a GRUB e SysRq, è possibile risparmiare molto tempo

La possibilità di accedere alla console seriale e a GRUB consente di migliorare i tempi di ripristino della macchina virtuale IaaS Linux nella maggior parte dei casi. GRUB offre opzioni di ripristino che altrimenti potrebbero richiedere più tempo per il ripristino della macchina virtuale. 


I motivi per eseguire un ripristino della macchina virtuale sono molti e possono essere attribuiti a scenari come:

   - File System danneggiati/kernel/MBR (record di avvio principale)
   - Aggiornamenti del kernel non riusciti
   - Parametri del kernel GRUB non corretti
   - Configurazioni fstab non corrette
   - Configurazioni del firewall
   - Password persa
   - File di configurazione sshd modificato
   - Configurazioni di rete

 Molti altri scenari, come descritto in dettaglio [qui](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Verificare che sia possibile accedere a GRUB e al console seriale nelle VM distribuite in Azure. 

Se non si ha familiarità con la console seriale, fare riferimento a [questo collegamento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Assicurarsi di eseguire i backup dei file prima di apportare modifiche

Guardare questo video di seguito per vedere come è possibile ripristinare rapidamente la VM Linux quando si ha accesso a GRUB

[Video ripristino VM Linux](https://youtu.be/KevOc3d_SG4)

Sono disponibili diversi metodi per il ripristino delle macchine virtuali Linux. In un ambiente cloud, questo processo è stato complesso.
Lo stato di avanzamento viene eseguito continuamente per gli strumenti e le funzionalità per garantire il ripristino rapido dei servizi.

Con la console seriale di Azure è possibile interagire con la VM Linux come se si trattasse di una console di sistema.

È possibile modificare molti file di configurazione, tra cui il modo in cui il kernel si avvierà. 

Gli amministratori di sys Linux/UNIX più esperti apprezzeranno le modalità **utente singolo** e di **emergenza** accessibili tramite la console seriale di Azure, rendendo ridondante lo scambio di dischi e l'eliminazione di VM per molti scenari di ripristino.

Il metodo di recupero dipende dal problema riscontrato, ad esempio è possibile reimpostare una password persa o smarrita tramite portale di Azure Opzioni-> **reimpostare la password**. La funzionalità **Reimposta password** è nota come estensione e comunica con l'agente guest Linux.

Sono disponibili altre estensioni, ad esempio script personalizzati, ma queste opzioni richiedono che il **waagent** di Linux sia attivo e in uno stato integro, che non è sempre il caso.

![stato agente](./media/virtual-machines-serial-console/agent-status.png)


Assicurarsi di avere accesso alla console seriale di Azure e a GRUB significa che è possibile correggere una modifica della password o una configurazione errata in pochi minuti anziché in ore. È anche possibile forzare l'avvio della macchina virtuale da un kernel alternativo se si dispone di più kernel su disco nello scenario in cui il kernel primario risulta danneggiato.

![kernel multikernel](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Ordine di metodi di recupero suggerito:

- Console seriale di Azure

- Scambio del disco: può essere automatizzato tramite:

   - [Script di ripristino di Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Script di ripristino bash](https://github.com/sribs/azure-support-scripts)

- Metodo legacy

## <a name="disk-swap-video"></a>Video di scambio disco:

Se non si ha accesso a GRUB, guardare [questo](https://youtu.be/m5t0GZ5oGAc) video e vedere come è possibile automatizzare facilmente la procedura di scambio del disco per ripristinare la macchina virtuale

## <a name="challenges"></a>Sfide

Non tutte le macchine virtuali Linux di Azure sono configurate per impostazione predefinita per l'accesso a GRUB e nessuno di essi è configurato per essere interrotto con i comandi sysrq. Alcune distribuzioni precedenti, ad esempio SLES 11, non sono configurate per la visualizzazione della richiesta di accesso nella console seriale di Azure

In questo articolo verranno esaminate le diverse distribuzioni di Linux e le configurazioni di documenti su come rendere disponibile GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Come configurare una macchina virtuale Linux per accettare le chiavi di SysRq
Per impostazione predefinita, la chiave sysrq è abilitata in alcune distribuzioni Linux più recenti, anche se in altre possono essere configurate per accettare valori solo per determinate funzioni SysRq.
Nelle distribuzioni precedenti, potrebbe essere disabilitata completamente.

La funzionalità SysRq è utile per il riavvio di una macchina virtuale bloccata o bloccata direttamente dalla console seriale di Azure, utile anche per accedere al menu GRUB, in alternativa riavviare una macchina virtuale da un'altra finestra del portale o una sessione ssh potrebbe eliminare la connessione console corrente scadendo quindi i timeout di GRUB a cui vengono usati per visualizzare il menu GRUB.
La macchina virtuale deve essere configurata in modo da accettare un valore pari a 1 per il parametro kernel, che Abilita tutte le funzioni di sysrq o 128, che consente il riavvio/spento


[Abilita video sysrq](https://youtu.be/0doqFRrHz_Mc)


Per configurare la macchina virtuale in modo che accetti un riavvio tramite i comandi SysRq nel portale di Azure, è necessario impostare il valore 1 per il parametro kernel kernel. sysrq

Per salvare in modo permanente il riavvio di questa configurazione, aggiungere una voce al file **sysctl. conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Per configurare dinamicamente il parametro kernel

`sysctl -w kernel.sysrq=1`

Se non si dispone dell'accesso alla **radice** o sudo è danneggiato, non sarà possibile configurare sysrq da un prompt della shell.

In questo scenario è possibile abilitare sysrq usando il portale di Azure. Questo metodo può essere utile se il file **sudoers. d/waagent** è stato danneggiato o è stato eliminato.

Usando la funzionalità portale di Azure Operations-> eseguire Command-> RunShellScript, è necessario che il processo waagent sia integro, quindi è possibile inserire questo comando per abilitare sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Come illustrato di seguito: ![abilitare sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Al termine, è possibile provare ad accedere a **sysrq** e verificare che sia possibile riavviare il computer.

![Abilita sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selezionare il comando **Riavvia** e **Invia sysrq**

![Abilita sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Il sistema deve registrare un messaggio di reimpostazione, ad esempio

![Abilita sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configurazione di Ubuntu GRUB

Per impostazione predefinita, si dovrebbe essere in grado di accedere a GRUB tenendo premuto **ESC** durante l'avvio della macchina virtuale. se il menu GRUB non è visualizzato, è possibile forzare e mantenere lo schermo del menu GRUB nella console seriale di Azure usando una di queste opzioni.

**Opzione 1** : forza la visualizzazione di GRUB sullo schermo 

Aggiornare il file/etc/default/grub.d/50-cloudimg-Settings.cfg per lasciare lo schermo del menu GRUB per il TIMEOUT specificato.
Non è necessario premere **ESC** perché grub verrà visualizzato immediatamente

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opzione 2** : consente di premere **ESC** prima dell'avvio

Un comportamento simile può essere sperimentato apportando modifiche al file/etc/default/grub e osservando un timeout di 3 secondi per premere **ESC**


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

Ubuntu 12,04 consentirà l'accesso alla console seriale, ma non offre la possibilità di interagire. Un **account di accesso:** prompt non è visibile


Per 12,04 ottenere un **account di accesso:** prompt:
1. Creare un file denominato/etc/init/ttyS0.conf contenente il testo seguente:

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

2. Richiedi avvio per avviare il Getty     
    ```
    sudo start ttyS0
    ```
 
Le impostazioni necessarie per configurare la console seriale per le versioni di Ubuntu sono disponibili [qui](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Modalità di ripristino Ubuntu

Sono disponibili opzioni aggiuntive per il ripristino e la pulizia per Ubuntu tramite GRUB, tuttavia queste impostazioni sono accessibili solo se si configurano i parametri del kernel di conseguenza.
Se non si configura questo parametro di avvio del kernel, il menu di ripristino verrà inviato al Diagnostica di Azure e non alla console seriale di Azure.
È possibile ottenere l'accesso al menu ripristino di Ubuntu attenendosi alla procedura seguente:

Interrompere il processo di avvio e accedere al menu GRUB

Selezionare Opzioni avanzate per Ubuntu e premere INVIO.

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Selezionare la visualizzazione linea *(modalità di ripristino)* non premere invio ma premere "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Individuare la riga che caricherà il kernel e sostituire l'ultimo parametro **nomodet** con la destinazione As **console = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Premere **CTRL + x** per avviare e caricare il kernel.
Se tutto va bene, vengono visualizzate le opzioni aggiuntive che consentono di eseguire altre opzioni di ripristino

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configurazione di Red Hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Configurazione di Red Hat 7\.4\+ GRUB
La configurazione predefinita di/etc/default/grub in queste versioni è configurata adeguatamente

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
Il file da modificare è/etc/default/grub. una configurazione predefinita è simile a questo esempio:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Modificare le righe seguenti in/etc/default/grub

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

Aggiungere anche questa riga:

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
Il file da modificare è/boot/grub/grub.conf. Il valore `timeout` determinerà per quanto tempo viene visualizzato GRUB.

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


L'ultimo terminale di riga *– timeout = 5 console seriale* aumenterà ulteriormente il timeout di **grub** aggiungendo una richiesta di 5 secondi per la visualizzazione di un **tasto premere un tasto qualsiasi per continuare.**

![RH6-1](./media/virtual-machines-serial-console/rh6-1.png)

Il menu GRUB verrà visualizzato sullo schermo per il timeout configurato = 15 senza la necessità di premere ESC. Assicurarsi di fare clic nella console del browser per rendere attivo il menu e selezionare il kernel necessario

![RH6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 SP1
Usare il bootloader di YaST come per la [documentazione](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles) ufficiale

In alternativa, Aggiungi/modifica a/etc/default/grub i parametri seguenti:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Verificare che ttyS0 sia usato nell'GRUB_CMDLINE_LINUX o GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Ricreare grub. cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Viene visualizzata la console seriale e vengono visualizzati i messaggi di avvio ma non viene visualizzato alcun **account di accesso:** prompt

Aprire una sessione SSH nella macchina virtuale e aggiornare il file **/etc/inittab** inserendo un commento per la riga seguente:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Eseguire quindi il comando 

`telinit q`

Per abilitare GRUB, è necessario apportare le modifiche seguenti a/boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Questa configurazione consentirà al messaggio di **premere un tasto qualsiasi per continuare** a essere visualizzato nella console per 5 secondi 

Il menu GRUB verrà visualizzato per altri 5 secondi. premendo la freccia giù si interrompe il contatore e si seleziona un kernel che si vuole avviare aggiungendo la parola chiave **Single** per la modalità utente singolo che richiede l'impostazione della password radice.

Se si aggiunge il comando **init =/bin/bash** , il kernel verrà caricato ma si garantisce che il programma init venga sostituito da una shell bash.

Si otterrà l'accesso a una shell senza dover immettere una password. È quindi possibile procedere con l'aggiornamento della password per gli account Linux o apportare altre modifiche alla configurazione.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Forza il kernel a una richiesta bash
L'accesso a GRUB consente di interrompere il processo di inizializzazione. questa interazione è utile per molte procedure di ripristino.
Se non si ha la password radice e l'utente singolo richiede una password radice, è possibile avviare il kernel sostituendo il programma init con una richiesta bash. per ottenere questo interrupt, aggiungere init =/bin/bash alla riga di avvio del kernel

![bash1](./media/virtual-machines-serial-console/bash1.png)

Rimontare la/(radice) file system RW usando il comando

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


A questo punto è possibile eseguire una modifica della password radice o molte altre modifiche alla configurazione di Linux

![bash3](./media/virtual-machines-serial-console/bash3.png)

Riavviare la macchina virtuale con 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modalità utente singolo

In alternativa, potrebbe essere necessario accedere alla macchina virtuale in modalità utente singolo o di emergenza. Selezionare il kernel che si vuole avviare o interrompere usando i tasti di direzione.
Immettere la modalità desiderata aggiungendo la parola chiave **Single** o **1** alla riga di avvio del kernel. Nei sistemi RHEL è inoltre possibile aggiungere **Rd. Break**.

Per altre informazioni su come accedere alla modalità utente singolo, vedere [questo documento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Passaggi successivi
Scopri di più sulla [console seriale di Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)