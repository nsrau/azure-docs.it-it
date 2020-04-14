---
title: Configurazione GRUB proattiva della console seriale di Azure Documenti Microsoft
description: Configurare GRUB tra varie distribuzioni consentendo l'accesso in modalità di ripristino e utente singolo nelle macchine virtuali di Azure.Configure GRUB across various distributions allowing single user and recovery mode access in Azure virtual machines.
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
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262894"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Garantire in modo proattivo l'accesso a GRUB e sysrq potrebbe farti risparmiare un sacco di tempo di inattività

Avere accesso alla Console Seriale e a GRUB migliorerà i tempi di recupero della macchina virtuale Linux IaaS nella maggior parte dei casi. GRUB offre opzioni di ripristino che altrimenti richiederebbero più tempo per il ripristino della macchina virtuale. 


I motivi per eseguire il ripristino di una macchina virtuale sono molti e possono essere attribuiti a scenari quali:The reasons to perform a VM recovery are many and can be attributed to scenarios such as:

   - File system danneggiati/kernel/MBR (Registrazione di avvio master)
   - Aggiornamenti del kernel non riusciti
   - Parametri del kernel GRUB errati
   - Configurazioni fstab errate
   - Configurazioni del firewall
   - Password persa
   - File di configurazioni sshd straziati
   - Configurazioni di rete

 Molti altri scenari come descritto [qui](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Verificare che sia possibile accedere a GRUB e alla console seriale nelle macchine virtuali distribuite in Azure.Verify that you can access GRUB and the Serial console on your VMs deployed in Azure. 

Se non si ha familiarità con console seriale, fare riferimento a [questo collegamento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Assicurarsi di eseguire il backup dei file prima di apportare modifiche

Guarda questo video qui sotto per vedere come recuperare rapidamente la tua VM Linux una volta che hai accesso a GRUB

[Recuperare il video sulla macchina virtuale LinuxRecover Linux VM Video](https://youtu.be/KevOc3d_SG4)

Esistono diversi metodi per facilitare il ripristino delle macchine virtuali Linux.There are a number of methods to help recovery of Linux VMs. In un ambiente Cloud, questo processo è stato impegnativo.
Si stanno compiendo continui progressi rispetto agli utensili e alle funzionalità per garantire che i servizi vengano recuperati rapidamente.

Con la console seriale di Azure è possibile interagire con la macchina virtuale Linux come se ci si trattasse di una console di sistema.

È possibile manipolare molti file di configurazione, tra cui la modalità di avvio del kernel. 

Gli amministratori di sistemi Linux/Unix più esperti apprezzeranno le modalità **utente singolo** e di **emergenza** accessibili tramite la console di serializzazione di Azure che rende ridondante lo scambio di dischi e l'eliminazione di macchine virtuali per molti scenari di ripristino.

Il metodo di ripristino dipende dal problema riscontrato, ad esempio una password persa o smarrita può essere reimpostata tramite le opzioni del portale di Azure -> **Reimposta password**. La funzionalità **Reimposta password** è nota come estensione e comunica con l'agente Guest Linux.

Altre estensioni come Custom Script sono disponibili tuttavia queste opzioni richiedono che il **waagent** Linux essere e in uno stato sano che non è sempre il caso.

![stato dell'agente](./media/virtual-machines-serial-console/agent-status.png)


Garantire l'accesso alla console seriale di Azure e a GRUB significa che una modifica della password o una configurazione non corretta può essere corretta in pochi minuti anziché in poche ore. È anche possibile forzare l'avvio della macchina virtuale da un kernel alternativo nel caso in cui si disponga di più kernel su disco nello scenario in cui il kernel primario viene danneggiato.

![multi kernel](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Ordine consigliato dei metodi di recupero:

- Console seriale di AzureAzure Serial Console

- Scambio disco – può essere automatizzato utilizzando:

   - [Script di ripristino di Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Bash Script di recupero](https://github.com/sribs/azure-support-scripts)

- Metodo Legacy

## <a name="disk-swap-video"></a>Video di scambio disco:

Se non si ha accesso a GRUB guardare [questo](https://youtu.be/m5t0GZ5oGAc) video e vedere, come è possibile automatizzare facilmente la procedura di scambio del disco per ripristinare la macchina virtuale

## <a name="challenges"></a>Sfide:

Non tutte le macchine virtuali di Azure Linux sono configurate per impostazione predefinita per l'accesso GRUB e non sono tutte configurate per essere interrotte con i comandi sysrq. Alcune distribuzioni meno recenti, ad esempio SLES 11, non sono configurate per visualizzare il prompt di accesso nella console seriale di AzureSome older distros such as SLES 11 are not configured to display Login prompt in the Azure Serial Console

In questo articolo, esamineremo varie distribuzioni Linux e documentare le configurazioni su come rendere gRUB disponibile.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Come configurare la macchina virtuale Linux per accettare le chiavi SysRqHow to configure Linux VM to accept SysRq keys
La chiave sysrq è abilitata su alcune distribuzioni Linux più recenti per impostazione predefinita, anche se in altre potrebbe essere configurata per accettare valori solo per determinate funzioni SysRq.
Nelle distribuzioni più vecchie, potrebbe essere disabilitato completamente.

La funzionalità SysRq è utile per riavviare una macchina virtuale bloccata o in modo anomalo direttamente dalla console seriale di Azure, utile anche per accedere al menu GRUB, in alternativa, il riavvio di una macchina virtuale da un'altra finestra del portale o sessione ssh potrebbe eliminare la connessione alla console corrente, in scadenza ai timeout GRUB in cui vengono utilizzati per visualizzare il menu GRUB.
La macchina virtuale deve essere configurata per accettare un valore pari a 1 per il parametro del kernel, che abilita tutte le funzioni di sysrq o 128, che consente il riavvio/spegnimento


[Abilita video sysrq](https://youtu.be/0doqFRrHz_Mc)


Per configurare la macchina virtuale per accettare un riavvio tramite i comandi SysRq nel portale di Azure, è necessario impostare un valore pari a 1 per il parametro del kernel kernel.sysrq

Per rendere persistente un riavvio, aggiungere una voce al file **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Per configurare il parametro del kernel in modo dinamico

`sysctl -w kernel.sysrq=1`

Se l'accesso **root** non è stato interrotto, non sarà possibile configurare sysrq da un prompt della shell.

È possibile abilitare sysrq in questo scenario usando il portale di Azure.You can enable sysrq in this scenario using the Azure portal. Questo metodo può essere utile se il file **sudoers.d/waagent** si è rotto o è stato eliminato.

L'uso del portale di Azure Operazioni -> Comando di esecuzione -> funzionalità RunShellScript richiede che il processo waagent sia integro, è quindi possibile inserire questo comando per abilitare sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Come illustrato ![di seguito: enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Una volta completato, è possibile provare ad accedere a **sysrq** e dovrebbe vedere che è possibile un riavvio.

![abilitare sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selezionare **Il comando Reboot** e Send **SysRq**

![abilitare sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Il sistema deve registrare un messaggio di reimpostazione

![abilitare sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configurazione GRUB di Ubuntu

Per impostazione predefinita, dovresti essere in grado di accedere a GRUB tenendo premuto il tasto **Esc** durante l'avvio della macchina virtuale, se il menu GRUB non viene presentato puoi forzare e mantenere il menu GRUB sullo schermo nella Console seriale di Azure usando una di queste opzioni.

**Opzione 1** - Forza la visualizzazione di GRUB sullo schermo 

Aggiornare il file /etc/default/grub.d/50-cloudimg-settings.cfg per mantenere il menu GRUB sullo schermo per il TIMEOUT specificato.
Non è necessario colpire **Esc** poiché GRUB verrà visualizzato immediatamente

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opzione 2** - Consente di premere **Esc** prima dell'avvio

Un comportamento simile può essere sperimentato apportando modifiche al file /etc/default/grub e osservare un timeout di 3 secondi per premere **Esc**


Commentare queste due righe:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
e aggiungere questa riga:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 consentirà l'accesso alla console seriale, ma non offre la possibilità di interagire. Un **login:** prompt non viene visualizzato


Per la 12.04 per ottenere un **login:** prompt:
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

2. Chiedi a upstart di avviare il getty     
    ```
    sudo start ttyS0
    ```
 
Le impostazioni necessarie per configurare la console seriale per le versioni di Ubuntu sono disponibili [qui](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Modalità di recupero Ubuntu

Ulteriori opzioni di ripristino e pulizia sono disponibili per Ubuntu tramite GRUB, tuttavia queste impostazioni sono accessibili solo se si configurano i parametri del kernel di conseguenza.
La mancata configurazione di questo parametro di avvio del kernel imporrebbe l'invio del menu Ripristino alla diagnostica di Azure e non alla console di serializzazione di Azure.Failure to configure this kernel boot parameter would force the Recovery menu to be sent to the Azure Diagnostics and not to the Azure Serial Console.
È possibile ottenere l'accesso al menu di ripristino Ubuntu attenendosi alla seguente procedura:

Interrompere il processo BOOT e accedere al menu GRUB

Selezionare Opzioni avanzate per Ubuntu e premere Invio

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Selezionare la linea che visualizza *(modalità di ripristino)* non premere Invio, ma premere "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Individuare la riga che caricherà il kernel e sostituire l'ultimo parametro **nomodeset** con la destinazione come **console-ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Premere **Ctrl-x** per avviare e caricare il kernel.
Se tutto va bene vedrete queste opzioni aggiuntive, che possono aiutare a eseguire altre opzioni di recupero

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configurazione Red Hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Red Hat\.\+ 7 4 Configurazione GRUB
La configurazione predefinita /etc/default/grub in queste versioni è configurata in modo adeguato

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

Attivare la chiave SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat\.7\.2 e 7 3 Configurazione GRUB
Il file da modificare è /etc/default/grub: una configurazione predefinita è simile a questo esempio:The file to modify is /etc/default/grub – a default config looks like this example:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Modificare le seguenti righe in /etc/default/grub

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

Aggiungi anche questa riga:

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
 
Completare e aggiornare la configurazione grub utilizzando

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Impostare il parametro del kernel SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

In alternativa, è possibile configurare GRUB e SysRq utilizzando una singola riga nella shell o tramite il comando Esegui. Eseguire il backup dei file prima di eseguire questo comando:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Configurazione Red\.Hat 6 x GRUB
Il file da modificare è /boot/grub/grub.conf. Il `timeout` valore determinerà per quanto tempo viene visualizzato GRUB.

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


L'ultima *linea terminale –-timeout-5 console seriale* aumenterà ulteriormente il timeout **GRUB** aggiungendo un prompt di 5 secondi che visualizza **Premere qualsiasi tasto per continuare.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

Il menu GRUB dovrebbe apparire sullo schermo per il timeout configurato, senza la necessità di premere ESC. Assicurarsi di fare clic nella Console nel browser per rendere attivo il menu e selezionare il kernel richiesto

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
Usa bootloader yast come da [documenti](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles) ufficiali

Oppure aggiungere / cambiare a /etc/default/grub i seguenti parametri:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Verificare che ttys0 venga utilizzato nel GRUB_CMDLINE_LINUX o GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Ricreare il file grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Viene visualizzata la console seriale con i messaggi di avvio ma non viene visualizzato il **messaggio di accesso:**

Aprire una sessione ssh nella macchina virtuale e aggiornare il file /etc/inittab annullando il commento da questa riga:Open an ssh session into the VM and update the file **/etc/inittab** by un-commenting this line:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Eseguire quindi il comando 

`telinit q`

Per abilitare GRUB, è necessario apportare le seguenti modifiche a /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Questa configurazione consentirà al messaggio **Premere un tasto qualsiasi per continuare a** comparire sulla console per 5 secondi 

Verrà quindi visualizzato il menu GRUB per altri 5 secondi - premendo la freccia giù si interromperà il contatore e si seleziona un kernel che si desidera avviare aggiungere la parola chiave **single** per la modalità utente singolo che richiede la password radice da impostare.

L'aggiunta del comando **init/bin/bash** caricherà il kernel ma assicura che il programma init venga sostituito da una shell bash.

Si otterrà l'accesso a una shell senza dover inserire una password. È quindi possibile procedere all'aggiornamento della password per gli account Linux o ad apportare altre modifiche alla configurazione.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Forzare il kernel a un prompt bash
L'accesso a GRUB consente di interrompere il processo di inizializzazione questa interazione è utile per molte procedure di ripristino.
Se non si dispone di password root e singolo utente richiede di avere una password root, è possibile avviare il kernel sostituendo il programma init con un prompt bash – questo interrupt può essere raggiunto aggiungendo init ./bin/bash alla riga di avvio del kernel

![bash1](./media/virtual-machines-serial-console/bash1.png)

Rimontare l'RW del file system / (radice) utilizzando il comando

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Ora è possibile eseguire la modifica della password radice o molte altre modifiche di configurazione Linux

![bash3](./media/virtual-machines-serial-console/bash3.png)

Riavviare la macchina virtuale con 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modalità utente singolo

In alternativa, potrebbe essere necessario accedere alla macchina virtuale in modalità utente singolo o di emergenza. Selezionare il kernel che si desidera avviare o interrompere utilizzando i tasti freccia.
Accedere alla modalità desiderata aggiungendo la parola chiave **single** o **1** alla riga di avvio del kernel. Nei sistemi RHEL, è anche possibile aggiungere **rd.break**.

Per ulteriori informazioni su come accedere alla modalità utente singolo, vedere [questo documento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Azure Serial ConsoleLearn]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) more about Azure Serial Console
