<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Prepare a distribution" pageTitle="Prepare a distribution of Linux in Azure" metaKeywords="Azure Git CodePlex, Azure website CodePlex, Azure website Git" description="Learn how to use Git to publish to an Azure website, as well as enable continuous deployment from GitHub and CodePlex." metaCanonical="" services="virtual-machines" documentationCenter="" title="Prepare a Linux Virtual Machine for Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Preparazione di una macchina virtuale Linux per Azure

Una macchina virtuale in Azure esegue il sistema operativo scelto al momento della creazione della macchina virtuale. Azure archivia il sistema operativo di una macchina virtuale in un disco rigido virtuale in formato VHD (file con estensione vhd). Il disco rigido virtuale di un sistema operativo che è stato preparato per la duplicazione è denominato immagine. In questo articolo viene illustrato come creare la propria immagine tramite il caricamento di un file VHD con un sistema operativo che è stato installato e generalizzato dall'utente. Per ulteriori informazioni su dischi e immagini in Azure, vedere [Gestione di dischi e immagini][Gestione di dischi e immagini].

**Nota**: quando si crea una macchina virtuale, è possibile personalizzare le impostazioni del sistema operativo per facilitare l'esecuzione dell'applicazione. La configurazione impostata viene archiviata nel disco per quella macchina virtuale. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata].

**Importante**: il contratto di servizio della piattaforma Azure si applica alle macchine virtuali che eseguono il sistema operativo Linux solo quando una distribuzione approvata viene utilizzata con i dettagli di configurazione specificati in [questo articolo][questo articolo]. Tutte le distribuzioni di Linux disponibili nella raccolta immagini di Azure sono distribuzioni approvate con la configurazione richiesta.

## Prerequisiti

In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

-   **Un certificato di gestione**: l'utente deve avere creato un certificato di gestione per la sottoscrizione per cui si desidera caricare un file VHD e avere esportato il certificato in un file con estensione cer. Per ulteriori informazioni sulla creazione di certificati, vedere [Creare e caricare un certificato di gestione per Azure][Creare e caricare un certificato di gestione per Azure].

-   **Sistema operativo Linux installato in un file VHD.**: l'utente deve aver installato un sistema operativo Linux supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Per creare il file VHD e installare il sistema operativo, è possibile utilizzare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale][Installare il ruolo Hyper-V e configurare una macchina virtuale].

    **Importante**: il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

    Per un elenco delle distribuzioni approvate, vedere [Linux in Azure - Distribuzioni approvate][Linux in Azure - Distribuzioni approvate]. In alternativa vedere la sezione [Informazioni per le distribuzioni non approvate][Informazioni per le distribuzioni non approvate] al termine di questo articolo.

-   **Strumento da riga di comando di Azure per Linux.** Se si utilizza un sistema operativo Linux per creare l'immagine, utilizzare questo strumento per caricare il file VHD. Per scaricare lo strumento, vedere [Strumenti da riga di comando di Azure per Mac e Linux][Strumenti da riga di comando di Azure per Mac e Linux].

-   **Cmdlet Add-AzureVHD**, incluso nel modulo Azure PowerShell. Per scaricare il modulo, vedere la pagina dei [download di Azure][download di Azure]. Per informazioni di riferimento, vedere [Add-AzureVhd][Add-AzureVhd].

Per tutte le distribuzioni, tenere presente quanto indicato di seguito:

L'agente Linux di Azure (Waagent) non è compatibile con NetworkManager. Per la configurazione di rete, che dovrebbe essere controllabile tramite gli script ifup/ifdown, è necessario utilizzare il file ifcfg-eth0. Non sarà possibile installare Waagent se viene rilevato il pacchetto NetworkManager.

NUMA non è supportato a causa di un bug nelle versioni del kernel Linux precedenti alla 2.6.37. L'installazione manuale di waagent disabiliterà automaticamente NUMA nella configurazione GRUB per la riga di comando del kernel Linux.

Per l'agente Linux di Azure è inoltre necessario che sia installato il pacchetto python-pyasn1.

È consigliabile non creare una partizione di SWAP al momento dell'installazione. È possibile configurare l'area di SWAP utilizzando l'agente Linux di Azure. Non è inoltre consigliabile utilizzare il kernel mainstream con una macchina virtuale di Azure senza la patch disponibile nel [sito Web Microsoft][sito Web Microsoft]. Molte distribuzioni o kernel correnti potrebbero già includere questa correzione.

Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.

Questa attività include i passaggi seguenti:

-   [Passaggio 1: Preparare l'immagine da caricare][Passaggio 1: Preparare l'immagine da caricare]
-   [Passaggio 2: Creare un account di archiviazione in Azure][Passaggio 2: Creare un account di archiviazione in Azure]
-   [Passaggio 3: Preparare la connessione ad Azure][Passaggio 3: Preparare la connessione ad Azure]
-   [Passaggio 4: Caricare l'immagine in Azure][Passaggio 4: Caricare l'immagine in Azure]

## <span id="prepimage"></span> </a>Passaggio 1: Preparare l'immagine da caricare

### Preparare il sistema operativo CentOS 6.2+

Per l'esecuzione della macchina virtuale in Azure è necessario eseguire specifici passaggi di configurazione nel sistema operativo.

1.  Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.  Disinstallare NetworkManager eseguendo il comando seguente:

        rpm -e --nodeps NetworkManager

    **Nota:** se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.

4.  Nella directory `/etc/sysconfig/` creare un file denominato **network** contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Nella directory `/etc/sysconfig/network-scripts/` creare un file denominato **ifcfg-eth0** contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        DHCP=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Abilitare il servizio di rete eseguendo il comando seguente:

        chkconfig network on

7.  CentOS 6.2 o 6.3: installare i driver per Linux Integration Services.

    **Nota:** questo passaggio è valido solo per CentOS 6.2 e 6.3. In CentOS 6.4+ Linux Integration Services è già disponibile nel kernel.

    a) Scaricare il file ISO contenente i driver per Linux Integration Services dall'[Area download][Area download].

    b) Nella console di gestione di Hyper-V, nel riquadro **Azioni** fare clic su **Impostazioni**.

    ![Apertura impostazioni di Hyper-V][Apertura impostazioni di Hyper-V]

    c) Nel riquadro **Hardware** fare clic su **Controller IDE 1**.

    ![Aggiunta unità DVD con supporti di installazione][Aggiunta unità DVD con supporti di installazione]

    d) Nella casella **Controller IDE** fare clic su **Unità DVD**, quindi fare clic su **Aggiungi**.

    e) Selezionare **File immagine**, passare a **Linux IC v3.2.iso** e quindi fare clic su **Apri**.

    f) Nella pagina **Impostazioni** fare clic su **OK**.

    g) Fare clic su **Connetti** per aprire la finestra della macchina virtuale.

    h) Nella finestra del prompt dei comandi digitare i comandi seguenti:

        mount /dev/cdrom /media
        /media/install.sh
        reboot

8.  Installare python-pyasn1 eseguendo il comando seguente:

        sudo yum install python-pyasn1

9.  Sostituire il file /etc/yum.repos.d/CentOS-Base.repo con il testo seguente:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

10. Aggiungere la riga seguente a /etc/yum.conf:

        http_caching=packages
        exclude=kernel*

11. Disabilitare il modulo yum "fastestmirror" modificando il file "/etc/yum/pluginconf.d/fastestmirror.conf" e in [main] digitare quanto segue:

        set enabled=0

12. Eseguire il comando seguente per cancellare i metadati yum correnti:

        yum clean all

13. Per CentOS 6.2 e 6.3, aggiornare il kernel di una VM in esecuzione eseguendo i comandi seguenti.

    Per CentOS 6.2, eseguire i comandi seguenti:

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    Per CentOS 6.3+ digitare quanto segue:

        sudo yum --disableexcludes=all install kernel

14. Modificare la riga di avvio del kernel in Grub per includere i parametri seguenti. In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

15. Verificare che tutti i dispositivi SCSI montati nel kernel includano un timeout di I/O non inferiore a 300 secondi.

16. In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

17. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

18. Installare l'agente Linux di Azure eseguendo il comando seguente:

        yum install WALinuxAgent

19. Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

21. Fare clic su **Arresto** nella console di gestione di Hyper-V.

### Preparare il sistema operativo Ubuntu 12.04+

1.  Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.  Sostituire gli archivi correnti nell'immagine con gli archivi di Azure che contengono il kernel e il pacchetto dell'agente necessari per aggiornare la VM. La procedura varia leggermente in base alla versione di Ubuntu.

    Prima di modificare /etc/apt/sources.list, è consigliabile eseguire un backup
     sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        sudo apt-get update

    Ubuntu 12.10:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        sudo apt-get update

    Ubuntu 13.04+:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-get update

4.  Aggiornare il sistema operativo al kernel più recente eseguendo i comandi seguenti:

    Ubuntu 12.04:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 12.10:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 13.04 e 13.10:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

5.  Dopo un arresto anomalo del sistema, Ubuntu attenderà l'input dell'utente al prompt di Grub. Per evitare questa situazione, completare i passaggi seguenti:

    a) Aprire il file /etc/grub.d/00\_header.

    b) Nella funzione **make\_timeout()** cercare **if ["\\${recordfail}" = 1 ]; then**

    c) Modificare l'istruzione sotto questa riga in **set timeout=5**.

    d) Eseguire update-grub.

6.  Modificare la riga di avvio del kernel in Grub o Grub2 per includere i parametri seguenti. In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

7.  In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

8.  Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

9.  Installare l'agente eseguendo il comando seguente con sudo:

        apt-get update
        apt-get install walinuxagent 

10. Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Fare clic su **Arresto** nella console di gestione di Hyper-V.

### Preparare il sistema operativo SUSE Linux Enterprise Server 11 SP2 e SP3

**NOTA:** [SUSE Studio][SUSE Studio] consente di creare e gestire facilmente le immagini SLES/openSUSE per Azure e Hyper-V. È inoltre possibile scaricare o clonare nel proprio account di SUSE Studio le immagini ufficiali seguenti, disponibili nella raccolta di SUSE Studio, per personalizzarle con la massima semplicità:

> -   [SLES 11 SP2 per Azure nella raccolta di SUSE Studio][SLES 11 SP2 per Azure nella raccolta di SUSE Studio]
> -   [SLES 11 SP3 per Azure nella raccolta di SUSE Studio][SLES 11 SP3 per Azure nella raccolta di SUSE Studio]

1.  Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.  Aggiungere l'archivio contenente il kernel più recente e l'agente Linux di Azure. Eseguire il comando `zypper lr`. Ad esempio, con SLES 11 SP3 l'output dovrebbe essere simile al seguente:

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

    Se il comando restituisce un messaggio di errore simile al seguente:

        "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

    sarà quindi necessario abilitare nuovamente gli archivi o registrare il sistema mediante l'utilità suse\_register. Per altre informazioni, vedere la [documentazione di SLES][documentazione di SLES].

Se uno degli archivi di aggiornamento pertinenti non è abilitato, abilitarlo con il comando seguente:

        zypper mr -e [REPOSITORY NUMBER]

Nel caso precedente, il comando corretto sarebbe

        zypper mr -e 1 2 3 4

1.  Aggiornare il kernel all'ultima versione disponibile:

        zypper up kernel-default

2.  Installare l'agente Linux di Azure:

        zypper up WALinuxAgent

    Può essere visualizzato un messaggio simile al seguente:

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor.
        Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

    Poiché il fornitore del pacchetto è cambiato da "Microsoft Corporation" a "SUSE LINUX Products GmbH, Nuernberg, Germany", è necessario installare esplicitamente il pacchetto come indicato nel messaggio.

    Nota: la versione del pacchetto WALinuxAgent potrebbe essere leggermente diversa.

3.  Modificare la riga di avvio del kernel in Grub per includere i parametri seguenti. In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

4.  È consigliabile modificare l'impostazione di /etc/sysconfig/network/dhcp o equivalente da DHCLIENT\_SET\_HOSTNAME="yes" a DHCLIENT\_SET\_HOSTNAME="no".

5.  In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

6.  Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

7.  Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Fare clic su **Arresto** nella console di gestione di Hyper-V.

### Preparare il sistema operativo SUSE 12.3+

**NOTA:** [SUSE Studio][SUSE Studio] consente di creare e gestire facilmente le immagini SLES/openSUSE per Azure e Hyper-V. È inoltre possibile scaricare o clonare nel proprio account di SUSE Studio le immagini ufficiali seguenti, disponibili nella raccolta di SUSE Studio, per personalizzarle con la massima semplicità:

> -   [openSUSE 12.3 nella raccolta di SUSE Studio][openSUSE 12.3 nella raccolta di SUSE Studio]

1.  Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.  Aggiornare il sistema operativo al kernel e alle patch più recenti disponibili.

4.  Nella shell eseguire il comando '`zypper lr`'. Se questo comando restituisce

        # | Alias                     | Name                      | Enabled | Refresh
        --+---------------------------+---------------------------+---------+--------
        1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
        2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
        3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

gli archivi sono configurati correttamente e non sono necessarie correzioni.

Se il comando restituisce un messaggio simile a "Nessun archivio definito. Usare il comando 'zypper addrepo' per aggiungere uno
o più archivi", è necessario abilitare nuovamente gli archivi:

        zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
        zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

Verificare che gli archivi siano stati aggiunti chiamando di nuovo 'zypper lr'.

Se uno degli archivi di aggiornamento pertinenti non è abilitato, abilitarlo con il comando seguente:

        zypper mr -e [NUMBER OF REPOSITORY]

1.  Disabilitare l'esecuzione automatica del probe del DVD ROM.

2.  Installare l'agente Linux di Azure:

    In primo luogo, aggiungere l'archivio contenente il nuovo WALinuxAgent:

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    Quindi, eseguire il comando seguente:

        zypper up WALinuxAgent

    Dopo l'esecuzione di questo comando, potrebbe essere visualizzato un messaggio simile al seguente:

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
        Use 'zypper install WALinuxAgent' to install this candidate."

    Questo messaggio è previsto. Poiché il fornitore del pacchetto è cambiato da "Microsoft Corporation" a "obs://build.opensuse.org/Cloud", è necessario installare esplicitamente il pacchetto come indicato nel messaggio.

3.  Modificare la riga di avvio del kernel in Grub per includere i parametri seguenti. In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    In /boot/grub/menu.lst, rimuovere inoltre i parametri seguenti dalla riga di comando del kernel, se presenti:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

4.  È consigliabile modificare l'impostazione di /etc/sysconfig/network/dhcp o equivalente da DHCLIENT\_SET\_HOSTNAME="yes" a DHCLIENT\_SET\_HOSTNAME="no".

5.  In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

6.  Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

7.  Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Verificare che l'agente Linux di Azure venga eseguito all'avvio:

        systemctl enable waagent.service

10. Fare clic su **Arresto** nella console di gestione di Hyper-V.

## <span id="createstorage"></span> </a>Passaggio 2: Creare un account di archiviazione in Azure

L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso ai servizi di archiviazione di Azure ed è associato alla sottoscrizione di Azure. È necessario disporre di un account di archiviazione di Azure per caricare un file VHD in Azure che può essere utilizzato per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile utilizzare il portale di gestione di Azure.

1.  Accedere al portale di gestione di Azure.

2.  Sulla barra dei comandi fare clic su **New**.

    ![Creazione dell'account di archiviazione][Creazione dell'account di archiviazione]

3.  Fare clic su **Storage Account** e quindi su **Quick Create**.

    ![Creazione rapida di un account di archiviazione][Creazione rapida di un account di archiviazione]

4.  Compilare i campi come indicato di seguito:

    ![Immissione dei dettagli dell'account di archiviazione][Immissione dei dettagli dell'account di archiviazione]

-   In **URL** digitare un nome di sottodominio da utilizzare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

-   Scegliere la posizione o il gruppo di affinità per l'account di archiviazione. Specificando un gruppo di affinità è possibile posizionare i servizi cloud nello stesso data center delle risorse di archiviazione.

-   Scegliere se si desidera usare la replica geografica per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a una posizione secondaria qualora si verifichi un errore grave che non può essere gestito nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se determinati requisiti legali o criteri dell'organizzazione richiedono un controllo più rigido rispetto alla posizione dell'archiviazione basata su cloud, è possibile disattivare la replica geografica. Tenere presente che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato.

1.  Fare clic su **Create Storage Account**.

    L'account verrà visualizzato in **Storage Accounts**.

    ![Creazione dell'account di archiviazione completata][Creazione dell'account di archiviazione completata]

## <span id="#connect"></span> </a>Passaggio 3: Preparare la connessione ad Azure

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Azure.

1.  Aprire una finestra di Azure PowerShell.

2.  Digitare:

    `Get-AzurePublishSettingsFile`

    Questo comando apre una finestra del browser e scarica automaticamente un file .publishsettings che contiene un certificato e le informazioni relative alla sottoscrizione di Azure.

3.  Salvare il file .publishsettings.

4.  Digitare:

    `Import-AzurePublishSettingsFile <PathToFile>`

    Dove `<PathToFile>` è il percorso completo del file .publishsettings.

    Per ulteriori informazioni, vedere [Iniziare a utilizzare i cmdlet di Azure][Iniziare a utilizzare i cmdlet di Azure].

## <span id="upload"></span> </a>Passaggio 4: Caricare l'immagine in Azure

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione creato nel passaggio 2 e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di gestione. **PathToVHDFile** è il percorso completo e il nome del file VHD.

Eseguire una delle operazioni seguenti:

-   Nella finestra di Azure PowerShell utilizzata nel passaggio precedente, digitare:

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Per ulteriori informazioni, vedere [Add-AzureVhd][Add-AzureVhd].

-   Utilizzare lo strumento da riga di comando per Linux per caricare l'immagine. È possibile caricare un'immagine utilizzando il comando seguente:

        Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <span id="nonendorsed"></span> </a>Informazioni per le distribuzioni non approvate

In sostanza, per il funzionamento corretto sulla piattaforma, tutte le distribuzioni eseguite in Azure dovranno soddisfare i prerequisiti illustrati di seguito.

L'elenco non è esaustivo, in quanto ogni distribuzione è diversa. È persino possibile che, anche se tutti i criteri elencati di seguito vengono soddisfatti, sia comunque necessario modificare in modo significativo l'immagine per il funzionamento corretto nella piattaforma.

Per questo motivo, è consigliabile iniziare con una delle [immagini di partner approvate][immagini di partner approvate].

L'elenco che segue sostituisce il passaggio 1 del processo per la creazione di un disco rigido virtuale:

1.  Sarà necessario verificare che il kernel in esecuzione integri i driver LIS più recenti per Hyper-V o che questi ultimi siano stati correttamente compilati, ovvero resi Open Source. I driver sono disponibili [a questo indirizzo.][a questo indirizzo.]

2.  Il kernel deve inoltre includere l'ultima versione del driver ATA PiiX utilizzato per il provisioning delle immagini e devono essere state implementate le correzioni con il commit cd006086fa5d91414d8ff9ff2b78fbb593878e3c Data: Ven 4 maggio 2012 21:15:11 (GMT) ata\_piix: defer disks to the Hyper-V drivers by default.

3.  Il file initrd compresso deve essere di dimensioni inferiori a 40 MB (\* questo valore viene aggiornato continuamente, dunque questa informazione potrebbe essere obsoleta).

4.  Modificare la riga di avvio del kernel in Grub o Grub2 per includere i parametri seguenti. In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  È consigliabile modificare l'impostazione di /etc/sysconfig/network/dhcp o equivalente da DHCLIENT\_SET\_HOSTNAME="yes" a DHCLIENT\_SET\_HOSTNAME="no".

6.  Verificare che tutti i dispositivi SCSI montati nel kernel includano un timeout di I/O non inferiore a 300 secondi.

7.  Installare l'agente Linux di Azure seguendo la procedura disponibile nella [Guida dell'agente Linux][Guida dell'agente Linux]. L'agente è stato rilasciato con licenza Apache 2 e tutti gli aggiornamenti sono disponibili nella [pagina GitHub dell'agente.][pagina GitHub dell'agente.]

8.  In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

9.  Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

10. Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Arrestare la macchina virtuale e procedere con il caricamento.

  [Gestione di dischi e immagini]: http://msdn.microsoft.com/it-it/library/windowsazure/jj672979.aspx
  [Come creare una macchina virtuale personalizzata]: /it-it/manage/windows/how-to-guides/custom-create-a-vm/
  [questo articolo]: http://support.microsoft.com/kb/2805216
  [Creare e caricare un certificato di gestione per Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg551722.aspx
  [Installare il ruolo Hyper-V e configurare una macchina virtuale]: http://technet.microsoft.com/it-it/library/hh846766.aspx
  [Linux in Azure - Distribuzioni approvate]: ../linux-endorsed-distributions
  [Informazioni per le distribuzioni non approvate]: #nonendorsed
  [Strumenti da riga di comando di Azure per Mac e Linux]: http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409
  [download di Azure]: /it-it/develop/downloads/
  [Add-AzureVhd]: http://msdn.microsoft.com/it-it/library/windowsazure/dn205185.aspx
  [sito Web Microsoft]: http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409
  [Passaggio 1: Preparare l'immagine da caricare]: #prepimage
  [Passaggio 2: Creare un account di archiviazione in Azure]: #createstorage
  [Passaggio 3: Preparare la connessione ad Azure]: #connect
  [Passaggio 4: Caricare l'immagine in Azure]: #upload
  [Area download]: http://www.microsoft.com/it-it/download/details.aspx?id=34603
  [Apertura impostazioni di Hyper-V]: ./media/virtual-machines-linux-how-to-prepare/settings.png
  [Aggiunta unità DVD con supporti di installazione]: ./media/virtual-machines-linux-how-to-prepare/installiso.png
  [SUSE Studio]: http://www.susestudio.com
  [SLES 11 SP2 per Azure nella raccolta di SUSE Studio]: http://susestudio.com/a/02kbT4/sles-11-sp2-for-windows-azure
  [SLES 11 SP3 per Azure nella raccolta di SUSE Studio]: http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure
  [documentazione di SLES]: https://www.suse.com/documentation/sles11/
  [openSUSE 12.3 nella raccolta di SUSE Studio]: http://susestudio.com/a/02kbT4/opensuse-12-3-for-windows-azure
  [Creazione dell'account di archiviazione]: ./media/virtual-machines-linux-how-to-prepare/create.png
  [Creazione rapida di un account di archiviazione]: ./media/virtual-machines-linux-how-to-prepare/storage-quick-create.png
  [Immissione dei dettagli dell'account di archiviazione]: ./media/virtual-machines-linux-how-to-prepare/storage-create-account.png
  [Creazione dell'account di archiviazione completata]: ./media/virtual-machines-linux-how-to-prepare/Storagenewaccount.png
  [Iniziare a utilizzare i cmdlet di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj554332.aspx
  [immagini di partner approvate]: https://www.windowsazure.com/it-it/manage/linux/other-resources/endorsed-distributions/
  [a questo indirizzo.]: http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409
  [Guida dell'agente Linux]: https://www.windowsazure.com/it-it/manage/linux/how-to-guides/linux-agent-guide/
  [pagina GitHub dell'agente.]: http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409
