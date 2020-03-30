---
title: Console seriale di Azure per GRUB e la modalità per utente singolo Documenti Microsoft
description: Questo articolo descrive come usare la console seriale per GRUB nelle macchine virtuali di Azure.This article describes how to use Serial Console for GRUB in Azure virtual machines.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883921"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Utilizzare la console seriale per accedere alla modalità GRUB e alla modalità utente singolo
GRand Unified Bootloader (GRUB) è probabilmente la prima cosa che si vede quando si avvia una macchina virtuale (VM). Poiché viene visualizzato prima dell'avvio del sistema operativo, GRUB non è accessibile tramite SSH. In GRUB, è possibile modificare la configurazione di avvio per l'avvio in modalità utente singolo, tra le altre cose.

La modalità utente singolo è un ambiente minimo con funzionalità minime. Può essere utile per analizzare i problemi di avvio, i problemi del file system o i problemi di rete. Un numero inferiore di servizi può essere eseguito in background e, a seconda del runlevel, un file system potrebbe anche non essere montato automaticamente.

La modalità utente singolo è utile anche nelle situazioni in cui la macchina virtuale potrebbe essere configurata per accettare solo le chiavi SSH per l'accesso. In questo caso, potrebbe essere possibile utilizzare la modalità utente singolo per creare un account con autenticazione tramite password. 

> [!NOTE]
> Il servizio Console seriale consente solo agli utenti con autorizzazioni di *collaboratore* o superiori di accedere alla console seriale di una macchina virtuale.

Per attivare la modalità utente singolo, immettere GRUB durante l'avvio della macchina virtuale e modificare la configurazione di avvio in GRUB. Vedere le istruzioni dettagliate per l'immissione di GRUB nella sezione successiva. In generale, se la macchina virtuale è stata configurata per visualizzare GRUB, è possibile usare il pulsante di riavvio all'interno della console seriale della macchina virtuale per riavviare la macchina virtuale e visualizzare GRUB.

![Il pulsante Riavvia della console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Informazioni generali sull'accesso a GRUB
Per accedere a GRUB, riavviare la macchina virtuale mentre il riquadro Console seriale è aperto. Alcune distribuzioni richiedono l'input da tastiera per visualizzare GRUB, mentre altre mostrano automaticamente GRUB per alcuni secondi per consentire all'input da tastiera dell'utente di annullare il timeout.

Per poter accedere alla modalità utente singolo, è necessario assicurarsi che GRUB sia abilitato nella macchina virtuale. A seconda della distribuzione, potrebbe essere necessario alcune operazioni di configurazione per garantire che GRUB sia abilitato. Per informazioni specifiche della distribuzione, vedere la sezione successiva e la pagina [Supporto per Linux in Azure.For](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) distribution-specific information, see the next section and our Support for Linux on Azure page.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Riavviare la macchina virtuale per accedere a GRUB nella console seriale
È possibile riavviare la macchina virtuale all'interno della Console seriale passando il puntatore del mouse sul pulsante **Riavvia** e selezionando **Riavvia macchina virtuale**. Nella parte inferiore del riquadro viene visualizzata una notifica relativa al riavvio.

È inoltre possibile riavviare la macchina virtuale eseguendo un comando SysRq "b" se [SysRq](./serial-console-nmi-sysrq.md) è abilitato. Per informazioni su cosa aspettarsi da GRUB al riavvio, vedere le istruzioni specifiche della distribuzione nelle sezioni successive.

![Riavvio della console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Accesso generale in modalità utente singolo
Potrebbe essere necessario l'accesso manuale alla modalità utente singolo quando non è stato configurato un account con l'autenticazione tramite password. Modificare la configurazione GRUB per accedere manualmente alla modalità utente singolo. Dopo aver eseguito questa operazione, vedere la sezione "Utilizzare la modalità utente singolo per reimpostare o aggiungere una password" per ulteriori istruzioni.

Se la macchina virtuale non è in grado di avviare l'avvio, le distribuzioni spesso rilasciano automaticamente l'utente in modalità utente singolo o in modalità di emergenza. Altre distribuzioni, tuttavia, richiedono una configurazione aggiuntiva, ad esempio la configurazione di una password root, prima che possano passare automaticamente alla modalità per utente singolo o di emergenza.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Utilizzare la modalità utente singolo per reimpostare o aggiungere una password
Dopo la modalità utente singolo, aggiungere un nuovo utente con privilegi sudo eseguendo le operazioni seguenti:
1. Esegui `useradd <username>` per aggiungere un utente.
1. Esegui `sudo usermod -a -G sudo <username>` per concedere i nuovi privilegi di root utente.
1. Usare `passwd <username>` per impostare la password per il nuovo utente È quindi possibile accedere come nuovo utente.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Accesso per Red Hat Enterprise Linux (RHEL)
Se RHEL non può avviarsi normalmente, si scende automaticamente in modalità utente singolo. Tuttavia, se non è stato configurato l'accesso root per la modalità utente singolo, non si dispone di una password radice e non è possibile accedere. Esiste una soluzione alternativa (vedere la sezione "Immettere manualmente la modalità utente singolo in RHEL"), ma si consiglia di impostare inizialmente l'accesso root.

### <a name="grub-access-in-rhel"></a>Accesso GRUB in RHEL
RHEL dotata di GRUB abilitata per impostazione predefinita. Per immettere GRUB, riavviare la macchina virtuale eseguendo `sudo reboot`, quindi premere un tasto qualsiasi. Verrà visualizzato il riquadro GRUB. In caso contrario, verificare che nel file GRUB`/etc/default/grub`siano presenti le seguenti righe ( ):

**Per RHEL 8**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Per RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat fornisce anche la documentazione per l'avvio in modalità Rescue, Modalità di emergenza o Modalità Debug e per la reimpostazione della password root. Per istruzioni, consultate [Modifica del menu Terminale durante l'avvio.](https://aka.ms/rhel7grubterminal)

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurare l'accesso root per la modalità utente singolo in RHELSet up root access for single-user mode in RHEL
L'utente root è disabilitato per impostazione predefinita. La modalità utente singolo in RHEL richiede l'abilitazione dell'utente root. Se è necessario abilitare la modalità utente singolo, utilizzare le istruzioni seguenti:

1. Accedi al sistema Red Hat tramite SSH.
1. Passare alla radice.
1. Abilitare la password per l'utente root eseguendo le operazioni seguenti:
    * Esegui `passwd root` (impostare una password root complessa).
1. Assicurarsi che l'utente root possa accedere solo tramite ttyS0 eseguendo le operazioni seguenti:  
    a. Eseguire `edit /etc/ssh/sshd_config`e verificare che PermitRootLogIn sia impostato su `no`.  
    b. Esegui `edit /etc/securetty file` per consentire l'accesso solo tramite ttyS0.

A questo punto, se il sistema viene avviato in modalità utente singolo, è possibile accedere con la password radice.

In alternativa, per RHEL 7.4 o 6.9, per abilitare la modalità utente singolo nei prompt di GRUB, vedere [Avvio in modalità utente singolo](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Immettere manualmente la modalità utente singolo in RHELManually enter single-user mode in RHEL
Se hai configurato GRUB e l'accesso root utilizzando le istruzioni precedenti, puoi attivare la modalità utente singolo eseguendo le operazioni seguenti:

1. Per immettere GRUB, premere ESC quando si riavvia la macchina virtuale.
1. In GRUB premere E per modificare il sistema operativo in cui si desidera eseguire l'avvio. Il sistema operativo è solitamente elencato sulla prima riga.
1. Trovare la riga del kernel. In Azure, inizia con *linux16*.
1. Premete Ctrl-E per andare alla fine della riga.
1. Alla fine della riga, aggiungere *systemd.unit-rescue.target*.
    
    Questa azione consente di eseguire la modalità utente singolo. Se si desidera utilizzare la modalità di emergenza, aggiungere *systemd.unit.emergency.target* alla fine della riga (invece di *systemd.unit-rescue.target*).

1. Per uscire e riavviare con le impostazioni applicate, premete Ctrl-X.

   Prima di poter accedere alla modalità utente singolo, verrà richiesta la password dell'amministratore. Questa password è quella creata nelle istruzioni precedenti.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Entra in modalità utente singolo senza account root abilitato in RHEL
Se l'utente root non è stato abilitato seguendo le istruzioni riportate in precedenza, è comunque possibile reimpostare la password radice eseguendo le operazioni seguenti:

> [!NOTE]
> Se si utilizza SELinux, quando si reimposta la password principale, assicurarsi di seguire i passaggi aggiuntivi descritti nella [documentazione Red Hat](https://aka.ms/rhel7grubterminal).

1. Per immettere GRUB, premere ESC quando si riavvia la macchina virtuale.

1. In GRUB premere E per modificare il sistema operativo in cui si desidera eseguire l'avvio. Il sistema operativo è solitamente elencato sulla prima riga.
1. Trovare la riga del kernel. In Azure, inizia con *linux16*.
1. Alla fine della riga, aggiungere *rd.break* alla fine della riga. Lasciare uno spazio tra la linea del kernel e *rd.break*.

    Questa azione interrompe il processo di `initramfs` avvio prima che il controllo venga passato da a , `systemd`come descritto nella [documentazione di Red Hat](https://aka.ms/rhel7rootpassword).
1. Per uscire e riavviare con le impostazioni applicate, premete Ctrl-X.

   Dopo il riavvio, si è passati alla modalità di emergenza con un file system di sola lettura. 
   
1. Nella shell, `mount -o remount,rw /sysroot` immettere per rimontare il file system radice con autorizzazioni di lettura/scrittura.
1. Dopo l'avvio in modalità utente singolo, immettere `chroot /sysroot` per passare alla `sysroot` prigione.
1. Ora sei alla radice. È possibile reimpostare la `passwd` password root immettendo e quindi utilizzare le istruzioni precedenti per accedere alla modalità utente singolo. 
1. Al termine, passare `reboot -f` al riavvio.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> L'esecuzione delle istruzioni precedenti elimina l'utente nella shell di emergenza in modo da poter eseguire anche attività quali la modifica. `fstab` Tuttavia, in genere ti consigliamo di reimpostare la password root e utilizzarla per entrare in modalità utente singolo.

## <a name="access-for-centos"></a>Accesso per CentOS
Proprio come Red Hat Enterprise Linux, la modalità utente singolo in CentOS richiede GRUB e l'utente root da abilitare.

### <a name="grub-access-in-centos"></a>Accesso GRUB in CentOS
CentOS dotata di GRUB abilitata per impostazione predefinita. Per immettere GRUB, riavviare la macchina virtuale immettendo `sudo reboot`, quindi premere un tasto qualsiasi. Questa azione consente di visualizzare il riquadro GRUB.

### <a name="single-user-mode-in-centos"></a>Modalità utente singolo in CentOS
Per abilitare la modalità utente singolo in CentOS, seguire le istruzioni precedenti per RHEL.

## <a name="access-for-ubuntu"></a>Accesso per Ubuntu
Le immagini Ubuntu non richiedono una password root. Se il sistema viene avviato in modalità utente singolo, è possibile utilizzarlo senza credenziali aggiuntive.

### <a name="grub-access-in-ubuntu"></a>Accesso GRUB in RHEL
Per accedere a GRUB, tenere premuto Esc durante l'avvio della macchina virtuale.

Per impostazione predefinita, le immagini Ubuntu potrebbero non visualizzare automaticamente il riquadro GRUB. È possibile modificare l'impostazione effettuando le seguenti operazioni:
1. In un editor di testo, aprire il file */etc/default/grub.d/50-cloudimg-settings.cfg.*

1. Modificare `GRUB_TIMEOUT` il valore in un valore diverso da zero.
1. In un editor di testo, aprire */etc/default/grub*.
1. Impostare `GRUB_HIDDEN_TIMEOUT=1` come commento la riga.
1. Assicurarsi che ci `GRUB_TIMEOUT_STYLE=menu` sia una linea.
1. Eseguire `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Modalità utente singolo in Ubuntu
Se Ubuntu non può avviarsi normalmente, si rilascia automaticamente in modalità utente singolo. Per attivare manualmente la modalità utente singolo, procedere come segue:

1. In GRUB, premere E per modificare la voce di avvio (la voce Ubuntu).
1. Cercare la riga che inizia con *linux*, quindi cercare *ro*.
1. Aggiungere *single* dopo *ro*, assicurandosi che vi sia uno spazio prima e dopo *il singolo*.
1. Premete Ctrl-X per riavviare con queste impostazioni e accedere alla modalità per utente singolo.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Usare GRUB per invocare bash in UbuntuUse GRUB to invoke bash in Ubuntu
Dopo aver provato le istruzioni precedenti, potrebbe esserci una situazione (ad esempio una password root dimenticata) in cui non è ancora possibile accedere alla modalità per utente singolo nella macchina virtuale Ubuntu. È anche possibile indicare `/bin/bash` al kernel di eseguire come init, anziché il sistema init. Questa azione consente una shell bash e consente la manutenzione del sistema. Usare le istruzioni seguenti:

1. In GRUB, premere E per modificare la voce di avvio (la voce Ubuntu).

1. Cercare la riga che inizia con *linux*, quindi cercare *ro*.
1. Sostituire *ro con* *rw init/bin/bash*.

    Questa azione monta il file system `/bin/bash` come lettura-scrittura e viene utilizzato come processo init.
1. Premete Ctrl-X per riavviare con queste impostazioni.

## <a name="access-for-coreos"></a>Accesso per CoreOS
La modalità utente singolo in CoreOS richiede l'abilitazione di GRUB.

### <a name="grub-access-in-coreos"></a>Accesso GRUB in CoreOS
Per accedere a GRUB, premere un tasto qualsiasi durante l'avvio della macchina virtuale.

### <a name="single-user-mode-in-coreos"></a>Modalità utente singolo in CoreOS
Se CoreOS non può avviarsi normalmente, si scende automaticamente in modalità utente singolo. Per attivare manualmente la modalità utente singolo, procedere come segue:

1. In GRUB premere E per modificare la voce di avvio.

1. Cercare la riga che inizia con *linux*. Ci dovrebbero essere due istanze della linea, ognuna incapsulata in un *diverso se... *clausola else.
1. Aggiungere *coreos.autologin-ttyS0* alla fine di ogni riga *linux.*
1. Premete Ctrl-X per riavviare con queste impostazioni e accedere alla modalità per utente singolo.

## <a name="access-for-suse-sles"></a>Accesso per SUSE SLES
Le immagini più recenti di SLES 12 SP3 consentono l'accesso tramite la console seriale se il sistema viene avviato in modalità di emergenza.

### <a name="grub-access-in-suse-sles"></a>Accesso GRUB in SUSE SLES
L'accesso GRUB in SLES richiede una configurazione bootloader tramite YaST. Per creare la configurazione, procedere come segue:

1. Utilizzare SSH per accedere alla macchina virtuale SLES e quindi eseguire `sudo yast bootloader`. Premere TAB, premere INVIO e quindi usare i tasti di direzione per spostarsi all'interno del menu.

1. Passare a **Parametri kernel**, quindi selezionare la casella di controllo Usa **console seriale** .
1. Aggiungere `serial --unit=0 --speed=9600 --parity=no` agli argomenti **della console.**
1. Premere F10 per salvare le impostazioni e uscire.
1. Per immettere GRUB, riavviare la macchina virtuale e premere un tasto qualsiasi durante la sequenza di avvio per visualizzare il riquadro GRUB.

    Il timeout predefinito per GRUB è **1s**. È possibile modificare questa `GRUB_TIMEOUT` impostazione modificando la variabile nel file */etc/default/grub.*

![Inizializzazione della configurazione del bootloader](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modalità utente singolo in SUSE SLES
Se SLES non può avviarsi normalmente, si viene automaticamente lasciati nel guscio di emergenza. Per immettere manualmente la shell di emergenza, procedere come segue:

1. In GRUB premere E per modificare la voce di avvio (la voce SLES).

1. Cercare la riga del kernel che inizia con *linux*.
1. Accodare *systemd.unit.emergency.target* alla fine della riga del kernel.
1. Premete Ctrl-X per riavviare con queste impostazioni e immettere la shell di emergenza.

   > [!NOTE]
   > Questa azione consente di trascinare l'utente nella shell di emergenza con un file system di sola lettura. Per modificare i file, rimontare il file system con autorizzazioni di lettura/scrittura. A tale scopo, immettere `mount -o remount,rw /` nella shell.

## <a name="access-for-oracle-linux"></a>Accesso per Oracle Linux
Proprio come Red Hat Enterprise Linux, la modalità utente singolo in Oracle Linux richiede GRUB e l'utente root da abilitare.

### <a name="grub-access-in-oracle-linux"></a>Accesso a GRUB in Oracle Linux
Oracle Linux dotata di GRUB abilitata per impostazione predefinita. Per immettere GRUB, riavviare la macchina virtuale eseguendo `sudo reboot`, quindi premere ESC. Questa azione consente di visualizzare il riquadro GRUB. Se il riquadro GRUB non è visualizzato, `GRUB_TERMINAL` assicurarsi che il valore `GRUB_TERMINAL="serial console"`della riga contenga una console *seriale,* ovvero un'opzione . Ricompilare `grub2-mkconfig -o /boot/grub/grub.cfg`GRUB con .

### <a name="single-user-mode-in-oracle-linux"></a>Modalità utente singolo in Oracle Linux
Per abilitare la modalità utente singolo in Oracle Linux, seguire le istruzioni precedenti per RHEL.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla console seriale, vedere:To learn more about Serial Console, see:
* [Documentazione sulla console seriale Linux](serial-console-linux.md)
* [Utilizzare la console seriale per abilitare GRUB in varie distribuzioni](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Utilizzare la console di serializzazione per le chiamate NMI e SysRq](serial-console-nmi-sysrq.md)
* [Console seriale per macchine virtuali Windows](serial-console-windows.md)
* [Diagnostica di avvio](boot-diagnostics.md)
