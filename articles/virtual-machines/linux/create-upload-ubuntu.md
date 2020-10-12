---
title: Creazione e caricamento di un VHD Ubuntu Linux in Azure
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure che contiene un sistema operativo Ubuntu Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: 8b34e266214285f6483acca59050780810e62345
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373352"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparare una macchina virtuale Ubuntu per Azure


Ubuntu ora pubblica i dischi rigidi virtuali di Azure ufficiali per il download all'indirizzo [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Se si deve creare un'immagine Ubuntu specializzata per Azure, piuttosto che seguire la procedura manuale riportata sotto si consiglia di iniziare con questi noti VHD funzionanti e personalizzarli in base alle esigenze. Le ultime versioni delle immagini sono sempre disponibili nei seguenti percorsi:

* Ubuntu 16.04/Xenial: [Ubuntu-16,04-server-cloudimg-amd64-disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-server-cloudimg-amd64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Ubuntu Linux in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Note sull'installazione di Ubuntu**

* Vedere anche [Note generali sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione di Linux per Azure.
* Il formato VHDX non è supportato in Azure, solo nei **VHD fissi**.  È possibile convertire il disco in formato VHD usando la console di gestione di Hyper-V o il `Convert-VHD` cmdlet.
* Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Non configurare una partizione swap o file sul disco del sistema operativo. L'agente di provisioning cloud-init può essere configurato in modo da creare un file di scambio o una partizione di scambio sul disco di risorse temporaneo. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in un disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB. Per altre informazioni, vedere [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="manual-steps"></a>Passaggi manuali
> [!NOTE]
> Prima di provare a creare un'immagine personalizzata di Ubuntu per Azure, è consigliabile usare invece le immagini predefinite e testate [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) .
> 
> 

1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3. Sostituire i repository correnti nell'immagine per usare il repository di Azure di Ubuntu.

    Prima di modificare `/etc/apt/sources.list`, è consigliabile eseguire un backup:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16,04 e Ubuntu 18,04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Le immagini di Ubuntu Azure ora usano il [kernel personalizzato di Azure](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel). Aggiornare il sistema operativo al kernel personalizzato di Azure più recente e installare gli strumenti Linux di Azure, incluse le dipendenze di Hyper-V, eseguendo i comandi seguenti:

    Ubuntu 16,04 e Ubuntu 18,04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Modificare la riga di avvio del kernel per Grub in modo da includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/etc/default/grub` in un editor di testo, trovare la variabile denominata `GRUB_CMDLINE_LINUX_DEFAULT` (o aggiungerla, se necessario) e modificarla in modo da includere i parametri seguenti:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Salvare e chiudere il file e quindi eseguire `sudo update-grub`. In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto tecnico di Azure nella risoluzione dei problemi di debug.

6. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.

7. Installare cloud-init (agente di provisioning) e l'agente Linux di Azure (gestore estensioni Guest). Cloud-init USA Netplan per configurare la configurazione della rete di sistema durante il provisioning e ogni avvio successivo.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  Il pacchetto `walinuxagent` potrebbe rimuovere i pacchetti `NetworkManager` e `NetworkManager-gnome`, se installati.

8. Rimuovere le configurazioni predefinite di cloud-init e gli artefatti Netplan residui che possono essere in conflitto con il provisioning cloud-init in Azure:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Configurare cloud-init per eseguire il provisioning del sistema usando l'origine dati di Azure:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Configurare l'agente Linux di Azure in modo che si basi su cloud-init per eseguire il provisioning. Per ulteriori informazioni su queste opzioni, vedere il [progetto WALinuxAgent](https://github.com/Azure/WALinuxAgent) .

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Pulire gli artefatti e i log di runtime dell'agente Linux di Azure e cloud-init:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    > [!NOTE]
    > Il `sudo waagent -force -deprovision+user` comando proverà a pulire il sistema e a renderlo idoneo per il nuovo provisioning. L' `+user` opzione Elimina l'ultimo account utente di cui è stato effettuato il provisioning e i dati associati.

    > [!WARNING]
    > Il deprovisioning usando il comando precedente non garantisce che l'immagine venga cancellata da tutte le informazioni riservate ed è adatta per la ridistribuzione.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V.

14. Azure accetta solo dischi rigidi virtuali a dimensione fissa. Se il disco del sistema operativo della macchina virtuale non è un VHD a dimensione fissa, usare il `Convert-VHD` cmdlet di PowerShell e specificare l' `-VHDType Fixed` opzione. Per un esempio, vedere la documentazione relativa a `Convert-VHD` [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=win10-ps).


## <a name="next-steps"></a>Passaggi successivi
È ora possibile usare il disco rigido virtuale Ubuntu Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere [Creare una macchina virtuale Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).
