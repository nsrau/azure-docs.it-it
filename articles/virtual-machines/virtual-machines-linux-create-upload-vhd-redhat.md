<properties 
	pageTitle="Creazione e caricamento di un VHD di Linux RedHat in Azure" 
	description="Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure che contiene un sistema operativo Linux RedHat." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="SuperScottz" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/28/2015" 
	ms.author="mingzhan"/>


# Preparare una macchina virtuale basata su RedHat per Azure
In questo articolo verrà descritto come preparare una macchina virtuale Red Hat Enterprise Linux (RHEL) per l'utilizzo in Azure. Le versioni di RHEL trattate in questo articolo sono 6.6, 6.7, 7.0 e 7.1 e gli hypervisor per la preparazione trattati in questo articolo sono Hyper-V, KVM e VMWare.




##Preparare un'immagine dalla console di gestione di Hyper-V 
###Prerequisiti
In questa sezione si presuppone che un'immagine RHEL sia già stata installata da un file ISO ottenuto dal sito Web di RedHats in un disco rigido virtuale (VHD). Per ulteriori informazioni su come utilizzare la console di gestione di Hyper-V per installare un'immagine del sistema operativo, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

**Note sull'installazione di RHEL**

- Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

- Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o RAID.

- Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

- Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.

###RHEL 6.6/6.7

1.	Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2.	Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

3.	Disinstallare NetworkManager attivando il seguente comando:

        # sudo rpm -e --nodeps NetworkManager

    **Nota:** se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.

4.	Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:
            
        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

8.	Registrare la sottoscrizione Red Hat per consentire l'installazione dei pacchetti dall’archivio RHEL:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	Abilitare l'archivio epel, poiché il pacchetto WALinuxAgent `WALinuxAgent-<version>` è stato inserito nell’archivio Fedora EPEL 6:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

10.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà disabilitato NUMA, a causa di un bug nella versione del kernel usata da RHEL 6.

    Inoltre, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.

    L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella VM di almeno 128 MB, il che potrebbe causare problemi con le VM di dimensioni inferiori.

11.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito. Modificare /etc/ssh/sshd\_config per includere la seguente riga:

        ClientAliveInterval 180

12.	Installare l'agente Linux di Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    **Si noti** che, installando il pacchetto WALinuxAgent, i pacchetti NetworkManager e NetworkManager-gnome verranno rimossi, se l'operazione non è già stata eseguita come descritto nel passaggio 2.

13.	Non creare l’area swap sul disco del sistema operativo. L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	Annullare la sottoscrizione (se necessario) tramite il comando seguente:

        # sudo subscription-manager unregister

15.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.
###RHEL 7.0/7.1

1.	Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2.	Fare clic su Connetti per aprire una finestra della console per la macchina virtuale.

3.	Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.	Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.	Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

6.	Registrare la sottoscrizione Red Hat per consentire l'installazione dei pacchetti dall’archivio RHEL:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB\_CMDLINE\_LINUX**, ad esempio:

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Inoltre, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella VM di almeno 128 MB, il che potrebbe causare problemi con le VM di dimensioni inferiori.

8.	Dopo aver terminato di modificare `/etc/default/grub` come sopra illustrato, eseguire il comando seguente per ricompilare la configurazione GRUB:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito. Modificare `/etc/ssh/sshd_config`per includere la seguente riga:

        ClientAliveInterval 180

10.	Abilitare l'archivio epel, poiché il pacchetto WALinuxAgent `WALinuxAgent-<version>` è stato inserito nell’archivio Fedora EPEL 7.

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

11.	Installare l'agente Linux di Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service 

12.	Non creare l'area di swap sul disco del sistema operativo. L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti `/etc/waagent.conf` in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

        # sudo subscription-manager unregister

14.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
        
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


##Preparare un'immagine da KVM 
###RHEL 6.6/6.7

1.	Scaricare l'immagine KVM di RHEL 6.6/6.7 dal sito web di Red Hat.

2.	Impostare una password radice

    Generare una password crittografata e copiare l'output del comando:

        # openssl passwd -1 changeme
    Impostare una password radice con guestfish:
       
        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit
    Modificare il secondo campo utente radice "!!" con la password crittografata.

3.	Creare una macchina virtuale in KVM dall'immagine qcow2, impostare il tipo di disco su **qcow2**, impostare il modello di dispositivo di interfaccia di rete virtuale su **virtio**. Quindi avviare la macchina virtuale e accedere come root.

4.	Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

        # chkconfig network on

8.	Registrare la sottoscrizione Red Hat per consentire l'installazione dei pacchetti dall’archivio RHEL:

        # subscription-manager register –auto-attach --username=XXX --password=XXX

9.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà disabilitato NUMA, a causa di un bug nella versione del kernel usata da RHEL 6.

    Inoltre, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella VM di almeno 128 MB, il che potrebbe causare problemi con le VM di dimensioni inferiori.

10.	Disinstallare cloud-init:

        # yum remove cloud-init

11.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:
 
        # chkconfig sshd on

    Modificare /etc/ssh/sshd\_config per includere le seguenti righe:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Riavviare sshd:

		# service sshd restart

12.	Abilitare l'archivio epel, poiché il pacchetto WALinuxAgent `WALinuxAgent-<version>` è stato inserito nell’archivio **Fedora EPEL 6**.

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

13.	Installare l'agente Linux di Azure eseguendo il comando seguente:

        # yum install WALinuxAgent
        # chkconfig waagent on

14.	L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in **/etc/waagent.conf** in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:
        
        # subscription-manager unregister

16.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	Arrestare la VM in KVM.

18.	Convertire l'immagine qcow2 in formato vhd: prima convertire l'immagine in formato non elaborato:
         
         # qemu-img convert -f qcow2 –O raw rhel-6.6.qcow2 rhel-6.6.raw
    Verificare che le dimensioni dell'immagine non elaborata siano allineate con 1MB, in caso contrario arrotondare la dimensione per l'allineamento con 1MB:

         # qemu-img resize rhel-6.6.raw $rounded_size

    Convertire il disco non elaborato alla dimensione fissa del disco rigido virtuale:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.6.raw rhel-6.6.vhd
 
###RHEL 7.0/7.1

1.	Scaricare l'immagine KVM di RHEL 7.0 dal sito web di Red Hat.

2.	Impostare una password radice

    Generare una password crittografata e copiare l'output del comando

        # openssl passwd -1 changeme

    Impostare una password radice con guestfish

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Modificare il secondo campo utente radice "!!" con la password crittografata

3.	Creare una macchina virtuale in KVM dall'immagine qcow2, impostare il tipo di disco su **qcow2**, impostare il modello di dispositivo di interfaccia di rete virtuale su **virtio**. Quindi avviare la macchina virtuale e accedere come root.

4.	Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

        # chkconfig network on

7.	Registrare la sottoscrizione Red Hat per consentire l'installazione dei pacchetti dall’archivio RHEL:

        # subscription-manager register –auto-attach --username=XXX --password=XXX

8.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB\_CMDLINE\_LINUX**, ad esempio:

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Inoltre, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella VM di almeno 128 MB, il che potrebbe causare problemi con le VM di dimensioni inferiori.

9.	Dopo aver terminato di modificare `/etc/default/grub` come sopra illustrato, eseguire il comando seguente per ricompilare la configurazione GRUB:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	Disinstallare cloud-init:

        # yum remove cloud-init

11.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:

        # systemctl enable sshd

    Modificare /etc/ssh/sshd\_config per includere le seguenti righe:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Riavviare sshd:

        systemctl restart sshd	

12.	Abilitare l'archivio epel, poiché il pacchetto WALinuxAgent `WALinuxAgent-<version>` è stato inserito nell’archivio **Fedora EPEL 7**.

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

13.	Installare l'agente Linux di Azure eseguendo il comando seguente:

        # yum install WALinuxAgent

    Abilitare il servizio waagent:

        # systemctl enable waagent.service

14.	Non creare l'area di swap sul disco del sistema operativo. L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti `/etc/waagent.conf` in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Annullare la sottoscrizione(se necessario) eseguendo il comando seguente:

        # subscription-manager unregister

16.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	Arrestare la macchina virtuale in KVM.

18.	Convertire l'immagine qcow2 in formato vhd:

    Convertire innanzitutto l'immagine in formato non elaborato:

         # qemu-img convert -f qcow2 –O raw rhel-7.0.qcow2 rhel-7.0.raw

    Verificare che le dimensioni dell'immagine non elaborata siano allineate con 1MB, in caso contrario arrotondare la dimensione per l'allineamento con 1MB:

         # qemu-img resize rhel-7.0.raw $rounded_size

    Convertire il disco non elaborato alla dimensione fissa del disco rigido virtuale:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.0.raw rhel-7.0.vhd


##Preparare un'immagine da VMWare
###Prerequisiti
In questa sezione si presuppone che una macchina virtuale RHEL sia già stata installata in VMWare. Per informazioni dettagliate su come installare un sistema operativo in VMWare, vedere [Guida all'installazione del sistema operativo guest VMWare](http://partnerweb.vmware.com/GOSIG/home.html).
 
- Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o RAID.

- Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

- Quando si crea il disco rigido virtuale, selezionare **Archivia disco virtuale come singolo file**.

###RHEL 6.6/6.7
1.	Disinstallare NetworkManager attivando il seguente comando:

         # sudo rpm -e --nodeps NetworkManager

    **Nota:** se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.

2.	Creare nella directory /etc/sysconfig/ un file denominato **network** contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.	Creare nella directory /etc/sysconfig/network-scripts/ un file denominato **ifcfg-eth0** contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.	Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

6.	Registrare la sottoscrizione Red Hat per consentire l'installazione dei pacchetti dall’archivio RHEL:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Abilitare l'archivio epel, poiché il pacchetto WALinuxAgent `WALinuxAgent-<version>` è stato inserito nell’archivio Fedora EPEL 6.

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

8.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà disabilitato NUMA, a causa di un bug nella versione del kernel usata da RHEL 6. Inoltre, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella VM di almeno 128 MB, il che potrebbe causare problemi con le VM di dimensioni inferiori.

9.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito. Modificare `/etc/ssh/sshd_config`per includere la seguente riga:

        ClientAliveInterval 180

10.	Installare l'agente Linux di Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

11.	Non creare l'area di swap sul disco del sistema operativo:
    
    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti `/etc/waagent.conf` in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

        # sudo subscription-manager unregister

13.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # sudo waagent -force -deprovision 
        # export HISTSIZE=0
        # logout

14.	Arrestare la macchina virtuale e convertire il file VMDK in file VHD.

    Convertire innanzitutto l'immagine in formato non elaborato:

        # qemu-img convert -f vmdk –O raw rhel-6.6.vmdk rhel-6.6.raw

    Verificare che le dimensioni dell'immagine non elaborata siano allineate con 1MB, in caso contrario arrotondare la dimensione per l'allineamento con 1MB:

        # qemu-img resize rhel-6.6.raw $rounded_size

    Convertire il disco non elaborato alla dimensione fissa del disco rigido virtuale:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.6.raw rhel-6.6.vhd

###RHEL 7.0/7.1

1.	Creare nella directory /etc/sysconfig/ un file denominato **network** contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.	Creare nella directory /etc/sysconfig/network-scripts/ un file denominato **ifcfg-eth0** contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.	Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

4.	Registrare la sottoscrizione Red Hat per consentire l'installazione dei pacchetti dall’archivio RHEL:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB\_CMDLINE\_LINUX**, ad esempio:

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Inoltre, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione crashkernel può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella VM di almeno 128 MB, il che potrebbe causare problemi con le VM di dimensioni inferiori.

6.	Dopo aver terminato di modificare `/etc/default/grub` come sopra illustrato, eseguire il comando seguente per ricompilare la configurazione GRUB:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Aggiungere moduli Hyper-V in initramfs:

    Modifica `/etc/dracut.conf`, e aggiungere il contenuto:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Ricompilare l’initramfs:

        # dracut –f -v

8.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito. Modificare `/etc/ssh/sshd_config`per includere la seguente riga:

        ClientAliveInterval 180

9.	Abilitare l'archivio epel, poiché il pacchetto WALinuxAgent `WALinuxAgent-<version>` è stato inserito nell’archivio Fedora EPEL 7:


        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

10.	Installare l'agente Linux di Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	Non creare l'area di swap sul disco del sistema operativo. L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti `/etc/waagent.conf` in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

        # sudo subscription-manager unregister

13.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	Arrestare la VM e convertire il file VMDK in file VHD.

    Convertire innanzitutto l'immagine in formato non elaborato:

        # qemu-img convert -f vmdk –O raw rhel-7.0.vmdk rhel-7.0.raw

    Verificare che le dimensioni dell'immagine non elaborata siano allineate con 1MB, in caso contrario arrotondare la dimensione per l'allineamento con 1MB:

        # qemu-img resize rhel-7.0.raw $rounded_size

    Convertire il disco non elaborato alla dimensione fissa del disco rigido virtuale:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.0.raw rhel-7.0.vhd


##Preparazione da un'immagine ISO utilizzando automaticamente il file automaticamente
###RHEL 7.0/7.1

1.	Creare il file kickstart con il contenuto seguente e salvare il file. Per informazioni dettagliate sull'installazione di kickstart, consultare la [Guida all'installazione di Kickstart](https://access.redhat.com/documentation/it-IT/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).


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

        # Primary Fedora repo
        repo --name="epel7" --baseurl="http://dl.fedoraproject.org/pub/epel/7/x86_64/"

        %packages
        @base
        @console-internet
        chrony
        sudo
        python-pyasn1
        parted
        ntfsprogs
        WALinuxAgent
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

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

 

2.	Posizionare il file kickstart in una posizione raggiungibile dal sistema di installazione.
 
3.	Creare una nuova macchina virtuale nella console di gestione Hyper-V. Nella pagina **Connessione disco rigido virtuale** selezionare **Collega un disco rigido virtuale in un secondo momento**, e completare la creazione guidata della macchina virtuale.

4.	Aprire le impostazioni della macchina virtuale:

    a. Collegare un nuovo disco rigido virtuale alla macchina virtuale, assicurarsi di selezionare **formato VHD** e **a dimensione fissa**.
    
    b. Collegare l'ISO di installazione all'unità DVD.

    c. Impostare BIOS per l'avvio da CD.

5.	Avviare la macchina virtuale, quando viene visualizzata la Guida all'installazione, premere **Scheda** per configurare le opzioni di avvio.

6.	Inserire `inst.ks=<the location of the Kickstart file>` alla fine di opzioni di avvio e premere **Invio**.

7.	Attendere il completamento dell'installazione. Una volta terminata la macchina virtuale verrà automaticamente arrestata. Il file VHD Linux è ora pronto per il caricamento in Azure.

##Problemi noti:
Esistono 2 problemi noti quando si utilizza RHEL 6.6, 7.0 e 7.1 in Hyper-V e Azure.

###Problema 1: Timeout del provisioning
Questo problema può verificarsi durante l'avvio con RHEL in Hyper-V e Azure. E’ più comune con RHEL 6.6.

Frequenza di ripetizione:

Il problema è intermittente. In genere si riproduce in macchine virtuali di dimensioni inferiori con una CPU virtuale singola e più spesso sui server più occupati.


###Problema 2: Blocco del disco I/O 

Questo problema può verificarsi durante attività del disco di archiviazione frequenti con RHEL 6.6, 7.0 e 7.1 in Hyper-V e Azure.

Frequenza di ripetizione:

Il problema è intermittente tuttavia si verifica più frequentemente durante le operazioni frequenti del disco I/O frequenti in Hyper-V e Azure.

    
[AZURE.NOTE]Questi 2 problemi noti sono già risolti da Red Hat. Per installare gli aggiornamenti rapidi associati, è possibile eseguire il comando seguente:

    # sudo yum update

<!---HONumber=Nov15_HO3-->