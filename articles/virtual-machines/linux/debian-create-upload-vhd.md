---
title: Preparare un disco rigido virtuale di Linux Debian in Azure | Documentazione Microsoft
description: Informazioni su come creare i file Debian VHD 7 e 8 per la distribuzione in Azure.
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 63970d162c12984d6476bf0b9fc4ab70160eccdb
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017

---
# <a name="prepare-a-debian-vhd-for-azure"></a>Preparare un disco rigido virtuale Debian per Azure
## <a name="prerequisites"></a>Prerequisiti
In questa sezione si presuppone che un sistema operativo Linux Debian sia già stato installato da un file .iso scaricato dal [sito Web di Debian](https://www.debian.org/distrib/) in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione .vhd; Hyper-V è solo un esempio. Per istruzioni sull’uso di Hyper-V, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Note sull'installazione
* Vedere anche [Note generali sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione di Linux per Azure.
* Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet **convert-vhd** .
* Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux di Azure può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Utilizzare Azure-Manage per creare dischi rigidi virtuali Debian
Per la generazione di dischi rigidi virtuali Debian per Azure sono disponibili alcuni strumenti, ad esempio gli script [azure-manage](https://github.com/credativ/azure-manage) di [credativ](http://www.credativ.com/). Questo è l'approccio consigliato rispetto alla creazione di un'immagine da zero. Ad esempio, per creare un disco rigido virtuale Debian 8, eseguire i comandi seguenti per scaricare azure-manage (e le dipendenze) ed eseguire lo script azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparare manualmente un disco rigido virtuale Debian
1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.
3. Impostare come commento la riga per **deb cdrom** in `/etc/apt/source.list` se si configura la macchina virtuale rispetto a un file ISO.
4. Modificare il file `/etc/default/grub` e il parametro **GRUB_CMDLINE_LINUX** come indicato di seguito per includere parametri aggiuntivi del kernel per Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. Ricompilare il grub ed eseguire:
   
        # sudo update-grub
6. Aggiungere i repository Azure di Debian al file /etc/apt/sources.list per Debian 7 o 8:
   
    **Debian 7.x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Installare l'agente Linux di Azure:
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Per Debian 7, è necessario eseguire il kernel basato su 3.16 dal repository wheezy-backports. Creare innanzitutto un file denominato /etc/apt/preferences.d/linux.pref con il seguente contenuto:
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    Quindi eseguire "sudo apt-get install linux-image-amd64" per l'installazione del nuovo kernel.
3. Effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure ed eseguire:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Fare clic su **Azione** -> Arresta nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile usare il disco rigido virtuale Debian per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere i passaggi 2 e 3 nell'articolo [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


