---
title: Creare e caricare un VHD di Red Hat Enterprise Linux per l'uso in Azure Stack | Microsoft Docs
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure contenente un sistema operativo Linux RedHat.
services: azure-stack
documentationcenter: ''
author: JeffGoldner
manager: BradleyB
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: jeffgo
ms.lastreviewed: 08/15/2018
ms.openlocfilehash: ad0419cee3fc5c838d6d81adf9040432b9feaf07
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242230"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Preparare una macchina virtuale basata su Red Hat per Azure Stack

In questo articolo si apprenderà come preparare una macchina virtuale Red Hat Enterprise Linux (RHEL) per l'uso in Azure Stack. Le versioni di RHEL trattate in questo articolo sono 7.1 +. Gli hypervisor per la preparazione illustrati in questo articolo sono Hyper-V, KVM (Kernel-based Virtual Machine) e VMware.

Per informazioni sul supporto di Red Hat Enterprise Linux, fare riferimento a [Red Hat e Azure Stack: Domande frequenti](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparare una macchina virtuale basata su Red Hat dalla console di gestione di Hyper-V

In questa sezione presuppone che sia già presente un file ISO dal sito Web di Red Hat e installato l'immagine RHEL in un disco rigido virtuale (VHD). Per altre informazioni su come usare Gestione di Hyper-V per installare un'immagine del sistema operativo, vedere [installare il ruolo Hyper-V e configurare una macchina virtuale](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>Note sull'installazione di RHEL

* Azure Stack non supporta il formato VHDX. Azure supporta solo dischi rigidi virtuali a dimensione fissa. È possibile usare la console di gestione di Hyper-V o il cmdlet convert-vhd per convertire il disco in formato VHD. Se si usa VirtualBox, durante la creazione del disco selezionare **Fixed size** (A dimensione fissa) anziché l'opzione predefinita di allocazione dinamica.
* Azure Stack supporta solo le macchine virtuali di generazione 1. È possibile convertire una macchina virtuale di prima generazione da VHDX al formato di file VHD e da disco a espansione dinamica a disco a dimensione fissa. Non è possibile modificare la generazione di una macchina virtuale. Per altre informazioni, vedere [Creare una macchina virtuale di generazione 1 o 2 in Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* La dimensione massima consentita per il disco rigido virtuale è 1.023 GB.
* Quando si installa il sistema operativo Linux è consigliabile usare partizioni standard anziché LVM (Logical Volume Manager), che è spesso l'impostazione predefinita per numerose installazioni. Questa prassi evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se si fosse eventualmente necessario collegare un disco del sistema operativo a un'altra macchina virtuale identica per la risoluzione dei problemi.
* Per montare file system UDF (Universal Disk Format) è necessario il supporto del kernel. Al primo avvio, i supporti con formattazione UDF collegati al guest passa la configurazione del provisioning per la macchina virtuale Linux. L'agente Linux di Azure è necessario montare il file system UDF per leggerne la configurazione ed eseguire il provisioning della macchina virtuale.
* Non configurare una partizione di swapping sul disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file di scambio sul disco risorse temporaneo. Altre informazioni sono reperibile nei passaggi seguenti.
* Le dimensioni di tutti i dischi rigidi virtuali in Azure devono essere allineate a 1 MB. Durante la conversione da un disco raw in disco rigido virtuale, è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB prima della conversione. Altri dettagli sono disponibili nelle procedure seguenti.
* Azure Stack non supportano cloud-init. La macchina virtuale deve essere configurata con una versione supportata di Windows Azure Linux Agent (WALA).

### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Preparare una macchina virtuale RHEL 7 dalla console di gestione di Hyper-V

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

1. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Crea o si modifica il `/etc/sysconfig/network-scripts/ifcfg-eth0` file, quindi aggiungere il testo seguente in base alle esigenze:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Assicurarsi che l'avvio del servizio di rete in fase di avvio eseguendo il comando seguente:

    ```bash
    sudo systemctl enable network
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa modifica, aprire `/etc/default/grub` in un editor di testo e modificare il `GRUB_CMDLINE_LINUX` parametro. Ad esempio: 

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ciò assicura che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando Azure supporto con il debug dei problemi. Questa configurazione disattiva anche le nuove convenzioni di denominazione di RHEL 7 per le schede di interfaccia di rete.

   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori. Si consiglia di rimuovere i parametri seguenti:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio, che è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

    ```sh
    ClientAliveInterval 180
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

    ```bash
    sudo subscription-manager unregister
    ```

1. Se si usa un sistema che è stato distribuito con un'autorità di certificazione, la macchina virtuale RHEL attendibile il certificato radice di Azure Stack. È necessario posizionare che nell'archivio radice attendibile. Visualizzare [aggiunta attendibili i certificati nel server principali](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Fare clic su **Azione** > **Arresta** nella console di gestione di Hyper-V.

1. Convertire il disco rigido virtuale in una disco rigido virtuale usando la funzionalità di "Modifica disco" gestione di Hyper-V o il comando di PowerShell Convert-VHD di dimensioni fisse. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparare una macchina virtuale basata su Red Hat da KVM

1. Scaricare l'immagine KVM di RHEL 7 dal sito Web di Red Hat. Questa procedura usa come esempio RHEL 7.

1. Impostare una password radice.

    Generare una password crittografata e copiare l'output del comando:

    ```bash
    openssl passwd -1 changeme
    ```

   Impostare una password radice con guestfish:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Modificare il secondo campo dell'utente radice da "!!" con la password crittografata.

1. Creare una macchina virtuale in KVM dall'immagine qcow2. Impostare il tipo di disco su **qcow2** e il modello del dispositivo di interfaccia di rete virtuale su **virtio**. Avviare quindi la macchina virtuale e accedere come utente ROOT.

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Assicurarsi che l'avvio del servizio di rete in fase di avvio eseguendo il comando seguente:

    ```bash
    sudo systemctl enable network
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l’installazione dei pacchetti dall’archivio RHEL eseguendo il seguente comando:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa configurazione, aprire `/etc/default/grub` in un editor di testo e modificare il `GRUB_CMDLINE_LINUX` parametro. Ad esempio: 

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Questo comando, inoltre, garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Il comando disattiva anche nuove convenzioni di denominazione RHEL 7 per NIC

   Avvio con interfaccia grafica e non interattiva non sono utili in un ambiente cloud in cui tutti i log vengono inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, che potrebbe causare problemi in macchine virtuali di dimensioni inferiori. Si consiglia di rimuovere i parametri seguenti:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Aggiungere i moduli Hyper-V in initramfs.

    Modificare `/etc/dracut.conf` e aggiungere il contenuto:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Ricompilare initramfs:

    ```bash
    dracut -f -v
    ```

1. Disinstallare cloud-init:

    ```bash
    yum remove cloud-init
    ```

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:

    ```bash
    systemctl enable sshd
    ```

    Modificare /etc/ssh/sshd_config per includere le righe seguenti:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

    ```bash
    yum install WALinuxAgent
    ```

    Abilitare il servizio waagent:

    ```bash
    systemctl enable waagent.service
    ```

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

    ```bash
    subscription-manager unregister
    ```

1. Se si usa un sistema che è stato distribuito con un'autorità di certificazione, la macchina virtuale RHEL attendibile il certificato radice di Azure Stack. È necessario posizionare che nell'archivio radice attendibile. Visualizzare [aggiunta attendibili i certificati nel server principali](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Arrestare la macchina virtuale in KVM.

1. Convertire l'immagine qcow2 nel formato VHD.

    > [!NOTE]
    > Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.

    Convertire innanzitutto l'immagine in formato non elaborato:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Oppure, con qemu versione **2.6+** includere l'opzione `force_size`:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparare una macchina virtuale basata su Red Hat da VMware

In questa sezione si presuppone che una macchina virtuale RHEL sia già stata installata in VMware. Per informazioni dettagliate su come installare un sistema operativo in VMware, vedere la [guida all'installazione del sistema operativo guest VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Quando si installa il sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che è spesso l'impostazione predefinita per numerose installazioni. Ciò consente di evitare conflitti di nome LVM con macchina virtuale clonata, in particolare se fosse necessario un disco di sistema operativo da collegare a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o RAID.
* Non configurare una partizione di swapping sul disco del sistema operativo. È possibile configurare l'agente Linux per poter creare un file di scambio sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi riportati di seguito.
* Quando si crea il disco rigido virtuale, selezionare **Store virtual disk as a single file**(Archivia disco virtuale come singolo file).

### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Preparare una macchina virtuale RHEL 7 da VMware

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

    ```bash
    sudo chkconfig network on
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa modifica, aprire `/etc/default/grub` in un editor di testo e modificare il `GRUB_CMDLINE_LINUX` parametro. Ad esempio: 

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Questa configurazione, inoltre, garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Disattiva anche le nuove convenzioni di denominazione RHEL 7 per le schede di interfaccia di rete. È consigliabile anche rimuovere i parametri seguenti:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Aggiungere i moduli Hyper-V a initramfs.

    Modificare `/etc/dracut.conf`e aggiungere il contenuto:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Ricompilare initramfs:

    ```bash
    dracut -f -v
    ```

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questa è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

    ```sh
    ClientAliveInterval 180
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e può essere svuotato con il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

    ```bash
    sudo subscription-manager unregister
    ```

1. Se si usa un sistema che è stato distribuito con un'autorità di certificazione, la macchina virtuale RHEL attendibile il certificato radice di Azure Stack. È necessario posizionare che nell'archivio radice attendibile. Visualizzare [aggiunta attendibili i certificati nel server principali](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Arrestare la macchina virtuale e convertire il file VMDK nel formato VHD.

    > [!NOTE]
    > Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Convertire innanzitutto l'immagine in formato non elaborato:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Oppure, con qemu versione **2.6+** includere l'opzione `force_size`:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparare una macchina virtuale basata su Red Hat da un'immagine ISO usando un file kickstart automatico

1. Creare e salvare un file kickstart con il contenuto seguente. Per informazioni dettagliate sull'installazione di kickstart, vedere la [guida all'installazione di kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Posizionare il file kickstart in un percorso in cui sia accessibile per il sistema di installazione.

1. Creare una nuova macchina virtuale nella console di gestione di Hyper-V. Nella pagina **Connessione disco rigido virtuale** selezionare **Connetti un disco rigido virtuale successivamente** e completare la creazione guidata della macchina virtuale.

1. Aprire le impostazioni della macchina virtuale:

    a. Collegare un nuovo disco rigido virtuale alla macchina virtuale. Accertarsi di selezionare **Formato VHD** e **A dimensione fissa**.

    b. Collegare l'ISO di installazione all'unità DVD.

    c. Impostare il BIOS per l'avvio da CD.

1. Avviare la macchina virtuale. Quando viene visualizzata la guida all'installazione, premere **Tab** per configurare le opzioni di avvio.

1. Inserire `inst.ks=<the location of the kickstart file>` alla fine di opzioni di avvio e premere **Invio**.

1. Attendere la fine dell'installazione. Al termine, la macchina virtuale viene arrestata automaticamente. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="known-issues"></a>Problemi noti

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Non è possibile includere il driver Hyper-V nel disco RAM iniziale quando si usa un hypervisor non Hyper-V

In alcuni casi, i programmi di installazione di Linux potrebbero non includere i driver per Hyper-V nel disco RAM iniziale (initrd o initramfs), a meno che Linux non rilevi di essere in esecuzione in un ambiente Hyper-V.

Quando si usa un sistema di virtualizzazione diverso (Virtualbox, Xen e così via) per preparare l'immagine Linux, potrebbe essere necessario ricompilare initrd per assicurarsi che almeno i moduli del kernel hv_vmbus e hv_storvsc siano disponibili nel disco RAM iniziale. Questo è un problema noto, almeno nei sistemi basati sulla distribuzione upstream di Red Hat.

Per risolvere questo problema, aggiungere i moduli Hyper-V a initramfs e ricompilarlo:

Modificare `/etc/dracut.conf` e aggiungere il contenuto seguente:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

Ricompilare initramfs:

    ```bash
    dracut -f -v
    ```

Per altre informazioni, vedere [ricompilazione di initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Passaggi successivi

Ora è possibile usare il disco rigido virtuale Red Hat Enterprise Linux per creare nuove macchine virtuali in Azure Stack. Se questa è la prima volta che si carica il file VHD in Azure Stack, vedere [creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Per altre informazioni sugli hypervisor certificati per l'esecuzione di Red Hat Enterprise Linux, vedere [sito Web di Red Hat](https://access.redhat.com/certified-hypervisors).
