---
title: Risoluzione dei problemi relativi a dischi collegati a macchine virtuali di Azure | Microsoft Docs
description: Offre collegamenti alle risorse di risoluzione dei problemi per i dischi rigidi virtuali della macchina virtuale di Azure (VHD).
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 81db3a819c28aab8f4b644a940eeb5e6c5ecf3d6
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307173"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Risoluzione dei problemi relativi a dischi collegati a macchine virtuali di Azure 

Le macchine virtuali di Azure (VM) si basano su dischi rigidi virtuali per il disco del sistema operativo ed eventuali dischi dati collegati. I dischi rigidi virtuali vengono archiviati come BLOB di pagine in uno o più account di Archiviazione di Azure. Questo articolo punta al contenuto per la risoluzione dei problemi comuni che possono verificarsi con i dischi rigidi virtuali. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Risolvere i problemi relativi agli errori di eliminazione di risorse di archiviazione per una macchina virtuale

In alcuni casi, è possibile riscontrare un errore durante l'eliminazione di una risorsa di archiviazione quando una macchina virtuale in una distribuzione di Resource Manager contiene dischi rigidi virtuali collegati. Per assistenza nella risoluzione di questo problema, vedere uno degli articoli seguenti: 

  * Su macchine virtuali Linux: [Errori durante l'eliminazione di risorse di archiviazione in una distribuzione di Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Su macchine virtuali Windows: [Errori durante l'eliminazione di risorse di archiviazione in una distribuzione di Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Risolvere i problemi relativi a riavvii imprevisti di VM con dischi rigidi virtuali collegati

Se si verificano riavvii imprevisti di una macchina virtuale con un numero elevato di dischi rigidi virtuali collegati, vedere uno degli articoli seguenti:

  * Su macchine virtuali Linux: [Riavvii imprevisti di VM con dischi rigidi virtuali collegati](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Su macchine virtuali Windows: [Riavvii imprevisti di VM con dischi rigidi virtuali collegati](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
