---
title: Aggiungere le immagini Linux allo Stack di Azure
description: Informazioni su come aggiungere le immagini Linux allo Stack di Azure.
services: azure-stack
documentationcenter: 
author: anjayajodha
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
ms.openlocfilehash: a8763c01cba4e5a9eaa3b7842b627d6eb9661a95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="add-linux-images-to-azure-stack"></a>Aggiungere le immagini Linux allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit* 

È possibile distribuire macchine virtuali Linux in Azure Stack mediante l'aggiunta di un'immagine basata su Linux in Azure Marketplace dello Stack. Il modo più semplice per aggiungere un'immagine Linux allo Stack di Azure è tramite la gestione del Marketplace.

## <a name="marketplace-management"></a>Gestione di Marketplace

Per scaricare le immagini Linux da Azure Marketplace, utilizzare le procedure descritte nell'articolo seguente. Selezionare le immagini Linux che si desiderano offrire agli utenti nello Stack Azure.

[Scaricare gli elementi di marketplace da Azure allo Stack Azure](azure-stack-download-azure-marketplace-item.md).

## <a name="download-an-image"></a>Scaricare un'immagine

È possibile scaricare ed estrarre le immagini Linux Azure compatibile Stack usando i collegamenti seguenti:


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. Estrarre l'immagine del disco rigido virtuale, se necessario e [aggiungere l'immagine del Marketplace](azure-stack-add-vm-image.md). Assicurarsi che il `OSType` parametro è impostato su `Linux`.
2. Dopo aver aggiunto l'immagine nel Marketplace, viene creato un elemento del Marketplace e gli utenti possono distribuire una macchina virtuale di Linux.

## <a name="prepare-your-own-image"></a>Preparare un'immagine personalizzata

È possibile preparare un'immagine Linux utilizzando una delle seguenti istruzioni:
   
   * [Distribuzioni basate su CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES e openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Scaricare e installare il [agente Linux di Azure](https://github.com/Azure/WALinuxAgent/).
   
    L'agente Linux di Azure versione 2.1.3 o versione successiva è richiesto per eseguire il provisioning di VM Linux nello Stack di Azure. Molte delle distribuzioni elencate in precedenza include già la versione dell'agente o versione successiva come un pacchetto nei loro repository (in genere chiamato `WALinuxAgent` o `walinuxagent`). Tuttavia, se la versione del pacchetto dell'agente di Azure è minore di 2.1.3 (ad esempio, 2.0.18 o inferiore), è necessario installare l'agente manualmente. La versione installata può essere determinata dal nome del pacchetto oppure eseguendo `/usr/sbin/waagent -version` nella macchina virtuale.
   
    Seguire le istruzioni seguenti per installare l'agente Linux di Azure manualmente:
   
   a. Innanzitutto, scaricare l'agente Linux di Azure più recente dal [GitHub](https://github.com/Azure/WALinuxAgent/releases), esempio:
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   b. Decomprimere l'agente di Azure:
     
            # tar -vzxf v2.2.16.tar.gz
   c. Installare python setuptools
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d. Installare l'agente di Azure:
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     I sistemi con Python 2. x e 3. x installati Python side-by-side potrebbe essere necessario eseguire il comando seguente:
     
         sudo python3 setup.py install --register-service
     Per ulteriori informazioni, vedere l'agente Linux di Azure [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
2. [Aggiungere l'immagine del Marketplace](azure-stack-add-vm-image.md). Assicurarsi che il `OSType` parametro è impostato su `Linux`.
3. Dopo aver aggiunto l'immagine nel Marketplace, viene creato un elemento del Marketplace e gli utenti possono distribuire una macchina virtuale di Linux.

## <a name="next-steps"></a>Passaggi successivi
[Panoramica dell'offerta di servizi di Azure Stack](azure-stack-offer-services-overview.md)

