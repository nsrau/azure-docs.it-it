---
title: Azure Console seriale per la modalità utente singolo e GRUB | Microsoft Docs
description: Uso della console seriale per grub nelle macchine virtuali di Azure.
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
ms.openlocfilehash: 135741a8bf385388fa1b3ac75a45e4c4678bf196
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814471"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Usare la Console seriale per accedere a GRUB e alla modalità utente singolo
GRUB (GRand Unified Bootloader) è probabilmente la prima funzionalità che viene visualizzata quando si avvia una macchina virtuale. Poiché viene visualizzata prima dell'avvio del sistema operativo, non è accessibile tramite SSH. Da GRUB è possibile, tra le altre cose, modificare la configurazione di avvio per eseguire l'avvio in modalità utente singolo.

La modalità utente singolo è un ambiente minimo con funzionalità minime. Può essere utile per analizzare i problemi relativi all'avvio, al file system o alla rete. In questa modalità è infatti possibile che vengano eseguiti meno servizi in background e che, a seconda del livello di esecuzione, non venga nemmeno montato automaticamente un file system.

La modalità utente singolo è utile anche nelle situazioni in cui la macchina virtuale può essere configurata solo in modo da accettare chiavi SSH per l'accesso. In questo caso, è possibile usare la modalità utente singolo per creare un account con autenticazione della password. Si noti che il servizio console seriale consente l'accesso alla console seriale di una macchina virtuale solo agli utenti con accesso a livello di collaboratore o superiore.

Per attivare la modalità utente singolo, è necessario accedere a GRUB durante la fase di avvio della macchina virtuale e modificare la configurazione di avvio in GRUB. Di seguito sono fornite istruzioni dettagliate per l'accesso a GRUB. È in genere possibile usare il pulsante di riavvio all'interno della console seriale della macchina virtuale per riavviare quest'ultima e visualizzare GRUB, se la macchina virtuale è stata configurata a questo scopo.

![Pulsante di riavvio della console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Informazioni generali sull'accesso a GRUB
Per accedere a GRUB, è necessario riavviare la macchina virtuale mantenendo aperto il pannello della console seriale. Per mostrare GRUB alcune distribuzioni richiedono l'input da tastiera, mentre altre distribuzioni mostrano GRUB automaticamente per alcuni secondi e consentono l'input da tastiera dell'utente per annullare il timeout.

È opportuno verificare che GRUB sia abilitato nella macchina virtuale per poter essere in grado di accedere alla modalità utente singolo. A seconda del tipo di distribuzione, potrebbero essere necessarie alcune operazioni di configurazione per assicurarsi che GRUB sia abilitato. Informazioni specifiche per le distribuzioni sono disponibili di seguito e in [questo collegamento](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Riavviare la macchina virtuale per accedere a GRUB nella console seriale
È possibile riavviare la macchina virtuale all'interno della console seriale passando al pulsante di alimentazione e facendo clic su "Restart VM" (Riavvia VM). La macchina virtuale verrà riavviata e nel portale di Azure verrà visualizzata la notifica del riavvio.
Il riavvio della macchina virtuale può essere eseguito anche con il comando SysRq `'b'`, se [SysRq](./serial-console-nmi-sysrq.md) è abilitato. Per informazioni sul comportamento di GRUB al riavvio, seguire le istruzioni specifiche per le distribuzioni riportate di seguito.

![Riavvio della console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Informazioni generali sull'accesso alla modalità utente singolo
L'accesso manuale alla modalità utente singolo può essere necessario nelle situazioni in cui non è stato configurato un account con autenticazione della password. Per attivare manualmente la modalità utente singolo è necessario modificare la configurazione di GRUB. Al termine di questa operazione, consultare la sezione Usare la modalità utente singolo per reimpostare o aggiungere una password per altre istruzioni.

Nei casi in cui la macchina virtuale non è in grado di eseguire l'avvio, alcune distribuzioni attivano automaticamente la modalità utente singolo o la modalità di emergenza. Altre distribuzioni, invece, prima di attivare automaticamente queste modalità, richiedono una configurazione aggiuntiva, ad esempio la configurazione di una password per l'utente ROOT.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Usare la modalità utente singolo per reimpostare o aggiungere una password
Con la modalità utente singolo attiva, eseguire il comando seguente per aggiungere un nuovo utente con privilegi sudo:
1. Eseguire `useradd <username>` per aggiungere un utente
1. Eseguire `sudo usermod -a -G sudo <username>` per concedere al nuovo utente i privilegi di ROOT
1. Usare `passwd <username>` per impostare la password per il nuovo utente in modo da poter accedere come nuovo utente


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Accesso per Red Hat Enterprise Linux (RHEL)
RHEL passerà in modalità utente singolo automaticamente se è impossibile avviare normalmente. Tuttavia, se non è stato configurato l'accesso alla radice per la modalità utente singolo, non avrà una password radice e non sarà in grado di accedere. È disponibile una soluzione (vedere “Accedere manualmente alla modalità utente singolo” qui sotto), ma il suggerimento consiste nell'impostare inizialmente l'accesso alla radice.

### <a name="grub-access-in-rhel"></a>Accesso GRUB in RHEL
RHEL dotata di GRUB abilitata per impostazione predefinita. Per immettere GRUB, riavviare la macchina virtuale con `sudo reboot` e premere un tasto qualsiasi. Verrà visualizzata la schermata GRUB visualizzati.

> Note: Red Hat fornisce anche la documentazione per l'avvio in modalità di ripristino, modalità di emergenza, modalità di debug e la reimpostazione della password radice. [Fai clic qui per accedere](https://aka.ms/rhel7grubterminal).

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

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Accedere alla modalità utente singolo senza account radice abilitato in RHEL
Se non sono stati esaminati i passaggi sopra per abilitare l'utente root, è ancora possibile reimpostare la password radice. Usare le istruzioni seguenti:

> Note: Se si usa SELinux, assicurarsi di aver eseguito i passaggi aggiuntivi descritti nella documentazione di Red Hat [qui](https://aka.ms/rhel7grubterminal) durante la reimpostazione della password radice.

1. Premere “Esc” durante il riavvio per immettere GRUB nella macchina virtuale
1. In GRUB, premere “e” per modificare il sistema operativo selezionato di cui si desidera eseguire l'avvio (in genere la prima riga)
1. Trovare la riga del kernel; in Azure inizia con `linux16`
1. Aggiungere `rd.break` alla fine della riga, assicurarsi che ci sia uno spazio prima `rd.break` (vedere l'esempio riportato di seguito)
    - Verrà interrotto il processo di avvio prima che il controllo sia passato da `initramfs` a `systemd`, come descritto nella documentazione di Red Hat [qui](https://aka.ms/rhel7rootpassword).
1. Premere Ctrl + X per uscire e riavviare il sistema con le impostazioni applicate
1. Eseguito l'avvio, si passa alla modalità di emergenza con un sistema di file di sola lettura. Immettere `mount -o remount,rw /sysroot` nella shell pe rrimonatre il file system radice con autorizzazioni di lettura/scrittura
1. Dopo aver avviato in modalità utente singolo, digitare `chroot /sysroot` per passare al `sysroot` jail
1. Root eseguito. È possibile reimpostare la password radice con `passwd` e quindi usare le istruzioni riportate sopra per attivare la modalità utente singolo. Digitare `reboot -f` per riavviare al termine.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Note: Tramite l’esecuzione delle istruzioni riportate si passerà alla shell di emergenza, in modo da poter eseguire attività quali la modifica `fstab`. Tuttavia, il suggerimento generalmente accettato consiste nel reimpostare la password radice e usarla per accedere alla modalità utente singolo.


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

Per impostazione predefinita, le immagini Ubuntu potrebbero non visualizzare automaticamente la schermata di GRUB. È possibile modificare questo comportamento con le istruzioni seguenti:
1. Aprire `/etc/default/grub.d/50-cloudimg-settings.cfg` in un editor di testo di propria scelta
1. Modificare il valore di `GRUB_TIMEOUT` impostando un valore diverso da zero
1. Aprire `/etc/default/grub` in un editor di testo di propria scelta
1. Impostare come commento la riga `GRUB_HIDDEN_TIMEOUT=1`
1. Eseguire `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Modalità utente singolo in Ubuntu
Ubuntu passerà in modalità utente singolo automaticamente se è impossibile avviare normalmente. Per accedere manualmente alla modalità utente singolo, seguire le istruzioni seguenti:

1. Da GRUB, premere “e” per modificare la voce di avvio (la voce di Ubuntu)
1. Cercare la riga che inizia con `linux`, quindi cercare `ro`
1. Aggiungere `single` dopo `ro`, assicurandosi che sia presente uno spazio prima e dopo `single`
1. Premere Ctrl + X per riavviare il sistema con queste impostazioni e attivare la modalità utente singolo

### <a name="using-grub-to-invoke-bash-in-ubuntu"></a>Usare GRUB per richiamare bash su Ubuntu
In certe situazioni (ad esempio se si dimentica la password radice) potrebbe non essere possibile accedere alla modalità utente singolo nella VM Ubuntu dopo aver seguito le istruzioni riportate. È anche possibile indicare al kernel di eseguire /bin/bash come init, invece dell'init di sistema, che offre una shell bash e consente la manutenzione del sistema. Usare le istruzioni seguenti:

1. Da GRUB, premere “e” per modificare la voce di avvio (la voce di Ubuntu)
1. Cercare la riga che inizia con `linux`, quindi cercare `ro`
1. Sostituire `ro` con `rw init=/bin/bash`.
    - Questa operazione monterà il file system in lettura e scrittura e userà /bin/bash come processo init
1. Premere Ctrl + X per riavviare il sistema con queste impostazioni

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

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

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
* Informazioni su come usare la console seriale per [abilitare GRUB in varie distribuzioni](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* Usare la console seriale per [NMI e SysRq chiamate](serial-console-nmi-sysrq.md)
* La console seriale è disponibile anche per macchine virtuali [Windows](serial-console-windows.md)
* Altre informazioni sulla [diagnostica di avvio](boot-diagnostics.md)
