---
title: Creazione e caricamento di un file VHD Linux in Azure
description: Come creare e caricare un disco rigido virtuale Azure (VHD) che contiene un sistema operativo Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: 1f9512e4eabf76edecef594b6b6498782725c019
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671609"
---
# <a name="information-for-non-endorsed-distributions"></a>Informazioni per le distribuzioni non approvate
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Il contratto di servizio della piattaforma Azure si applica alle macchine virtuali che eseguono il sistema operativo Linux solo quando viene usata una delle [distribuzioni approvate](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per queste distribuzioni approvate, in Azure Marketplace vengono fornite immagini Linux preconfigurate.

* [Linux in Azure - Distribuzioni supportate](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Supporto delle immagini Linux in Microsoft Azure](https://support.microsoft.com/kb/2941892)

Tutte le distribuzioni in esecuzione in Azure devono soddisfare alcuni prerequisiti. Questo articolo non può essere esaustivo, perché ogni distribuzione è diversa. Pur soddisfacendo i criteri seguenti, potrebbe essere necessario modificare in modo significativo il sistema Linux perché funzioni correttamente.

È consigliabile iniziare con una delle [distribuzioni Linux in Azure approvate](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Gli articoli seguenti descrivono come preparare le diverse distribuzioni approvate supportate in Azure:

* **[Distribuzioni basate su CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES e openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Questo articolo è incentrato sulle linee guida generali per l'esecuzione della distribuzione Linux in Azure.

## <a name="general-linux-installation-notes"></a>Note generali sull'installazione di Linux
* Il formato di disco rigido virtuale di Hyper-V (VHDX) non è supportato in Azure, ma sono supportati solo *dischi rigidi virtuali fissi*.  È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd). Se si usa VirtualBox, selezionare **dimensioni fisse** anziché il valore predefinito allocato in modo dinamico durante la creazione del disco.
* Azure supporta solo macchine virtuali di generazione 1. È possibile convertire una macchina virtuale di prima generazione dal formato VHDX al formato VHD e da disco a espansione dinamica a disco a dimensione fissa. Non è possibile modificare la generazione di una macchina virtuale. Per ulteriori informazioni, vedere [Creare una macchina virtuale di prima o seconda generazione in Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* La dimensione massima consentita per il disco rigido virtuale è 1023 GB.
* Quando si installa il sistema operativo Linux, è consigliabile usare partizioni standard anziché LVM (Logical Volume Manager), che è spesso l'impostazione predefinita per numerose installazioni. In questo modo, sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se sarà mai necessario collegare un disco del sistema operativo a un'altra macchina virtuale identica per la risoluzione dei problemi. È possibile usare [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) su dischi di dati.
* Per il montaggio di file system UDF, è necessario il supporto del kernel. Al primo avvio in Azure la configurazione del provisioning viene passata alla macchina virtuale Linux tramite supporti di memorizzazione formattati con UDF, collegati al guest. È necessario che l'agente Linux di Azure possa montare il file system UDF per leggerne la configurazione ed effettuare il provisioning della macchina virtuale.
* I kernel Linux con versione precedente a 2.6.37 non supportano NUMA in Hyper-V con macchine virtuali di dimensioni maggiori. Questo problema incide principalmente sulle distribuzioni precedenti che usano il kernel upstream Red Hat 2.6.32. Il problema è stato risolto in Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504). I sistemi che eseguono kernel personalizzati con versione precedente a 2.6.37 o kernel basati su RHEL con versione precedente a 2.6.32-504 devono impostare il parametro di avvio `numa=off` nella riga di comando del kernel in rub.conf. Per altre informazioni, vedere l'articolo [KB 436883](https://access.redhat.com/solutions/436883) di Red Hat.
* Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap nel disco temporaneo delle risorse, come descritto nei passaggi seguenti.
* Le dimensioni di tutti i dischi rigidi virtuali in Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB, come descritto nei passaggi seguenti.

### <a name="installing-kernel-modules-without-hyper-v"></a>Installazione dei moduli kernel senza Hyper-V
Poiché Azure viene eseguito nell'hypervisor Hyper-V, Linux richiede l'esecuzione di alcuni moduli kernel in Azure. Se una macchina virtuale è stata creata esternamente ad Hyper-V, i programmi di installazione di Linux potrebbero non includere i driver per Hyper-V nel disco RAM iniziale (initrd o initramfs), a meno che la macchina virtuale non riconosca di essere in esecuzione in un ambiente Hyper-V. Quando si usa un sistema di virtualizzazione diverso (come Virtualbox, KVM e così via) per preparare l'immagine Linux, potrebbe essere necessario ricreare initrd in modo che almeno i moduli kernel hv_vmbus e hv_storvsc siano disponibili nel disco RAM iniziale.  Questo è un problema noto per i sistemi basati sulla distribuzione upstream di Red Hat e può esserlo anche per altri sistemi.

Il meccanismo per ricreare l'immagine initrd o initramfs può variare a seconda della distribuzione. Per la procedura appropriata, consultare la documentazione della distribuzione o il supporto.  Ecco un esempio di come ricompilare l'immagine initrd tramite l'utilità `mkinitrd`:

1. Eseguire il backup dell'immagine initrd esistente:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Ricompilare initrd con i moduli kernel hv_vmbus e hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Ridimensionamento dei dischi rigidi virtuali
Le dimensioni virtuali delle immagini VHD su Azure devono essere allineate a 1 MB.  In genere i dischi rigidi virtuali creati tramite Hyper-V sono già allineati correttamente.  Se il disco rigido virtuale non è allineato correttamente, è possibile che venga visualizzato un messaggio di errore simile al seguente quando si tenta di creare un'immagine dal disco rigido virtuale.

* Il disco rigido virtuale http:\//\<mystorageaccount >.blob.core.windows.net/vhds/MyLinuxVM.vhd ha una dimensione virtuale non supportata di 21475270656 byte. La dimensione deve essere un numero intero (in MB).

In questo caso, ridimensionare la macchina virtuale usando la console di gestione di Hyper-V o il cmdlet [Resize-VHD](https://technet.microsoft.com/library/hh848535.aspx) di PowerShell.  Se l'ambiente di esecuzione non è Windows, è consigliabile usare `qemu-img` per convertire (se necessario) e ridimensionare il disco rigido virtuale.

> [!NOTE]
> Esiste un [bug noto nelle versioni di qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611) >= 2.2.1 che produce un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. È consigliabile usare `qemu-img` 2.2.0 o versione precedente o 2.6 o versione successiva.
> 

1. Il ridimensionamento diretto del disco rigido virtuale con strumenti quali `qemu-img` o `vbox-manage` può determinare un disco rigido virtuale che non può essere avviato.  È consigliabile convertire prima il disco rigido virtuale in un'immagine disco RAW.  Se l'immagine della macchina virtuale è stata creata come immagine disco RAW (comportamento predefinito per alcuni hypervisor come KVM), è possibile ignorare questo passaggio.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Calcolare le dimensioni necessarie dell'immagine disco per assicurarsi che le dimensioni virtuali siano allineate a 1 MB.  Lo script della shell Bash usa `qemu-img info` per determinare le dimensioni virtuali dell'immagine disco, quindi calcola le dimensioni al successivo 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Ridimensionare il disco non formattato usando `$rounded_size` impostato come descritto sopra.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Riconvertire ora il disco RAW di nuovo in disco rigido virtuale a dimensione fissa.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   In alternativa, con qemu versione 2.6+, includere l'opzione `force_size`.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Requisiti del kernel Linux

I driver di Linux Integration Services (LIS) per Hyper-V e Azure vengono forniti direttamente nel kernel Linux upstream. Per molte distribuzioni che includono una versione recente del kernel Linux (ad esempio, 3.x) questi driver sono già disponibili o, in alternativa, forniscono versioni backport dei driver con i rispettivi kernel.  Poiché questi driver vengono costantemente aggiornati nel kernel upstream con nuove correzioni e funzionalità, se possibile è consigliabile eseguire una [distribuzione approvata](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) che includa questi aggiornamenti e correzioni.

Se si esegue una variante di Red Hat Enterprise Linux versioni dalla 6.0 alla 6.3, è necessario installare i [driver LIS più recenti per Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). A partire da RHEL 6.4 e versioni successive (e distribuzioni derivate) i driver LIS sono già inclusi nel kernel e di conseguenza non sono necessari pacchetti di installazione aggiuntivi.

Se è necessario un kernel personalizzato, è consigliabile usare una versione più recente del kernel, ad esempio la versione 3.8 e successive. Per le distribuzioni o i fornitori che gestiscono il proprio kernel, è consigliabile eseguire regolarmente il backport ai driver LIS dal kernel upstream al kernel personalizzato.  Anche se si esegue già una versione relativamente recente del kernel, è in genere consigliabile tenere traccia di eventuali correzioni upstream nei driver LIS ed eseguirne il backport in base alle esigenze. I file di origine dei driver LIS si trovano nel file [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) nell'albero di origine del kernel Linux:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
Le patch seguenti devono essere incluse nel kernel. Questo elenco non può essere completato per tutte le distribuzioni.

* [ata_piix: defer disks to the Hyper-V drivers by default](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Account per i pacchetti in transito nel percorso di ripristino](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: avoid usage of WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Disabilitare la scrittura stesso per RAID e i driver della scheda host virtuale](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: Correzione di dereferenziazione del puntatore NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ring buffer failures may result in I/O freeze](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: protect against double execution of __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Agente Linux di Azure
L'[agente Linux di Azure](../extensions/agent-linux.md) `waagent` effettua il provisioning di una macchina virtuale Linux in Azure. È possibile ottenere la versione più recente, inviare problemi o inviare richieste pull al [repository GitHub dell'agente Linux](https://github.com/Azure/WALinuxAgent).

* L'agente Linux viene rilasciato con la licenza Apache 2.0. Molte distribuzioni forniscono già pacchetti RPM o Debian per l'agente, che possono essere installati e aggiornati in tutta semplicità.
* L'agente Linux di Azure richiede Python 2.6 o versioni successive.
* Inoltre, l'agente richiede il modulo python-pyasn1, La maggior parte delle distribuzioni fornisce questo modulo come pacchetto separato per l'installazione.
* In alcuni casi l'agente Linux di Azure può non essere compatibile con NetworkManager. Molti dei pacchetti RPM/Debian forniti dalle distribuzioni configurano NetworkManager come in conflitto con il pacchetto waagent. In questi casi, disinstalleranno NetworkManager quando si installa il pacchetto dell'agente Linux.
* La versione dell'agente Linux di Azure deve essere successiva alla [versione minima supportata](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Requisiti generali di sistema relativi a Linux

1. Modificare la riga di avvio del kernel in GRUB o GRUB2 in modo da includere i parametri seguenti, perché tutti i messaggi della console vengano inviati alla prima porta seriale. Questi messaggi possono aiutare il supporto di Azure per il debug di eventuali problemi.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    È inoltre consigliabile *rimuovere* i parametri seguenti, se presenti.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Un avvio grafico e silenzioso non è utile in un ambiente cloud in cui tutti i log devono essere inviati alla porta seriale. È possibile configurare l'opzione `crashkernel`, se necessario, ma tenere presente che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo può causare problemi con le macchine virtuali di dimensioni inferiori.

1. Installare l'agente Linux di Azure.
  
    L'agente Linux di Azure è necessario per eseguire il provisioning di un'immagine Linux su Azure.  Molte distribuzioni forniscono l'agente come pacchetto RPM o Debian (il pacchetto è in genere denominato WALinuxAgent o walinuxagent).  È inoltre possibile installare l'agente manualmente seguendo la procedura indicata nella [Guida all'agente Linux](../extensions/agent-linux.md).

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questa configurazione è in genere quella predefinita.

1. Non creare l'area di swap sul disco del sistema operativo.
  
    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure (passaggio 2 sopra), modificare i parametri seguenti in /etc/waagent.conf nel modo necessario.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Dopo aver eseguito `waagent -force -deprovision`, in Virtualbox è possibile che venga visualizzato un errore che indica `[Errno 5] Input/output error`. Questo messaggio di errore non è critico e può essere ignorato.

* Arrestare la macchina virtuale e caricare il VHD in Azure.

