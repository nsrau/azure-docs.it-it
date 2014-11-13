<properties urlDisplayName="Upload a Linux VHD" pageTitle="Creare e caricare un file VHD Linux in Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Come creare e caricare un disco rigido virtuale Azure (VHD) che contiene un sistema operativo Linux." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creazione e caricamento di un disco rigido virtuale che contiene un sistema operativo Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos" />

# <span id="nonendorsed"></span> </a>Informazioni per le distribuzioni non approvate

**Importante**: il contratto di servizio della piattaforma Azure si applica alle macchine virtuali che eseguono il sistema operativo Linux solo quando viene usata una delle [distribuzioni approvate][distribuzioni approvate]. Tutte le distribuzioni di Linux disponibili nella raccolta immagini di Azure sono distribuzioni approvate con la configurazione richiesta.

-   [Distribuzioni di Linux supportate da Azure][distribuzioni approvate]
-   [Supporto delle immagini Linux in Microsoft Azure][Supporto delle immagini Linux in Microsoft Azure]

Tutte le distribuzioni eseguite in Azure dovranno soddisfare numerosi prerequisiti per poter essere eseguite correttamente sulla piattaforma. Questo articolo non è in alcun modo esaustivo in quanto ogni distribuzione è diversa. È persino possibile che, anche nel caso in cui tutti i criteri seguenti vengano soddisfatti, sia comunque necessario modificare in modo significativo il sistema Linux per garantirne il funzionamento corretto sulla piattaforma.

Per questo motivo, è consigliabile iniziare, laddove possibile, con una delle [distribuzioni approvate di Linux su Azure][distribuzioni approvate di Linux su Azure]. Gli articoli seguenti forniscono le istruzioni per preparare le varie distribuzioni Linux approvate che sono supportate in Azure:

-   **[Distribuzioni basate su CentOS][Distribuzioni basate su CentOS]**
-   **[Oracle Linux][Oracle Linux]**
-   **[SLES e openSUSE][SLES e openSUSE]**
-   **[Ubuntu][Ubuntu]**

La restante parte di questo articolo sarà incentrata sulle indicazioni generali per l'esecuzione della distribuzione Linux in Azure.

## Note generali sull'installazione di Linux

-   il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

-   Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o [RAID][RAID].

-   NUMA non è supportato per VM di dimensioni maggiori a causa di un bug presente nelle versioni del kernel di Linux inferiori a 2.6.37. Questo problema influisce principalmente sulle distribuzioni che usano il kernel Red Hat 2.6.32 upstream. L'installazione manuale dell'agente Linux di Azure (waagent) disabiliterà automaticamente NUMA nella configurazione GRUB per il kernel Linux.

-   Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

-   Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.

## Requisiti del kernel Linux

I driver di Linux Integration Services (LIS) per Hyper-V e Azure vengono forniti direttamente nel kernel Linux upstream. Per molte distribuzioni che includono una versione recente del kernel Linux (cioè 3.x) questi driver sono già disponibili; in alternativa, fornire versioni backport dei driver con i relativi kernel. Questi driver vengono aggiornati costantemente nel kernel upstream con nuove correzioni e funzionalità, pertanto, quando è possibile, è consigliabile eseguire una [distribuzione approvata][distribuzioni approvate di Linux su Azure], che include tali correzioni e aggiornamenti.

Se si esegue una variante di Red Hat Enterprise Linux versioni **6.0-6.3**, è necessario installare i driver LIS più recenti per Hyper-V. I driver sono disponibili [in questa pagina][in questa pagina]. Per quanto riguarda RHEL **6.4 e versioni successive** (e distribuzioni derivate) i driver LIS sono già inclusi nel kernel e pertanto non sono necessari pacchetti di installazione aggiuntivi per eseguire tali sistemi su Azure.

Se è necessario un kernel personalizzato, è consigliabile usare una versione più recente del kernel (ad es. **3.8 e versioni successive**). Per le distribuzioni o i fornitori che gestiscono il proprio kernel, è consigliabile eseguire il backport ai driver LIS dal kernel upstream al kernel personalizzato. Anche se si esegue già una versione relativamente recente del kernel, è generalmente consigliabile tener traccia di eventuali correzioni upstream nei driver LIS e di eseguirne il backport in base alle esigenze. La posizione dei file di origine dei driver LIS è disponibile nel file [MAINTAINERS][MAINTAINERS] nell'albero di origine del kernel Linux:

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

Come minimo, l'assenza delle seguenti patch causa problemi in Azure e quindi tali patch devono essere incluse nel kernel. L'elenco potrebbe non essere esaustivo o completo per tutte le distribuzioni:

-   [ata\_piix: defer disks to the Hyper-V drivers by default][ata\_piix: defer disks to the Hyper-V drivers by default]
-   [storvsc: Account for in-transit packets in the RESET path][storvsc: Account for in-transit packets in the RESET path]

## Agente Linux di Azure

L'[agente Linux di Azure][agente Linux di Azure] (waagent) è necessario per eseguire correttamente il provisioning di una macchina virtuale Linux in Azure. È possibile scaricare la versione più recente, inviare problemi o inviare richieste pull all'[archivio GitHub dell'agente Linux][archivio GitHub dell'agente Linux].

-   L'agente Linux viene rilasciato con la licenza Apache 2.0. Molte distribuzioni forniscono già pacchetti RPM o Debian per l'agente, che pertanto può, in alcuni casi, essere installato e aggiornato con un livello minimo di impegno.

-   L'agente Linux di Azure richiede Python 2.6 o versioni successive.

-   Inoltre, l'agente richiede il modulo python-pyasn1, che nella maggior parte delle distribuzioni viene fornito come pacchetto separato installabile.

-   In alcuni casi l'agente Linux di Azure potrebbe non essere compatibile con NetworkManager. Molti dei pacchetti RPM/Debian forniti dalle distribuzioni configurano NetworkManager come in conflitto con il pacchetto waagent, pertanto disinstalleranno NetworkManager quando si installa il pacchetto dell'agente Linux.

## Requisiti generali di sistema relativi a Linux

-   Modificare la riga di avvio del kernel in GRUB o GRUB2 per includere i parametri seguenti. In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.

    Inoltre, è consigliabile *rimuovere* i parametri seguenti, se esistenti:

        rhgb quiet crashkernel=auto

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.

    L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella VM di almeno 128 MB, il che potrebbe causare problemi con le VM di dimensioni inferiori.

-   Installazione dell'agente Linux di Azure

    L'agente Linux di Azure è necessario per eseguire il provisioning di un'immagine Linux su Azure. Molte distribuzioni forniscono l'agente come pacchetto RPM o Debian (il pacchetto è in genere denominato "WALinuxAgent" or "walinuxagent"). È inoltre possibile installare l'agente manualmente seguendo la procedura indicata nella [Guida all'agente Linux][agente Linux di Azure].

-   Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

-   Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap usando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

-   In "/etc/sudoers" è necessario rimuovere o impostare come commento le righe seguenti, se esistenti:

        Defaults targetpw
        ALL    ALL=(ALL) ALL

-   Infine, eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

-   Arrestare la macchina virtuale e caricare il VHD in Azure.

  [distribuzioni approvate]: ../virtual-machines-linux-endorsed-distributions
  [Supporto delle immagini Linux in Microsoft Azure]: http://support2.microsoft.com/kb/2941892
  [distribuzioni approvate di Linux su Azure]: ../linux-endorsed-distributions
  [Distribuzioni basate su CentOS]: ../virtual-machines-linux-create-upload-vhd-centos
  [Oracle Linux]: ../virtual-machines-linux-create-upload-vhd-oracle
  [SLES e openSUSE]: ../virtual-machines-linux-create-upload-vhd-suse
  [Ubuntu]: ../virtual-machines-linux-create-upload-vhd-ubuntu
  [RAID]: ../virtual-machines-linux-configure-raid
  [in questa pagina]: http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409
  [MAINTAINERS]: https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS
  [ata\_piix: defer disks to the Hyper-V drivers by default]: https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c
  [storvsc: Account for in-transit packets in the RESET path]: https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c
  [agente Linux di Azure]: ../virtual-machines-linux-agent-user-guide
  [archivio GitHub dell'agente Linux]: https://github.com/Azure/WALinuxAgent
