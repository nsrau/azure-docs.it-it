---
title: Aggiungere le immagini Linux in Azure Stack
description: Informazioni su come aggiungere immagini Linux in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: bee143f7e814884eda3c5dc9017895a8431e76aa
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890553"
---
# <a name="add-linux-images-to-azure-stack"></a>Aggiungere le immagini Linux in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile distribuire macchine virtuali (VM) Linux in Azure Stack tramite l'aggiunta di un'immagine basata su Linux in Azure Stack Marketplace. Il modo più semplice per aggiungere un'immagine Linux in Azure Stack è tramite la gestione di Marketplace. Queste immagini sono state preparate e testate per garantire la compatibilità con Azure Stack.

## <a name="marketplace-management"></a>Gestione di Marketplace

Per scaricare le immagini Linux da Azure Marketplace, usare le procedure descritte nel [Download di elementi di marketplace di Azure ad Azure Stack](azure-stack-download-azure-marketplace-item.md) articolo. Selezionare le immagini Linux che si vuole offrire agli utenti in Azure Stack. 

Si noti che esistono aggiornamenti frequenti delle immagini, quindi controllare gestione Marketplace spesso per mantenere sempre aggiornato.

## <a name="prepare-your-own-image"></a>Preparare un'immagine personalizzata

Laddove possibile, scaricare le immagini disponibili tramite la gestione di Marketplace che sono stati preparati e testate per Azure Stack. 
 
L'agente Linux di Azure (in genere viene chiamato `WALinuxAgent` o `walinuxagent`) è obbligatorio, non tutte le versioni dell'agente funzionerà in Azure Stack. È consigliabile usare versione 2.2.20 o versione successiva se si crea la propria immagine. Si noti che [cloud-init](https://cloud-init.io/) , in questo momento, non è supportato in Azure Stack.

È possibile preparare l'immagine Linux personalizzata seguendo le istruzioni seguenti:

* [Distribuzioni basate su CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES e openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Aggiungere l'immagine di marketplace
 
Seguire [aggiungere l'immagine di Marketplace](azure-stack-add-vm-image.md). Assicurarsi che il `OSType` parametro è impostato su `Linux`.

Dopo aver aggiunto le immagini nel Marketplace, viene creato un elemento del Marketplace e gli utenti possono distribuire una macchina virtuale Linux.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni:

- [Scaricare elementi di marketplace di Azure ad Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Panoramica di Azure Stack Marketplace](azure-stack-marketplace.md)
