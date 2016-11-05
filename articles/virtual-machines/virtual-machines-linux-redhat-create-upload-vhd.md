---
title: Creare e caricare un VHD di Red Hat Enterprise Linux per l'utilizzo in Azure
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure contenente un sistema operativo Linux RedHat.
services: virtual-machines-linux
documentationcenter: ''
author: SuperScottz
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/17/2016
ms.author: mingzhan

---
# Preparare una macchina virtuale basata su RedHat per Azure
In questo articolo verrà descritto come preparare una macchina virtuale Red Hat Enterprise Linux (RHEL) per l'utilizzo in Azure. Le versioni di RHEL illustrate in questo articolo sono la 6.7, la 7.1 e la 7.2. Gli hypervisor per la preparazione illustrati in questo articolo sono Hyper-V, KVM (Kernel-based Virtual Machine) e VMware. Per altre informazioni sui requisiti di idoneità per partecipare al programma di accesso al cloud di Red Hat, vedere gli articoli relativi al [sito web di accesso al cloud di Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) e all'[esecuzione di RHEL in Azure](https://access.redhat.com/articles/1989673).

[Preparare una macchina virtuale RHEL 6.7 dalla console di gestione di Hyper-V](#rhel67hyperv)

[Preparare una macchina virtuale RHEL 7.1/7.2 dalla console di gestione di Hyper-V](#rhel7xhyperv)

[Preparare una macchina virtuale RHEL 6.7 da KVM](#rhel67kvm)

[Preparare una macchina virtuale RHEL 7.1/7.2 da KVM](#rhel7xkvm)

[Preparare una macchina virtuale RHEL 6.7 da VMware](#rhel67vmware)

[Preparare una macchina virtuale RHEL 7.1/7.2 da VMware](#rhel7xvmware)

[Preparare una macchina virtuale RHEL 7.1/7.2 da un file kickstart](#rhel7xkickstart)

## Preparare una macchina virtuale basata su Red Hat dalla console di gestione di Hyper-V
### Prerequisiti
In questa sezione si presuppone che un'immagine RHEL sia già stata installata (da un file ISO ottenuto dal sito Web di Red Hat) in un disco rigido virtuale (VHD). Per altre informazioni su come usare la console di gestione di Hyper-V per installare un'immagine del sistema operativo, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

**Note sull'installazione di RHEL**

* Vedere anche [Note generali sull'installazione di Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione di Linux per Azure.
* Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD usando la console di gestione di Hyper-V o il cmdlet di PowerShell **convert-vhd**.
* Le unità VHD devono essere create come "fisse". Le unità VHD dinamiche non sono supportate.
* Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, spesso predefinite per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o [RAID](virtual-machines-linux-configure-raid.md).
* Non configurare una partizione swap nel disco del sistema operativo. È possibile configurare l'agente Linux per poter creare un file di scambio sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.
* Quando si usa **qemu-img** per convertire immagini del disco in formato VHD, si noti che c'è un bug noto nelle versioni di qemu-img 2.2.1 o successive. Questo bug restituisce un'unità VHD non correttamente formattata. Il problema verrà risolto in una versione futura di qemu-img. Per ora è consigliabile usare qemu-img versione 2.2.0 o precedente.

### <a id="rhel67hyperv"> </a>Preparare una macchina virtuale RHEL 6.7 dalla console di gestione di Hyper-V
1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.
3. Disinstallare NetworkManager attivando il seguente comando:
   
   # sudo rpm -e --nodeps NetworkManager
   Si noti che, se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.
4. Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
5. Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
6. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:
   
   # sudo mkdir -m 0700 /var/lib/waagent
   # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
7. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:
   
   # sudo chkconfig network on
8. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:
   
   # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
9. È stato eseguito il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:
   
   # subscription-manager repos --enable=rhel-6-server-extras-rpms
10. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:
    
     console=ttyS0
     earlyprintk=ttyS0
     rootdelay=300
     numa=off
    
    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà disabilitato NUMA, a causa di un bug nella versione del kernel usata da RHEL 6.
    
    È anche consigliabile rimuovere i parametri seguenti:
    
     rhgb quiet crashkernel=auto
    
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.
    
    L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che può causare problemi con le macchine virtuali di dimensioni inferiori.
11. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito. Modificare /etc/ssh/sshd\_config per includere la riga seguente:
    
     ClientAliveInterval 180
12. Installare l'agente Linux di Azure eseguendo il comando seguente:
    
    # sudo yum install WALinuxAgent
    # sudo chkconfig waagent on
    Si noti che, installando il pacchetto WALinuxAgent, i pacchetti NetworkManager e NetworkManager-gnome verranno rimossi, se l'operazione non è già stata eseguita come descritto nel passaggio 2.
13. Non creare l'area di swap sul disco del sistema operativo. L'agente Linux di Azure può configurare automaticamente l'area di swapping usando il disco risorse locale collegato alla VM dopo il provisioning della VM in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure, come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:
    
     ResourceDisk.Format=y
     ResourceDisk.Filesystem=ext4
     ResourceDisk.MountPoint=/mnt/resource
     ResourceDisk.EnableSwap=y
     ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:
    
    # sudo subscription-manager unregister
15. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
16. Fare clic su **Action > Shut Down** (Azione > Arresta) nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

### <a id="rhel7xhyperv"> </a>Preparare una macchina virtuale RHEL 7.1/7.2 dalla console di gestione di Hyper-V
1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.
3. Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
4. Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
5. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:
   
   # sudo chkconfig network on
6. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:
   
   # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
7. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB\_CMDLINE\_LINUX**. ad esempio:
   
     GRUB_CMDLINE_LINUX="rootdelay=300
     console=ttyS0
     earlyprintk=ttyS0"
   
   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. È anche consigliabile rimuovere i parametri seguenti:
   
     rhgb quiet crashkernel=auto
   
   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che può causare problemi con le macchine virtuali di dimensioni inferiori.
8. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:
   
   # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
9. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:
   
     ClientAliveInterval 180
10. È stato eseguito il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:
    
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
11. Installare l'agente Linux di Azure eseguendo il comando seguente:
    
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent.service
12. Non creare l'area di swap sul disco del sistema operativo. L'agente Linux di Azure può configurare automaticamente l'area di swapping usando il disco risorse locale collegato alla VM dopo il provisioning della VM in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i seguenti parametri di `/etc/waagent.conf` in modo appropriato:
    
     ResourceDisk.Format=y
     ResourceDisk.Filesystem=ext4
     ResourceDisk.MountPoint=/mnt/resource
     ResourceDisk.EnableSwap=y
     ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:
    
    # sudo subscription-manager unregister
14. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
15. Fare clic su **Action > Shut Down** (Azione > Arresta) nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

## Preparare una macchina virtuale basata su Red Hat da KVM
### <a id="rhel67kvm"> </a>Preparare una macchina virtuale RHEL 6.7 da KVM
1. Scaricare l'immagine KVM di RHEL 6.7 dal sito Web di Red Hat.
2. Impostare una password radice.
   
   Generare una password crittografata e copiare l'output del comando:
   
   # openssl passwd -1 changeme
   Impostare una password radice con guestfish:
   
   # guestfish --rw -a <image-name>
   > <fs> run
   > <fs> list-filesystems
   > <fs> mount /dev/sda1 /
   > <fs> vi /etc/shadow
   > <fs> exit
   > 
   > 
   
   Modificare il secondo campo dell'utente radice "!!" con la password crittografata.
3. Creare una macchina virtuale in KVM dall'immagine qcow2, impostare il tipo di disco su **qcow2** e impostare il modello del dispositivo di interfaccia di rete virtuale su **virtio**. Quindi avviare la macchina virtuale e accedere come root.
4. Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
5. Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
6. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:
   
   # mkdir -m 0700 /var/lib/waagent
   # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
7. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:
   
   # chkconfig network on
8. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:
   
   # subscription-manager register --auto-attach --username=XXX --password=XXX
9. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà disabilitato NUMA, a causa di un bug nella versione del kernel usata da RHEL 6.
   
   È anche consigliabile rimuovere i parametri seguenti:
   
     rhgb quiet crashkernel=auto
   
   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che può causare problemi con le macchine virtuali di dimensioni inferiori.
10. Aggiungere moduli Hyper-V in initramfs:
    
    Modificare `/etc/dracut.conf` e aggiungere il contenuto: add\_drivers+="hv\_vmbus hv\_netvsc hv\_storvsc"
    
    Ricompilare initramfs: # dracut –f -v
11. Disinstallare cloud-init:
    
    # yum remove cloud-init
12. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:
    
    # chkconfig sshd on
    Modificare /etc/ssh/sshd\_config per includere le righe seguenti:
    
     PasswordAuthentication yes
     ClientAliveInterval 180
    
    Riavviare sshd:
    
    # service sshd restart
13. È stato eseguito il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:
    
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
14. Installare l'agente Linux di Azure eseguendo il comando seguente:
    
    # yum install WALinuxAgent
    # chkconfig waagent on
15. L'agente Linux di Azure può configurare automaticamente l'area di swapping usando il disco risorse locale collegato alla VM dopo il provisioning della VM in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure, come illustrato nel passaggio precedente, modificare i parametri seguenti in **/etc/waagent.conf** in modo appropriato:
    
     ResourceDisk.Format=y
     ResourceDisk.Filesystem=ext4
     ResourceDisk.MountPoint=/mnt/resource
     ResourceDisk.EnableSwap=y
     ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
16. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:
    
    # subscription-manager unregister
17. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
    # waagent -force -deprovision
    # export HISTSIZE=0
    # logout
18. Arrestare la VM in KVM.
19. Convertire l'immagine qcow2 in formato VHD. Convertire innanzitutto l'immagine in formato non elaborato:
    
    # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare la dimensione per l'allineamento a 1 MB: # MB=$((1*1024)) # size=$(qemu-img info -f raw --output json "rhel-1024.raw" | \\ gawk 'match($6.7, /"virtual-size": ([0]+),/, val) {print val[0-9]}') # rounded\_size=$((($size/$MB + 1)*$MB))
    
    # qemu-img resize rhel-6.7.raw $rounded_size
    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:
    
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

### <a id="rhel7xkvm"> </a>Preparare una macchina virtuale RHEL 7.1/7.2 da KVM
1. Scaricare l'immagine KVM di RHEL 7.1 (o 7.2) dal sito Web di Red Hat. Per questo esempio, si userà RHEL 7.1.
2. Impostare una password radice.
   
   Generare una password crittografata e copiare l'output del comando:
   
   # openssl passwd -1 changeme
   Impostare una password radice con guestfish.
   
   # guestfish --rw -a <image-name>
   > <fs> run
   > <fs> list-filesystems
   > <fs> mount /dev/sda1 /
   > <fs> vi /etc/shadow
   > <fs> exit
   > 
   > 
   
   Modificare il secondo campo utente radice "!!" con la password crittografata.
3. Creare una macchina virtuale in KVM dall'immagine qcow2, impostare il tipo di disco su **qcow2** e impostare il modello del dispositivo di interfaccia di rete virtuale su **virtio**. Quindi avviare la macchina virtuale e accedere come root.
4. Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
5. Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
6. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:
   
   # chkconfig network on
7. Registrare la propria sottoscrizione Red Hat per abilitare l’installazione dei pacchetti dall’archivio RHEL eseguendo il seguente comando:
   
   # subscription-manager register --auto-attach --username=XXX --password=XXX
8. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB\_CMDLINE\_LINUX**. ad esempio:
   
     GRUB_CMDLINE_LINUX="rootdelay=300
     console=ttyS0
     earlyprintk=ttyS0"
   
   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. È anche consigliabile rimuovere i parametri seguenti:
   
     rhgb quiet crashkernel=auto
   
   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che può causare problemi con le macchine virtuali di dimensioni inferiori.
9. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:
   
   # grub2-mkconfig -o /boot/grub2/grub.cfg
10. Aggiungere moduli Hyper-V in initramfs:
    
    Modificare `/etc/dracut.conf` e aggiungere il contenuto:
    
     add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
    
    Ricompilare initramfs:
    
    # dracut –f -v
11. Disinstallare cloud-init:
    
    # yum remove cloud-init
12. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:
    
    # systemctl enable sshd
    Modificare /etc/ssh/sshd\_config per includere le righe seguenti:
    
     PasswordAuthentication yes
     ClientAliveInterval 180
    
    Riavviare sshd:
    
     systemctl restart sshd
13. È stato eseguito il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:
    
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
14. Installare l'agente Linux di Azure eseguendo il comando seguente:
    
    # yum install WALinuxAgent
    Abilitare il servizio waagent:
    
    # systemctl enable waagent.service
15. Non creare l'area di swap sul disco del sistema operativo. L'agente Linux di Azure può configurare automaticamente l'area di swapping usando il disco risorse locale collegato alla VM dopo il provisioning della VM in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i seguenti parametri di `/etc/waagent.conf` in modo appropriato:
    
     ResourceDisk.Format=y
     ResourceDisk.Filesystem=ext4
     ResourceDisk.MountPoint=/mnt/resource
     ResourceDisk.EnableSwap=y
     ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
16. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:
    
    # subscription-manager unregister
17. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
18. Arrestare la macchina virtuale in KVM.
19. Convertire l'immagine qcow2 in formato VHD.
    
    Convertire innanzitutto l'immagine in formato non elaborato:
    
    # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw
    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:
    
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.1.raw $rounded_size
    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:
    
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd

## Preparare una macchina virtuale basata su Red Hat da VMware
### Prerequisiti
In questa sezione si presuppone che una macchina virtuale RHEL sia già stata installata in VMware. Per informazioni dettagliate su come installare un sistema operativo in VMware, vedere [VMware Guest Operating System Installation Guide](http://partnerweb.vmware.com/GOSIG/home.html) (Guida all'installazione del sistema operativo guest VMware).

* Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, spesso predefinite per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o RAID.
* Non configurare una partizione swap nel disco del sistema operativo. È possibile configurare l'agente Linux per poter creare un file di scambio sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Quando si crea il disco rigido virtuale, selezionare **Store virtual disk as a single file** (Archivia disco virtuale come singolo file).

### <a id="rhel67vmware"> </a>Preparare una macchina virtuale RHEL 6.7 da VMware
1. Disinstallare NetworkManager attivando il seguente comando:
   
   # sudo rpm -e --nodeps NetworkManager
   Si noti che, se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.
2. Nella directory /etc/sysconfig/, creare un file denominato **network** contenente il testo seguente:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
3. Nella directory /etc/sysconfig/network-scripts/ creare un file denominato **ifcfg-eth0** contenente il testo seguente:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
4. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:
   
   # sudo mkdir -m 0700 /var/lib/waagent
   # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
5. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:
   
   # sudo chkconfig network on
6. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:
   
   # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
7. È stato eseguito il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:
   
   # subscription-manager repos --enable=rhel-6-server-extras-rpms
8. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:
   
     console=ttyS0
     earlyprintk=ttyS0
     rootdelay=300
     numa=off
   
   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà disabilitato NUMA, a causa di un bug nella versione del kernel usata da RHEL 6. È anche consigliabile rimuovere i parametri seguenti:
   
     rhgb quiet crashkernel=auto
   
   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che può causare problemi con le macchine virtuali di dimensioni inferiori.
9. Aggiungere moduli Hyper-V in initramfs:
   
     Edit `/etc/dracut.conf` and add content:
   
         add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
     Rebuild initramfs:
   
         # dracut –f -v
10. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:
    
     ClientAliveInterval 180
11. Installare l'agente Linux di Azure eseguendo il comando seguente:
    
    # sudo yum install WALinuxAgent
    # sudo chkconfig waagent on
12. Non creare l'area di swap sul disco del sistema operativo:
    
    L'agente Linux di Azure può configurare automaticamente l'area di swapping usando il disco risorse locale collegato alla VM dopo il provisioning della VM in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i seguenti parametri di `/etc/waagent.conf` in modo appropriato:
    
     ResourceDisk.Format=y
     ResourceDisk.Filesystem=ext4
     ResourceDisk.MountPoint=/mnt/resource
     ResourceDisk.EnableSwap=y
     ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:
    
    # sudo subscription-manager unregister
14. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
15. Arrestare la macchina virtuale e convertire il file VMDK in un file con estensione vhd.
    
    Convertire innanzitutto l'immagine in formato non elaborato:
    
    # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw
    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:
    
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
             gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.7.raw $rounded_size
    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:
    
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

### <a id="rhel7xvmware"> </a>Preparare una macchina virtuale RHEL 7.1/7.2 da VMware
1. Nella directory /etc/sysconfig/, creare un file denominato **network** contenente il testo seguente:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
2. Nella directory /etc/sysconfig/network-scripts/ creare un file denominato **ifcfg-eth0** contenente il testo seguente:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
3. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:
   
   # sudo chkconfig network on
4. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:
   
   # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
5. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB\_CMDLINE\_LINUX**. ad esempio:
   
     GRUB_CMDLINE_LINUX="rootdelay=300
     console=ttyS0
     earlyprintk=ttyS0"
   
   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. È anche consigliabile rimuovere i parametri seguenti:
   
     rhgb quiet crashkernel=auto
   
   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che può causare problemi con le macchine virtuali di dimensioni inferiori.
6. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:
   
   # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
7. Aggiungere moduli Hyper-V in initramfs:
   
   Modificare `/etc/dracut.conf` e aggiungere il contenuto:
   
     add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Ricompilare initramfs:
   
   # dracut –f -v
8. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:
   
     ClientAliveInterval 180
9. È stato eseguito il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:
   
   # subscription-manager repos --enable=rhel-7-server-extras-rpms
10. Installare l'agente Linux di Azure eseguendo il comando seguente:
    
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent.service
11. Non creare l'area di swap sul disco del sistema operativo. L'agente Linux di Azure può configurare automaticamente l'area di swapping usando il disco risorse locale collegato alla VM dopo il provisioning della VM in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i seguenti parametri di `/etc/waagent.conf` in modo appropriato:
    
     ResourceDisk.Format=y
     ResourceDisk.Filesystem=ext4
     ResourceDisk.MountPoint=/mnt/resource
     ResourceDisk.EnableSwap=y
     ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
12. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:
    
    # sudo subscription-manager unregister
13. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
14. Arrestare la VM e convertire il file VMDK in file VHD.
    
    Convertire innanzitutto l'immagine in formato non elaborato:
    
    # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw
    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:
    
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.1.raw $rounded_size
    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:
    
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd

## Preparare una macchina virtuale basata su Red Hat da un'immagine ISO usando un file kickstart automatico
### <a id="rhel7xkickstart"> </a>Preparare una macchina virtuale RHEL 7.1/7.2 da un file kickstart
1. Creare un file kickstart con il contenuto seguente e salvare il file. Per informazioni dettagliate sull'installazione di kickstart, vedere la [guida all'installazione di kickstart](https://access.redhat.com/documentation/it-IT/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
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

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

1. Posizionare il file kickstart in una posizione accessibile dal sistema di installazione.
2. Creare una nuova macchina virtuale nella console di gestione Hyper-V. Nella pagina **Connessione disco rigido virtuale** selezionare **Connetti un disco rigido virtuale successivamente** e completare la creazione guidata della macchina virtuale.
3. Aprire le impostazioni della macchina virtuale:
   
   a. Associare un nuovo disco rigido virtuale alla VM Accertarsi di selezionare **VHD Format** (Formato VHD) e **Fixed Size** (A dimensione fissa).
   
   b. Collegare l'ISO di installazione all'unità DVD.
   
   c. Impostare il BIOS per l'avvio da CD.
4. Avviare la VM. Quando viene visualizzata la guida all'installazione, premere **Tab** per configurare le opzioni di avvio.
5. Inserire `inst.ks=<the location of the kickstart file>` alla fine di opzioni di avvio e premere **Invio**.
6. Attendere la fine dell'installazione. Al termine, la VM verrà arrestata automaticamente. Il file VHD Linux è ora pronto per il caricamento in Azure.

## Problemi noti
Esistono problemi noti quando si usa RHEL 7.1 in Hyper-V e Azure.

### Blocco del disco I/O
Questo problema può verificarsi durante frequenti attività di I/O del disco di archiviazione con RHEL 7.1 in Hyper-V e Azure.

Frequenza di ripetizione:

Il problema è intermittente. Si verifica più spesso durante frequenti operazioni di I/O del disco in Hyper-V e Azure.

[AZURE.NOTE] Questo problema noto è già stato risolto da Red Hat. Per installare gli aggiornamenti rapidi associati, eseguire il comando seguente:

    # sudo yum update

### Non è possibile includere il driver Hyper-V nel disco RAM iniziale quando si usa un hypervisor non Hyper-V
In alcuni casi, i programmi di installazione di Linux potrebbero non includere i driver per Hyper-V nel disco RAM iniziale (initrd o initramfs), a meno che non rilevino di essere in esecuzione in un ambiente Hyper-V.

Quando si usa un sistema di virtualizzazione diverso (ad esempio Virtualbox, Xen e così via) per preparare l'immagine Linux, potrebbe essere necessario ricompilare initrd per assicurarsi che almeno i moduli kernel hv\_vmbus e hv\_storvsc siano disponibili sul disco RAM iniziale. Questo è un problema noto, almeno nei sistemi basati sulla distribuzione upstream di Red Hat.

Per risolvere questo problema, è necessario aggiungere i moduli Hyper-V in initramfs e ricompilarlo:

Modificare `/etc/dracut.conf` e aggiungere il contenuto:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Ricompilare initramfs:

        # dracut –f -v

Per altri dettagli, vedere le informazioni sulla [ricompilazione di initramfs](https://access.redhat.com/solutions/1958).

## Passaggi successivi
È ora possibile usare il disco rigido virtuale Red Hat Enterprise Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere i passaggi 2 e 3 nell'articolo [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

Per altre informazioni sugli hypervisor certificati per l'esecuzione di Red Hat Enterprise Linux, visitare [il sito Web di Red Hat](https://access.redhat.com/certified-hypervisors).

<!---HONumber=AcomDC_0518_2016-->