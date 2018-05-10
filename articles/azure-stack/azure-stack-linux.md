---
title: Aggiungere le immagini Linux allo Stack di Azure
description: Informazioni su come aggiungere le immagini Linux allo Stack di Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Aggiungere le immagini Linux allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile distribuire macchine virtuali Linux (VM) sullo Stack di Azure mediante l'aggiunta di un'immagine basata su Linux in Azure Marketplace dello Stack. Il modo più semplice per aggiungere un'immagine Linux allo Stack di Azure è tramite la gestione del Marketplace. Queste immagini sono state preparate e testate per la compatibilità con lo Stack di Azure.

## <a name="marketplace-management"></a>Gestione di Marketplace

Per scaricare le immagini Linux da Azure Marketplace, utilizzare le procedure descritte nell'articolo seguente. Selezionare le immagini Linux che si desiderano offrire agli utenti nello Stack Azure. 

[Scaricare gli elementi di marketplace da Azure allo Stack Azure](azure-stack-download-azure-marketplace-item.md).

Si noti che sono presenti aggiornamenti frequenti per queste immagini, controllare gestione Marketplace spesso a mantenere aggiornati.

## <a name="prepare-your-own-image"></a>Preparare un'immagine personalizzata

 Laddove possibile, scaricare le immagini disponibili tramite la gestione del Marketplace che sono stati preparati e testato per lo Stack di Azure. 
 
 L'agente Linux di Azure (in genere chiamato `WALinuxAgent` o `walinuxagent`) è necessario, e non tutte le versioni dell'agente funzionerà sullo Stack di Azure. È consigliabile utilizzare versione 2.2.18 o versione successiva se si crea un'immagine personalizzata. Si noti che [init cloud](https://cloud-init.io/) non è supportata nello Stack di Azure in questo momento.

 È possibile preparare la propria immagine Linux seguendo le istruzioni seguenti:

   * [Distribuzioni basate su CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES e openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Aggiungere un'immagine del marketplace
 
Seguire [aggiungere l'immagine del Marketplace](azure-stack-add-vm-image.md). Assicurarsi che il `OSType` parametro è impostato su `Linux`.

Dopo aver aggiunto l'immagine nel Marketplace, viene creato un elemento del Marketplace e gli utenti possono distribuire una macchina virtuale di Linux.
