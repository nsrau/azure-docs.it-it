---
title: Aggiungere le immagini Linux allo Stack di Azure
description: Informazioni su come aggiungere le immagini Linux allo Stack di Azure.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Aggiungere le immagini Linux allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile distribuire macchine virtuali Linux (VM) sullo Stack di Azure mediante l'aggiunta di un'immagine basata su Linux in Azure Marketplace dello Stack. Il modo più semplice per aggiungere un'immagine Linux allo Stack di Azure è tramite la gestione del Marketplace. Queste immagini sono state preparate e testate per la compatibilità con lo Stack di Azure.

## <a name="marketplace-management"></a>Gestione di Marketplace

Per scaricare le immagini Linux da Azure Marketplace, utilizzare le procedure descritte nell'articolo seguente. Selezionare le immagini Linux che si desiderano offrire agli utenti nello Stack Azure.

[Scaricare gli elementi di marketplace da Azure allo Stack Azure](azure-stack-download-azure-marketplace-item.md).

## <a name="prepare-your-own-image"></a>Preparare un'immagine personalizzata

È possibile preparare un'immagine Linux utilizzando una delle seguenti istruzioni:

   * [Distribuzioni basate su CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES e openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Scaricare e installare il [agente Linux di Azure](https://github.com/Azure/WALinuxAgent/).

    L'agente Linux di Azure versione 2.2.2 o versione successiva è necessario eseguire il provisioning non funzionano le VM Linux su Stack di Azure e alcune versioni (2.2.12 e 2.2.13, per gli esempi). Molte delle distribuzioni elencate in precedenza include già una versione dell'agente (in genere chiamato `WALinuxAgent` o `walinuxagent`). Se si crea un'immagine personalizzata, è necessario installare manualmente l'agente. La versione installata può essere determinata dal nome del pacchetto o eseguendo `/usr/sbin/waagent -version` nella macchina virtuale.

    Seguire le istruzioni seguenti per installare l'agente Linux di Azure manualmente:

   a. Innanzitutto, scaricare l'agente Linux di Azure più recente dal [GitHub](https://github.com/Azure/WALinuxAgent/releases), esempio:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   b. Decomprimere l'agente di Azure:

            # tar -vzxf v2.2.21.tar.gz
   c. Installare python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. Installare l'agente di Azure:

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     I sistemi con Python 2. x e 3. x installati Python side-by-side potrebbe essere necessario eseguire il comando seguente:

         sudo python3 setup.py install --register-service
     Per ulteriori informazioni, vedere l'agente Linux di Azure [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
2. [Aggiungere l'immagine del Marketplace](azure-stack-add-vm-image.md). Assicurarsi che il `OSType` parametro è impostato su `Linux`.
3. Dopo aver aggiunto l'immagine nel Marketplace, viene creato un elemento del Marketplace e gli utenti possono distribuire una macchina virtuale di Linux.

## <a name="next-steps"></a>Passaggi successivi
[Panoramica dell'offerta di servizi di Azure Stack](azure-stack-offer-services-overview.md)
