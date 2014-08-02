<properties linkid="manage-linux-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Linux VHD in Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Learn to create and upload an Azure virtual hard disk (VHD) that has the Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux
===========================================================================================

Una macchina virtuale in Azure esegue il sistema operativo scelto al momento della creazione della macchina virtuale. Azure archivia il sistema operativo di una macchina virtuale in un disco rigido virtuale in formato VHD (file con estensione vhd). Il disco rigido virtuale di un sistema operativo che è stato preparato per la duplicazione è denominato immagine. In questo articolo viene illustrato come creare la propria immagine tramite il caricamento di un file VHD con un sistema operativo che è stato installato e generalizzato dall'utente. Per ulteriori informazioni su dischi e immagini in Azure, vedere [Gestione di dischi e immagini](http://msdn.microsoft.com/en-us/library/windowsazure/jj672979.aspx).

**Nota**: quando si crea una macchina virtuale, è possibile personalizzare le impostazioni del sistema operativo per facilitare l'esecuzione dell'applicazione. La configurazione impostata viene archiviata nel disco per quella macchina virtuale. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata](/en-us/manage/windows/how-to-guides/custom-create-a-vm/).

**Importante**: il contratto di servizio della piattaforma Azure si applica alle macchine virtuali che eseguono il sistema operativo Linux solo quando una distribuzione approvata viene utilizzata con i dettagli di configurazione specificati in [questo articolo](http://support.microsoft.com/kb/2805216). Tutte le distribuzioni di Linux disponibili nella raccolta immagini di Azure sono distribuzioni approvate con la configurazione richiesta.

Prerequisiti
------------

In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

-   **Un certificato di gestione**: l'utente deve avere creato un certificato di gestione per la sottoscrizione per cui si desidera caricare un file VHD e avere esportato il certificato in un file con estensione cer. Per ulteriori informazioni sulla creazione di certificati, vedere [Creare e caricare un certificato di gestione per Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx).

-   **Sistema operativo Linux installato in un file VHD.**: l'utente deve aver installato un sistema operativo Linux supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Per creare il file VHD e installare il sistema operativo, è possibile utilizzare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

    **Importante**: il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

    Per un elenco delle distribuzioni approvate, vedere [Linux in Azure - Distribuzioni approvate](../linux-endorsed-distributions). In alternativa vedere la sezione [Informazioni per le distribuzioni non approvate](#nonendorsed) al termine di questo articolo.

-   **Strumento da riga di comando di Azure per Linux.** Se si utilizza un sistema operativo Linux per creare l'immagine, utilizzare questo strumento per caricare il file VHD. Per scaricare lo strumento, vedere [Strumenti da riga di comando di Azure per Mac e Linux](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409).

-   **Cmdlet Add-AzureVHD**, incluso nel modulo Azure PowerShell. Per scaricare il modulo, vedere la pagina dei [download di Azure](/en-us/develop/downloads/). Per informazioni di riferimento, vedere [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx).

**Per tutte le distribuzioni, tenere presente quanto indicato di seguito:**

-   Durante l'installazione del sistema operativo Linux è consigliabile utilizzare partizioni standard anziché LVM, spesso predefinite per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi.

-   L'agente Linux di Azure (waagent) non è compatibile con NetworkManager. Dovrebbe essere possibile controllare la configurazione di rete mediante gli script ifup/ifdown. La maggior parte dei pacchetti RPM/Debian forniti dalle distribuzioni configura NetworkManager come in conflitto con il pacchetto waagent, pertanto disinstallerà NetworkManager quando si installa il pacchetto dell'agente Linux. L'agente Linux di Azure richiede inoltre l'installazione del pacchetto python-pyasn1.

-   NUMA non è supportato per le VM di dimensioni maggiori, a causa di un bug nelle versioni del kernel Linux precedenti alla 2.6.37. L'installazione manuale di waagent disabiliterà automaticamente NUMA nella configurazione GRUB per il kernel Linux. Questo problema incide principalmente sulle distribuzioni che utilizzano il kernel upstream Red Hat 2.6.32.

-   È consigliabile non creare una partizione di SWAP al momento dell'installazione. È possibile configurare l'area di SWAP utilizzando l'agente Linux di Azure. Non è inoltre consigliabile utilizzare il kernel mainstream con una macchina virtuale di Azure senza la patch disponibile nel [sito Web Microsoft](http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409). Molte distribuzioni o kernel correnti potrebbero già includere questa correzione.

-   Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.

Questa attività include i passaggi seguenti:

-   [Passaggio 1: Preparare l'immagine da caricare](#prepimage)
-   [Passaggio 2: Creare un account di archiviazione in Azure](#createstorage)
-   [Passaggio 3: Preparare la connessione ad Azure](#connect)
-   [Passaggio 4: Caricare l'immagine in Azure](#upload)

Passaggio 1: Preparare l'immagine da caricare
---------------------------------------------

### Preparare CentOS 6.2+

Per l'esecuzione della macchina virtuale in Azure è necessario eseguire specifici passaggi di configurazione nel sistema operativo.

1.  Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.  Disinstallare NetworkManager eseguendo il comando seguente:

         rpm -e --nodeps NetworkManager

    **Nota:** se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.

4.  Creare nella directory `/etc/sysconfig/` un file denominato **network** contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Creare nella directory `/etc/sysconfig/network-scripts/` un file denominato **ifcfg-eth0** contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Windows Azure o Hyper-V:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

         # chkconfig network on

8.  **Solo CentOS 6.2 o 6.3**: installare i driver per Linux Integration Services.

    **Nota:** questo passaggio è valido solo per CentOS 6.2 e 6.3. In CentOS 6.4+ Linux Integration Services è già disponibile nel kernel.

    a) Scaricare il file ISO contenente i driver per Linux Integration Services dall'[Area download](http://www.microsoft.com/en-us/download/details.aspx?id=34603).

    b) Nella console di gestione di Hyper-V, nel riquadro **Azioni** fare clic su **Impostazioni**.

    ![Apertura impostazioni di Hyper-V](./media/virtual-machines-linux-create-upload-vhd/settings.png)

    c) Nel riquadro **Hardware** fare clic su **Controller IDE 1**.

    ![Aggiunta unità DVD con supporti di installazione](./media/virtual-machines-linux-create-upload-vhd/installiso.png)

    d) Nella casella **Controller IDE** fare clic su **Unità DVD**, quindi fare clic su **Aggiungi**.

    e) Selezionare **File immagine**, passare a **Linux IC v3.2.iso** e quindi fare clic su **Apri**.

    f) Nella pagina **Impostazioni** fare clic su **OK**.

    g) Fare clic su **Connetti** per aprire la finestra della macchina virtuale.

    h) Nella finestra del prompt dei comandi digitare i comandi seguenti:

        mount /dev/cdrom /media
        /media/install.sh
        reboot

9.  Installare python-pyasn1 eseguendo il comando seguente:

         # sudo yum install python-pyasn1

10. Sostituire il file /etc/yum.repos.d/CentOS-Base.repo con il testo seguente:

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

11. Aggiungere la riga seguente a /etc/yum.conf:

        http_caching=packages

    Solo in CentOS 6.2 e 6.3 aggiungere la riga seguente:

        exclude=kernel*

12. Disabilitare il modulo yum "fastestmirror" modificando il file "/etc/yum/pluginconf.d/fastestmirror.conf" e in [main] digitare quanto segue:

        set enabled=0

13. Eseguire il comando seguente per cancellare i metadati yum correnti:

        yum clean all

14. Per CentOS 6.2 e 6.3, aggiornare il kernel di una VM in esecuzione eseguendo i comandi seguenti.

    Per CentOS 6.2, eseguire i comandi seguenti:

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    Per CentOS 6.3+ digitare quanto segue:

        sudo yum --disableexcludes=all install kernel

15. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire /boot/grub/menu.lst in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà inoltre disabilitato NUMA, a causa di un bug nella versione del kernel utilizzata da CentOS 6.

16. In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

17. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

18. Installare l'agente Linux di Azure eseguendo il comando seguente:

        yum install WALinuxAgent

    Si noti che, installando il pacchetto WALinuxAgent, i pacchetti NetworkManager e NetworkManager-gnome verranno rimossi, se l'operazione non è già stata eseguita come descritto nel passaggio 2.

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

### Preparare Ubuntu 12.04+

1.  Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.  Sostituire gli archivi correnti nell'immagine con gli archivi di Azure che contengono il kernel e il pacchetto dell'agente necessari per aggiornare la VM. La procedura varia leggermente in base alla versione di Ubuntu.

    Prima di modificare /etc/apt/sources.list, è consigliabile eseguire un backup sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

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

    Ubuntu 13.04, 13.10 e 14.04:

         sudo apt-get update
         sudo apt-get install hv-kvp-daemon-init
         (recommended) sudo apt-get dist-upgrade
         sudo reboot

5.  Dopo un arresto anomalo del sistema, Ubuntu attenderà l'input dell'utente al prompt di Grub. Per evitare questa situazione, completare i passaggi seguenti:

    a) Aprire il file /etc/grub.d/00\_header.

    b) Nella funzione **make\_timeout()** cercare **if ["\${recordfail}" = 1 ]; then**

    c) Modificare l'istruzione sotto questa riga in **set timeout=5**.

    d) Eseguire 'sudo update-grub'.

6.  Modificare la riga di avvio del kernel per Grub in modo da includere ulteriori parametri del kernel per Azure. A questo scopo, aprire /etc/default/grub in un editor di testo, individuare la variabile "GRUB\_CMDLINE\_LINUX\_DEFAULT" (o aggiungerla, se necessario) e modificarla per includere i parametri seguenti:

         GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

    Salvare e chiudere il file e quindi eseguire 'sudo update-grub'. In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto tecnico di Azure nella risoluzione dei problemi di debug.

7.  In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

8.  Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

9.  Installare l'agente eseguendo il comando seguente con sudo:

        apt-get update
        apt-get install walinuxagent

    Si noti che, installando il pacchetto walinuxagent, i pacchetti NetworkManager e NetworkManager-gnome eventualmente installati verranno rimossi.

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

### Preparare Oracle Linux 6.4+

Per l'esecuzione della macchina virtuale in Azure è necessario eseguire specifici passaggi di configurazione nel sistema operativo.

1.  Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.  Disinstallare NetworkManager eseguendo il comando seguente:

         rpm -e --nodeps NetworkManager

    **Nota:** se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.

4.  Creare nella directory `/etc/sysconfig/` un file denominato **network** contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Creare nella directory `/etc/sysconfig/network-scripts/` un file denominato **ifcfg-eth0** contenente il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Windows Azure o Hyper-V:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

         # chkconfig network on

8.  Installare python-pyasn1 eseguendo il comando seguente:

         # sudo yum install python-pyasn1

9.  Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire /boot/grub/menu.lst in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà inoltre disabilitato NUMA, a causa di un bug nella versione del kernel utilizzata da CentOS 6.

10. In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

11. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

12. Installare l'agente Linux di Azure eseguendo il comando seguente:

        yum install WALinuxAgent

    Si noti che, installando il pacchetto WALinuxAgent, i pacchetti NetworkManager e NetworkManager-gnome eventualmente installati verranno rimossi.

13. Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

15. Fare clic su **Arresto** nella console di gestione di Hyper-V.

### Preparare SUSE Linux Enterprise Server 11 SP2 e SP3

**NOTA:** [SUSE Studio](http://www.susestudio.com) consente di creare e gestire facilmente le immagini SLES/openSUSE per Azure e Hyper-V. È inoltre possibile scaricare o clonare nel proprio account di SUSE Studio le immagini ufficiali seguenti, disponibili nella raccolta di SUSE Studio, per personalizzarle con la massima semplicità:

> -   [SLES 11 SP3 per Azure nella raccolta di SUSE Studio](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)

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

		"Nessun archivio definito. Utilizzare il comando 'zypper addrepo' per aggiungere uno o più archivi."

	sarà quindi necessario abilitare nuovamente gli archivi o registrare il sistema mediante l'utilità suse_register. Per ulteriori informazioni, vedere la [documentazione su SLES](https://www.suse.com/documentation/sles11/).

   Se uno degli archivi di aggiornamento pertinenti non è abilitato, abilitarlo con il comando seguente:

        zypper mr -e [REPOSITORY NUMBER]

   Nel caso precedente, il comando corretto sarebbe

        zypper mr -e 1 2 3 4

1.  Aggiornare il kernel all'ultima versione disponibile:

         zypper up kernel-default

2.  Installare l'agente Linux di Azure:

         zypper up WALinuxAgent

    Può essere visualizzato un messaggio simile al seguente:

         "È disponibile un candidato all'aggiornamento per 'WALinuxAgent', ma è di un fornitore diverso.
         Utilizzare 'zypper install WALinuxAgent-1.2-1.1.noarch' per installare il candidato."

    Poiché il fornitore del pacchetto è cambiato da "Microsoft Corporation" a "SUSE LINUX Products GmbH, Nuernberg, Germany", è necessario installare esplicitamente il pacchetto come indicato nel messaggio.

    Nota: la versione del pacchetto WALinuxAgent potrebbe essere leggermente diversa.

3.  Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire /boot/grub/menu.lst in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

         console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.

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

### Preparare openSUSE 12.3+

**NOTA:** [SUSE Studio](http://www.susestudio.com) consente di creare e gestire facilmente le immagini SLES/openSUSE per Azure e Hyper-V. È inoltre possibile scaricare o clonare nel proprio account di SUSE Studio le immagini ufficiali seguenti, disponibili nella raccolta di SUSE Studio, per personalizzarle con la massima semplicità:

> -   [openSUSE 13.1 per Azure nella raccolta di SUSE Studio](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

1.  Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.  Aggiornare il sistema operativo al kernel e alle patch più recenti disponibili.

4.  Nella shell eseguire il comando '`zypper lr`'. Se questo comando restituisce un output simile al seguente (si noti che i numeri di versione possono variare):

         # | Alias                     | Name                      | Enabled | Refresh
         --+---------------------------+---------------------------+---------+--------
         1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
         2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
         3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

    gli archivi sono configurati correttamente e non sono necessarie correzioni.

    Se il comando restituisce un messaggio simile a "Nessun archivio definito. Utilizzare il comando 'zypper addrepo' per aggiungere uno o più archivi", è necessario abilitare nuovamente gli archivi:

         zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
         zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

    Verificare che gli archivi siano stati aggiunti chiamando di nuovo 'zypper lr'.

    Se uno degli archivi di aggiornamento pertinenti non è abilitato, abilitarlo con il comando seguente:

         zypper mr -e [NUMBER OF REPOSITORY]

5.  Disabilitare l'esecuzione automatica del probe del DVD ROM.

6.  Installare l'agente Linux di Azure:

    In primo luogo, aggiungere l'archivio contenente il nuovo WALinuxAgent:

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    Quindi, eseguire il comando seguente:

        zypper up WALinuxAgent

    Dopo l'esecuzione di questo comando, potrebbe essere visualizzato un messaggio simile al seguente:

        "È disponibile un candidato all'aggiornamento per 'WALinuxAgent', ma è di un fornitore diverso. 
        Utilizzare 'zypper install WALinuxAgent' per installare il candidato."

    Questo messaggio è previsto. Poiché il fornitore del pacchetto è cambiato da "Microsoft Corporation" a "obs://build.opensuse.org/Cloud", è necessario installare esplicitamente il pacchetto come indicato nel messaggio.

7.  Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire /boot/grub/menu.lst in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Rimuovere inoltre i messaggi seguenti dalla riga di avvio del kernel, se presenti:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

8.  È consigliabile modificare l'impostazione di /etc/sysconfig/network/dhcp o equivalente da DHCLIENT\_SET\_HOSTNAME="yes" a DHCLIENT\_SET\_HOSTNAME="no".

9.  In /etc/sudoers impostare come commento la riga seguente, se esistente:

        Defaults targetpw

10. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.

11. Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

13. Verificare che l'agente Linux di Azure venga eseguito all'avvio:

        systemctl enable waagent.service

14. Fare clic su **Arresto** nella console di gestione di Hyper-V.

Passaggio 2: Creare un account di archiviazione in Azure
--------------------------------------------------------

L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso ai servizi di archiviazione di Azure ed è associato alla sottoscrizione di Azure. È necessario disporre di un account di archiviazione di Azure per caricare un file VHD in Azure che può essere utilizzato per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile utilizzare il portale di gestione di Azure.

1.  Accedere al portale di gestione di Azure.

2.  Sulla barra dei comandi fare clic su **New**.

    ![Creazione dell'account di archiviazione](./media/virtual-machines-linux-create-upload-vhd/create.png)

3.  Fare clic su **Storage Account** e quindi su **Quick Create**.

    ![Creazione rapida di un account di archiviazione](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4.  Compilare i campi come indicato di seguito:

    ![Immissione dei dettagli dell'account di archiviazione](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

-   In **URL** digitare un nome di sottodominio da utilizzare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

-   Scegliere la posizione o il gruppo di affinità per l'account di archiviazione. Specificando un gruppo di affinità è possibile posizionare i servizi cloud nello stesso data center delle risorse di archiviazione.

-   Scegliere se si desidera utilizzare la replica geografica per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a una posizione secondaria qualora si verifichi un errore grave che non può essere gestito nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se determinati requisiti legali o criteri dell'organizzazione richiedono un controllo più rigido rispetto alla posizione dell'archiviazione basata su cloud, è possibile disattivare la replica geografica. Tenere presente che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato.

1.  Fare clic su **Create Storage Account**.

    L'account verrà visualizzato in **Storage Accounts**.

    ![Creazione dell'account di archiviazione completata](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)

Passaggio 3: Preparare la connessione ad Azure
----------------------------------------------

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Azure.

1.  Aprire una finestra di Azure PowerShell.

2.  Digitare:

    `Get-AzurePublishSettingsFile`

    Questo comando apre una finestra del browser e scarica automaticamente un file .publishsettings che contiene un certificato e le informazioni relative alla sottoscrizione di Azure.

3.  Salvare il file .publishsettings.

4.  Digitare:

    `Import-AzurePublishSettingsFile <PathToFile>`

    Dove `<PathToFile>` è il percorso completo del file .publishsettings.

    Per ulteriori informazioni, vedere [Iniziare a utilizzare i cmdlet di Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx).

Passaggio 4: Caricare l'immagine in Azure
-----------------------------------------

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione creato nel passaggio 2 e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di gestione. **PathToVHDFile** è il percorso completo e il nome del file VHD.

Eseguire una delle operazioni seguenti:

-   Nella finestra di Azure PowerShell utilizzata nel passaggio precedente, digitare:

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Per ulteriori informazioni, vedere [Add-AzureVhd](http://msdn.microsoft.com/en-us/library/windowsazure/dn205185.aspx).

-   Utilizzare lo strumento da riga di comando per Linux per caricare l'immagine. È possibile caricare un'immagine utilizzando il comando seguente:

          Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

Informazioni per le distribuzioni non approvate
-----------------------------------------------

In sostanza, per il funzionamento corretto sulla piattaforma, tutte le distribuzioni eseguite in Azure dovranno soddisfare i prerequisiti illustrati di seguito.

L'elenco non è esaustivo, in quanto ogni distribuzione è diversa. È persino possibile che, anche se tutti i criteri elencati di seguito vengono soddisfatti, sia comunque necessario modificare in modo significativo l'immagine per il funzionamento corretto nella piattaforma.

Per questo motivo, è consigliabile iniziare con una delle [immagini di partner approvate](https://www.windowsazure.com/en-us/manage/linux/other-resources/endorsed-distributions/).

L'elenco che segue sostituisce il passaggio 1 del processo per la creazione di un disco rigido virtuale:

1.  Sarà necessario verificare che il kernel in esecuzione integri i driver LIS più recenti per Hyper V o che questi ultimi siano stati correttamente compilati, ovvero resi Open Source. I driver sono disponibili [a questo indirizzo.](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)

2.  Il kernel deve inoltre includere l'ultima versione del driver ATA PiiX utilizzato per il provisioning delle immagini e devono essere state implementate le correzioni con il commit cd006086fa5d91414d8ff9ff2b78fbb593878e3c Data: Ven 4 maggio 2012 21:15:11 (GMT) ata\_piix: defer disks to the Hyper-V drivers by default.

3.  Il file initrd compresso deve essere di dimensioni inferiori a 40 MB (\* questo valore viene aggiornato continuamente, dunque questa informazione potrebbe essere obsoleta).

4.  Modificare la riga di avvio del kernel in Grub o Grub2 per includere i parametri seguenti. In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  È consigliabile modificare l'impostazione di /etc/sysconfig/network/dhcp o equivalente da DHCLIENT\_SET\_HOSTNAME="yes" a DHCLIENT\_SET\_HOSTNAME="no".

6.  Verificare che tutti i dispositivi SCSI montati nel kernel includano un timeout di I/O non inferiore a 300 secondi.

7.  Installare l'agente Linux di Azure seguendo la procedura disponibile nella [Guida dell'agente Linux](https://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/). L'agente è stato rilasciato con licenza Apache 2 e tutti gli aggiornamenti sono disponibili nella [pagina GitHub dell'agente.](http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409)

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


