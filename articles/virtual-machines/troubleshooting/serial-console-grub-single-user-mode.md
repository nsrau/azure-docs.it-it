---
title: Console seriale di Azure per GRUB e modalità utente singolo | Microsoft Docs
description: Questo articolo descrive come usare la console seriale per GRUB in macchine virtuali di Azure.
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
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883921"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Usare la console seriale per accedere a GRUB e alla modalità utente singolo
È probabile che il GRand Unified Bootloader (GRUB) sia il primo elemento visualizzato quando si avvia una macchina virtuale (VM). Poiché viene visualizzato prima dell'avvio del sistema operativo, GRUB non è accessibile tramite SSH. In GRUB è possibile modificare la configurazione di avvio per l'avvio in modalità utente singolo, tra le altre cose.

La modalità utente singolo è un ambiente minimo con funzionalità minime. Può essere utile per analizzare i problemi di avvio, file system problemi o problemi di rete. Un numero minore di servizi può essere eseguito in background e, a seconda del runlevel, una file system potrebbe non essere montata automaticamente.

La modalità utente singolo è utile anche in situazioni in cui la macchina virtuale può essere configurata in modo da accettare solo le chiavi SSH per l'accesso. In questo caso, potrebbe essere possibile usare la modalità utente singolo per creare un account con autenticazione della password. 

> [!NOTE]
> Il servizio console seriale consente solo agli utenti con autorizzazioni di livello *collaboratore* o superiori di accedere alla console seriale di una macchina virtuale.

Per attivare la modalità utente singolo, immettere GRUB quando si avvia la macchina virtuale e modificare la configurazione di avvio in GRUB. Vedere le istruzioni dettagliate per l'immissione di GRUB nella sezione successiva. In generale, se la macchina virtuale è stata configurata per la visualizzazione di GRUB, è possibile usare il pulsante Riavvia nella console seriale della macchina virtuale per riavviare la macchina virtuale e visualizzare GRUB.

![Pulsante Riavvia console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Informazioni generali sull'accesso a GRUB
Per accedere a GRUB, riavviare la macchina virtuale mentre il riquadro della console seriale è aperto. Per alcune distribuzioni è necessario l'input da tastiera per visualizzare GRUB, mentre altre vengono automaticamente visualizzate per alcuni secondi per consentire l'input da tastiera utente per annullare il timeout.

Per poter accedere alla modalità utente singolo, è necessario assicurarsi che GRUB sia abilitato nella macchina virtuale. A seconda della distribuzione, potrebbe essere necessario eseguire alcune operazioni di installazione per assicurarsi che GRUB sia abilitato. Per informazioni specifiche della distribuzione, vedere la sezione successiva e la pagina relativa al [supporto per Linux in Azure](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) .

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Riavviare la macchina virtuale per accedere a GRUB nella console seriale
È possibile riavviare la macchina virtuale all'interno della console seriale passando il puntatore del mouse sul pulsante **Riavvia** e selezionando **Riavvia macchina virtuale**. Nella parte inferiore del riquadro viene visualizzata una notifica relativa al riavvio.

È anche possibile riavviare la macchina virtuale eseguendo un comando SysRq "b" Se [sysrq](./serial-console-nmi-sysrq.md) è abilitato. Per informazioni su cosa aspettarsi da GRUB quando si riavvia, vedere le istruzioni specifiche per la distribuzione nelle sezioni successive.

![Riavvio console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Accesso in modalità utente singolo generale
Potrebbe essere necessario l'accesso manuale alla modalità utente singolo quando non è stato configurato un account con autenticazione della password. Modificare la configurazione di GRUB per immettere manualmente la modalità utente singolo. Al termine dell'operazione, vedere la sezione "utilizzare la modalità utente singolo per reimpostare o aggiungere una password" per ulteriori istruzioni.

Se la macchina virtuale non è in grado di eseguire l'avvio, le distribuzioni vengono spesso rilasciate automaticamente in modalità utente singolo o in modalità di emergenza. Altre distribuzioni, tuttavia, richiedono una configurazione aggiuntiva, ad esempio la configurazione di una password radice, prima che possano essere rilasciate automaticamente in modalità utente singolo o di emergenza.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Usare la modalità utente singolo per reimpostare o aggiungere una password
Dopo aver eseguito la modalità utente singolo, aggiungere un nuovo utente con privilegi sudo eseguendo le operazioni seguenti:
1. Eseguire `useradd <username>` per aggiungere un utente.
1. Eseguire `sudo usermod -a -G sudo <username>` per concedere i nuovi privilegi utente radice.
1. Usare `passwd <username>` per impostare la password per il nuovo utente È quindi possibile accedere come nuovo utente.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Accesso per Red Hat Enterprise Linux (RHEL)
Se RHEL non è in grado di eseguire l'avvio normalmente, la modalità utente singolo viene rilasciata automaticamente. Tuttavia, se non è stato configurato l'accesso alla radice per la modalità utente singolo, non si dispone di una password radice e non è possibile accedere. Esiste una soluzione alternativa (vedere la sezione "immettere manualmente la modalità utente singolo in RHEL"), ma è consigliabile configurare inizialmente l'accesso radice.

### <a name="grub-access-in-rhel"></a>Accesso GRUB in RHEL
RHEL dotata di GRUB abilitata per impostazione predefinita. Per immettere grub, riavviare la VM `sudo reboot`eseguendo, quindi premere un tasto qualsiasi. Verrà visualizzato il riquadro GRUB. In caso contrario, assicurarsi che le righe seguenti siano presenti nel file di GRUB (`/etc/default/grub`):

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
> Red Hat fornisce inoltre la documentazione per l'avvio in modalità di ripristino, in modalità di emergenza o di debug e per la reimpostazione della password radice. Per istruzioni, vedere la pagina relativa alla [modifica del menu Terminal durante l'avvio](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurare l'accesso radice per la modalità utente singolo in RHEL
L'utente root è disabilitato per impostazione predefinita. La modalità utente singolo in RHEL richiede l'abilitazione dell'utente root. Se è necessario abilitare la modalità utente singolo, usare le istruzioni seguenti:

1. Accedere al sistema Red Hat tramite SSH.
1. Passa alla radice.
1. Abilitare la password per l'utente root eseguendo le operazioni seguenti:
    * Eseguire `passwd root` (impostare una password radice avanzata).
1. Assicurarsi che l'utente root possa accedere solo tramite ttyS0 seguendo questa procedura:  
    a. Eseguire `edit /etc/ssh/sshd_config`e verificare che PermitRootLogIn sia impostato su `no`.  
    b. Eseguire `edit /etc/securetty file` per consentire l'accesso solo tramite ttyS0.

A questo punto, se il sistema viene avviato in modalità utente singolo, è possibile accedere con la password radice.

In alternativa, per RHEL 7.4 + o 6.9 +, per abilitare la modalità utente singolo nei prompt di GRUB, vedere [avvio in modalità utente singolo](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Immettere manualmente la modalità utente singolo in RHEL
Se è stato configurato l'accesso a GRUB e alla radice usando le istruzioni precedenti, è possibile immettere la modalità utente singolo eseguendo le operazioni seguenti:

1. Per immettere GRUB, premere ESC quando si riavvia la macchina virtuale.
1. In GRUB premere E per modificare il sistema operativo in cui si vuole eseguire l'avvio. Il sistema operativo è in genere elencato nella prima riga.
1. Trovare la riga del kernel. In Azure inizia con *Linux16*.
1. Premere CTRL + E per passare alla fine della riga.
1. Alla fine della riga, aggiungere *systemd. Unit = Rescue. target*.
    
    Questa azione viene avviata in modalità utente singolo. Se si desidera utilizzare la modalità di emergenza, aggiungere *systemd. Unit = Emergency. target* alla fine della riga, anziché *systemd. Unit = Rescue. target*.

1. Premere CTRL + X per uscire e riavviare con le impostazioni applicate.

   Prima di poter accedere alla modalità utente singolo verrà richiesta la password dell'amministratore. Questa password è quella creata nelle istruzioni precedenti.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Immettere la modalità utente singolo senza account radice abilitato in RHEL
Se l'utente root non è stato abilitato seguendo le istruzioni precedenti, è comunque possibile reimpostare la password radice attenendosi alla seguente procedura:

> [!NOTE]
> Se si usa SELinux, quando si reimposta la password radice, assicurarsi di seguire i passaggi aggiuntivi descritti nella [documentazione di Red Hat](https://aka.ms/rhel7grubterminal).

1. Per immettere GRUB, premere ESC quando si riavvia la macchina virtuale.

1. In GRUB premere E per modificare il sistema operativo in cui si vuole eseguire l'avvio. Il sistema operativo è in genere elencato nella prima riga.
1. Trovare la riga del kernel. In Azure inizia con *Linux16*.
1. Alla fine della riga, aggiungere *Rd. Break* alla fine della riga. Lasciare uno spazio tra la riga del kernel e *Rd. Break*.

    Questa azione interrompe il processo di avvio prima che il controllo venga `initramfs` passato `systemd`da a, come descritto nella [documentazione di Red Hat](https://aka.ms/rhel7rootpassword).
1. Premere CTRL + X per uscire e riavviare con le impostazioni applicate.

   Una volta riavviato, si viene rilasciati in modalità di emergenza con un file system di sola lettura. 
   
1. Nella shell immettere `mount -o remount,rw /sysroot` per rimontare la file system radice con le autorizzazioni di lettura/scrittura.
1. Dopo l' `chroot /sysroot` `sysroot` avvio in modalità utente singolo, immettere per passare alla jail.
1. A questo punto si è alla radice. È possibile reimpostare la password radice immettendo `passwd` e quindi usare le istruzioni precedenti per attivare la modalità utente singolo. 
1. Al termine, immettere `reboot -f` per riavviare il computer.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> L'esecuzione delle istruzioni precedenti consente di passare alla shell di emergenza, in modo da poter eseguire anche attività quali `fstab`la modifica. Tuttavia, in genere è consigliabile reimpostare la password radice e usarla per attivare la modalità utente singolo.

## <a name="access-for-centos"></a>Accesso per CentOS
Analogamente Red Hat Enterprise Linux, la modalità utente singolo in CentOS richiede che GRUB e l'utente root siano abilitati.

### <a name="grub-access-in-centos"></a>Accesso GRUB in CentOS
CentOS dotata di GRUB abilitata per impostazione predefinita. Per immettere grub, riavviare la macchina virtuale immettendo `sudo reboot`e quindi premere un tasto qualsiasi. Questa azione consente di visualizzare il riquadro GRUB.

### <a name="single-user-mode-in-centos"></a>Modalità utente singolo in CentOS
Per abilitare la modalità utente singolo in CentOS, seguire le istruzioni precedenti per RHEL.

## <a name="access-for-ubuntu"></a>Accesso per Ubuntu
Le immagini Ubuntu non richiedono una password radice. Se il sistema viene avviato in modalità utente singolo, è possibile usarlo senza credenziali aggiuntive.

### <a name="grub-access-in-ubuntu"></a>Accesso GRUB in RHEL
Per accedere a GRUB, tenere premuto ESC mentre è in corso l'avvio della macchina virtuale.

Per impostazione predefinita, le immagini Ubuntu potrebbero non visualizzare automaticamente il riquadro GRUB. È possibile modificare l'impostazione eseguendo le operazioni seguenti:
1. In un editor di testo aprire il file */etc/default/grub.d/50-cloudimg-Settings.cfg* .

1. Modificare il `GRUB_TIMEOUT` valore in un valore diverso da zero.
1. In un editor di testo aprire */etc/default/grub*.
1. Impostare come commento `GRUB_HIDDEN_TIMEOUT=1` la riga.
1. Verificare che sia presente una `GRUB_TIMEOUT_STYLE=menu` riga.
1. Eseguire `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Modalità utente singolo in Ubuntu
Se Ubuntu non è in grado di eseguire l'avvio normalmente, la modalità utente singolo viene rilasciata automaticamente. Per attivare la modalità utente singolo manualmente, eseguire le operazioni seguenti:

1. In GRUB premere E per modificare la voce di avvio (voce di Ubuntu).
1. Cercare la riga che inizia con *Linux*e cercare *ro*.
1. Aggiungere *Single* dopo *ro*, assicurandosi che sia presente uno spazio prima e dopo *Single*.
1. Premere CTRL + X per riavviare con queste impostazioni e immettere la modalità utente singolo.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Usare GRUB per richiamare bash in Ubuntu
Dopo aver eseguito le istruzioni precedenti, potrebbe verificarsi una situazione, ad esempio una password radice dimenticata, in cui non è ancora possibile accedere alla modalità utente singolo nella macchina virtuale Ubuntu. È anche possibile indicare al kernel di essere `/bin/bash` eseguito come init, anziché come init di sistema. Questa azione offre una shell bash e consente la manutenzione del sistema. Usare le istruzioni seguenti:

1. In GRUB premere E per modificare la voce di avvio (voce di Ubuntu).

1. Cercare la riga che inizia con *Linux*e cercare *ro*.
1. Sostituire *ro* con *rw init =/bin/bash*.

    Questa azione monta il file System come di lettura e scrittura e USA `/bin/bash` come processo init.
1. Premere CTRL + X per riavviare con queste impostazioni.

## <a name="access-for-coreos"></a>Accesso per CoreOS
Per la modalità utente singolo in CoreOS è necessario abilitare GRUB.

### <a name="grub-access-in-coreos"></a>Accesso GRUB in CoreOS
Per accedere a GRUB, premere un tasto qualsiasi mentre è in corso l'avvio della macchina virtuale.

### <a name="single-user-mode-in-coreos"></a>Modalità utente singolo in CoreOS
Se CoreOS non è in grado di eseguire l'avvio normalmente, la modalità utente singolo viene rilasciata automaticamente. Per attivare la modalità utente singolo manualmente, eseguire le operazioni seguenti:

1. In GRUB premere E per modificare la voce di avvio.

1. Cercare la riga che inizia con *Linux $* . Devono essere presenti due istanze della riga, ognuna incapsulata in un oggetto *if diverso... clausola else* .
1. Aggiungere *CoreOS. autologin = ttyS0* alla fine di ogni riga di *Linux $* .
1. Premere CTRL + X per riavviare con queste impostazioni e immettere la modalità utente singolo.

## <a name="access-for-suse-sles"></a>Accesso per SUSE SLES
Le immagini più recenti di SLES 12 SP3 e consentono l'accesso tramite la console seriale se il sistema viene avviato in modalità di emergenza.

### <a name="grub-access-in-suse-sles"></a>Accesso GRUB in SUSE SLES
L'accesso a GRUB in SLES richiede una configurazione di bootloader tramite YaST. Per creare la configurazione, eseguire le operazioni seguenti:

1. Usare SSH per accedere alla VM SLES, quindi eseguire `sudo yast bootloader`. Premere TAB, premere invio e quindi usare i tasti di direzione per spostarsi nel menu.

1. Passare a **parametri kernel**, quindi selezionare la casella di controllo **Usa console seriale** .
1. Aggiungere `serial --unit=0 --speed=9600 --parity=no` agli argomenti della **console** .
1. Premere F10 per salvare le impostazioni e uscire.
1. Per immettere GRUB, riavviare la macchina virtuale e premere un tasto qualsiasi durante la sequenza di avvio per tenere visualizzato il riquadro GRUB.

    Il timeout predefinito per GRUB è **1S**. È possibile modificare questa impostazione modificando la `GRUB_TIMEOUT` variabile nel file */etc/default/grub* .

![Inizializzazione della configurazione del bootloader](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modalità utente singolo in SUSE SLES
Se SLES non può essere avviato normalmente, viene automaticamente rilasciato nella shell di emergenza. Per immettere manualmente la shell di emergenza, eseguire le operazioni seguenti:

1. In GRUB premere E per modificare la voce di avvio (voce SLES).

1. Cercare la riga del kernel che inizia con *Linux*.
1. Aggiungere *systemd. Unit = Emergency. target* alla fine della riga del kernel.
1. Premere CTRL + X per riavviare con queste impostazioni e immettere la shell di emergenza.

   > [!NOTE]
   > Questa azione rilascia la shell di emergenza con un file system di sola lettura. Per modificare i file, rimontare la file system con autorizzazioni di lettura/scrittura. A tale scopo, immettere `mount -o remount,rw /` nella shell.

## <a name="access-for-oracle-linux"></a>Accesso per Oracle Linux
Analogamente Red Hat Enterprise Linux, la modalità utente singolo in Oracle Linux richiede l'abilitazione di GRUB e dell'utente root.

### <a name="grub-access-in-oracle-linux"></a>Accesso a GRUB in Oracle Linux
Oracle Linux dotata di GRUB abilitata per impostazione predefinita. Per immettere grub, riavviare la VM `sudo reboot`eseguendo, quindi premere ESC. Questa azione consente di visualizzare il riquadro GRUB. Se il riquadro grub non è visualizzato, verificare che il valore della `GRUB_TERMINAL` riga contenga la *console seriale* `GRUB_TERMINAL="serial console"`(ovvero). Ricompilare GRUB `grub2-mkconfig -o /boot/grub/grub.cfg`con.

### <a name="single-user-mode-in-oracle-linux"></a>Modalità utente singolo in Oracle Linux
Per abilitare la modalità utente singolo in Oracle Linux, seguire le istruzioni precedenti per RHEL.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla console seriale, vedere:
* [Documentazione sulla console seriale Linux](serial-console-linux.md)
* [Usare la console seriale per abilitare GRUB in diverse distribuzioni](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Usare la console seriale per le chiamate a NMI e SysRq](serial-console-nmi-sysrq.md)
* [Console seriale per macchine virtuali Windows](serial-console-windows.md)
* [Diagnostica di avvio](boot-diagnostics.md)
