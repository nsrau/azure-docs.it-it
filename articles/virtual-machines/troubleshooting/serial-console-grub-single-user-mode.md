---
title: Azure Console seriale per la modalità utente singolo e GRUB | Microsoft Docs
description: Uso della console seriale per grub nelle macchine virtuali di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: alsin
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
ms.openlocfilehash: 47a97d842822ed3d6c8c1583808552c1b2d1d53e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47412556"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Usare la Console seriale per accedere a GRUB e alla modalità utente singolo
La modalità utente singolo è un ambiente minimo con funzionalità minime. Può essere utile per analizzare eventuali problemi di avvio o problemi di rete poiché un minor numero di servizi possono essere eseguiti in background e, a seconda del livello di esecuzione, un file System potrebbe non essere montato automaticamente. Ciò è utile per individuare situazioni, ad esempio un file System danneggiato, un fstab interrotto, o (configurazione di iptables errato) di connettività di rete.

Alcune distribuzioni portano automaticamente in modalità utente singolo o in modalità di emergenza se la macchina virtuale non è in grado di eseguire l'avvio. Altri utenti, tuttavia, richiedono una configurazione aggiuntiva prima di passare in modalità utente singolo o emergenza automaticamente.

È opportuno verificare che GRUB sia abilitato nella macchina virtuale per poter essere in grado di accedere alla modalità utente singolo. A seconda del tipo di distribuzione, potrebbero essere necessarie alcune operazioni di configurazione per assicurarsi che GRUB sia abilitato. 


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Accesso per Red Hat Enterprise Linux (RHEL)
RHEL passerà in modalità utente singolo automaticamente se è impossibile avviare normalmente. Tuttavia, se non è stato configurato l'accesso alla radice per la modalità utente singolo, non avrà una password radice e non sarà in grado di accedere. È disponibile una soluzione (vedere “Accedere manualmente alla modalità utente singolo” qui sotto), ma il suggerimento consiste nell'impostare inizialmente l'accesso alla radice.

### <a name="grub-access-in-rhel"></a>Accesso GRUB in RHEL
RHEL dotata di GRUB abilitata per impostazione predefinita. Per immettere GRUB, riavviare la macchina virtuale con `sudo reboot` e premere un tasto qualsiasi. Verrà visualizzata la schermata GRUB visualizzati.

> Nota: Red Hat fornisce anche la documentazione per l'avvio in modalità di ripristino, modalità di emergenza, modalità di Debug e la reimpostazione della password radice. [Fai clic qui per accedere](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurare l'accesso radice per la modalità utente singolo in RHEL
La modalità utente singolo in Red Hat richiede che sia abilitato l'utente ROOT, che è disabilitato per impostazione predefinita. Per abilitare la modalità utente singolo, seguire le istruzioni seguenti:

1. Accedere al sistema Red Hat tramite SSH
1. Passa alla radice
1. Abilitare la password per l'utente ROOT 
    * `passwd root` (impostare una password complessa per l'utente ROOT)
1. Verificare che l'utente ROOT possa eseguire l'accesso solo tramite ttyS0
    * `edit /etc/ssh/sshd_config` e assicurarsi che l'opzione PermitRootLogin sia impostata su no
    * `edit /etc/securetty file` per consentire l'accesso solo tramite ttyS0 

Se il sistema viene ora avviato in modalità utente singolo, è possibile accedervi tramite la password ROOT.

In alternativa per RHEL 7.4+ o 6.9+ è possibile abilitare la modalità utente singolo nei prompt GRUB, come illustrato nelle istruzioni riportate [in questa pagina](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Immettere manualmente la modalità utente singolo in RHEL
Se è stata impostata una radice e GRUB accedere con le istruzioni riportate sopra, quindi è possibile immettere la modalità utente singolo con le istruzioni seguenti:

1. Premere “Esc” durante il riavvio per immettere GRUB nella macchina virtuale
1. In GRUB, premere “e” per modificare il sistema operativo selezionato di cui si desidera eseguire l'avvio (in genere la prima riga)
1. Trovare la riga del kernel; in Azure inizia con `linux16`
1. Premere Ctrl + E per passare alla fine della riga
1. Aggiungere quanto segue alla fine della riga: `systemd.unit=rescue.target`
    * Consente di avviare in modalità utente singolo. Se si desidera utilizzare la modalità di emergenza, aggiungere `systemd.unit=emergency.target` alla fine della riga di anziché `systemd.unit=rescue.target`
1. Premere Ctrl + X per uscire e riavviare il sistema con le impostazioni applicate
1. Verrà richiesta la password dell'amministratore prima di attivare la modalità utente singolo; questa è la stessa password creata nelle istruzioni riportate sopra    

    ![](/media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Accedere alla modalità utente singolo senza account radice abilitato in RHEL
Se non sono stati esaminati i passaggi sopra per abilitare l'utente root, è ancora possibile reimpostare la password radice. Usare le istruzioni seguenti:

> Nota: Se si usa SELinux, assicurarsi di aver eseguito i passaggi aggiuntivi descritti nella documentazione di Red Hat [qui](https://aka.ms/rhel7grubterminal) quando si reimposta la password radice.

1. Premere “Esc” durante il riavvio per immettere GRUB nella macchina virtuale
1. In GRUB, premere “e” per modificare il sistema operativo selezionato di cui si desidera eseguire l'avvio (in genere la prima riga)
1. Trovare la riga del kernel; in Azure inizia con `linux16`
1. Aggiungere `rd.break` alla fine della riga, assicurarsi che ci sia uno spazio prima `rd.break` (vedere l'esempio riportato di seguito)
    - Verrà interrotto il processo di avvio prima che il controllo sia passato da `initramfs` a `systemd`, come descritto nella documentazione di Red Hat [qui](https://aka.ms/rhel7rootpassword).
1. Premere Ctrl + X per uscire e riavviare il sistema con le impostazioni applicate
1. Eseguito l'avvio, si passa alla modalità di emergenza con un sistema di file di sola lettura. Immettere `mount -o remount,rw /sysroot` nella shell pe rrimonatre il file system radice con autorizzazioni di lettura/scrittura
1. Dopo aver avviato in modalità utente singolo, digitare `chroot /sysroot` per passare al `sysroot` jail
1. Root eseguito. È possibile reimpostare la password radice con `passwd` e quindi usare le istruzioni riportate sopra per attivare la modalità utente singolo. Digitare `reboot -f` per riavviare al termine.

![](/media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Nota: tramite l’esecuzione delle istruzioni riportate si passerà alla shell di emergenza, in modo che sia anche possibile eseguire attività quali la modifica `fstab`. Tuttavia, il suggerimento generalmente accettato consiste nel reimpostare la password radice e usarla per accedere alla modalità utente singolo. 


## <a name="access-for-centos"></a>Accesso per CentOS
Red Hat Enterprise Linux, modalità utente singolo in CentOS richiede GRUB e l'utente root deve essere abilitato. 

### <a name="grub-access-in-centos"></a>Accesso GRUB in CentOS
CentOS dotata di GRUB abilitata per impostazione predefinita. Per immettere GRUB, riavviare la macchina virtuale con `sudo reboot` e premere un tasto qualsiasi. Verrà visualizzata la schermata GRUB visualizzati.

### <a name="single-user-mode-in-centos"></a>Modalità utente singolo in CentOS
Seguire le istruzioni per RHEL sopra per abilitare la modalità utente singolo in CentOS.

## <a name="access-for-ubuntu"></a>Accesso per Ubuntu 
Le immagini di Ubuntu non richiedono una password radice. Se il sistema viene ora avviato in modalità utente singolo, è possibile usarlo senza credenziali aggiuntive. 

### <a name="grub-access-in-ubuntu"></a>Accesso GRUB in RHEL
Per accedere a GRUB, premere e tenere premuto “Esc” durante l'avvio della macchina virtuale di backup.

### <a name="single-user-mode-in-ubuntu"></a>Modalità utente singolo in Ubuntu
Ubuntu passerà in modalità utente singolo automaticamente se è impossibile avviare normalmente. Per accedere manualmente alla modalità utente singolo, seguire le istruzioni seguenti:

1. Da GRUB, premere “e” per modificare la voce di avvio (la voce di Ubuntu)
1. Cercare la riga che inizia con `linux`, quindi cercare `ro`
1. Aggiungere `single` dopo `ro`, assicurandosi che sia presente uno spazio prima e dopo `single`
1. Premere Ctrl + X per riavviare il sistema con queste impostazioni e attivare la modalità utente singolo

## <a name="access-for-coreos"></a>Accesso per CoreOS
La modalità utente singolo in CoreOS richiede GRUB abilitato. 

### <a name="grub-access-in-coreos"></a>Accesso GRUB in CoreOS
Per accedere a GRUB, premere un tasto quando la macchina virtuale è in fase di avvio.

### <a name="single-user-mode-in-coreos"></a>Modalità utente singolo in CoreOS
CoreOS passerà in modalità utente singolo automaticamente se è impossibile avviare normalmente. Per accedere manualmente alla modalità utente singolo, seguire le istruzioni seguenti:
1. Da GRUB, premere “e” per modificare la voce di avvio
1. Cercare la riga che inizia con `linux$`. Dovrebbero essercene 2, incapsulate in diversi clausole if/else
1. Aggiungere `coreos.autologin=ttyS0` alla fine di entrambe le righe `linux$`
1. Premere Ctrl + X per riavviare il sistema con queste impostazioni e attivare la modalità utente singolo

## <a name="access-for-suse-sles"></a>Accesso per SUSE SLES
Le immagini di SLES 12 SP3+ più recenti consentono l'accesso tramite la console seriale nel caso in cui il sistema venga avviato in modalità di emergenza. 

### <a name="grub-access-in-suse-sles"></a>Accesso GRUB in SUSE SLES
L’accesso GRUB in SLES richiede la configurazione del caricatore di avvio tramite YaST. A questo scopo, seguire queste istruzioni:

1. SSH in una VM SLES ed eseguire `sudo yast bootloader`. Usare la chiave `tab`, chiave `enter` e i tasti di direzione per spostarsi tra i menu. 
1. Passare a `Kernel Parameters` e controllare `Use serial console`. 
1. Aggiungere `serial --unit=0 --speed=9600 --parity=no` agli argomenti della Console

1. Premere F10 per salvare le impostazioni e chiudere
1. Per immettere GRUB, riavviare la macchina virtuale e premere un tasto qualsiasi durante la sequenza di avvio per far restare GRUB nella schermata
    - Il timeout predefinito per GRUB è di 1 secondo. È possibile modificare questo valore modificando la variabile `GRUB_TIMEOUT` in `/etc/default/grub`

![](/media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modalità utente singolo in SUSE SLES
Si passerà automaticamente alla shell di emergenza se non è possibile avviare normalmente SLES. Per accedere manualmente alla modalità utente singolo, seguire le istruzioni seguenti:

1. Da GRUB, premere “e” per modificare la voce di avvio (la voce di SLES)
1. Cercare la riga del kernel che verrà avviata con `linux`
1. Aggiungere `systemd.unit=emergency.target` alla fine della riga
1. Premere Ctrl + X per riavviare il sistema con queste impostazioni e accedere alla shell di emergenza
> Si passerà alla shell di emergenza con file system di _sola lettura_. Se si vuole apportare le modifiche ai file, è necessario rimontare il file system con autorizzazioni di lettura/scrittura. A tale scopo, immettere `mount -o remount,rw /` nella shell

## <a name="access-for-oracle-linux"></a>Accesso per Oracle Linux
Red Hat Enterprise Linux, modalità utente singolo in Oracle Linux richiede GRUB e l'utente root deve essere abilitato. 

### <a name="grub-access-in-oracle-linux"></a>Accesso a GRUB in Oracle Linux
Oracle Linux dotata di GRUB abilitata per impostazione predefinita. Per accedere a GRUB, riavviare la macchina virtuale con `sudo reboot` e premere “Esc”. Verrà visualizzata la schermata GRUB visualizzati.

### <a name="single-user-mode-in-oracle-linux"></a>Modalità utente singolo in Oracle Linux
Seguire le istruzioni per RHEL sopra per abilitare la modalità utente singolo in Oracle Linux.

## <a name="next-steps"></a>Passaggi successivi
* La pagina principale della documentazione di Linux relativa alla console seriale è disponibile [qui](serial-console-linux.md).
* Utilizzare la console seriale per [NMI e SysRq chiamate](serial-console-nmi-sysrq.md)
* La console seriale è disponibile anche per macchine virtuali [Windows](serial-console-windows.md)
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md)