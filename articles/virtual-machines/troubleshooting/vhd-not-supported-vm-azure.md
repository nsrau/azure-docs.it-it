---
title: VHD non è supportato quando si crea una macchina virtuale in Azure | Microsoft Docs
description: Questo articolo consente di correggere gli errori del disco rigido virtuale durante l'esecuzione di una macchina virtuale in Microsoft Azure.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: ff4822b513ed2aea6a18ba45bffc1d060ee2410e
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937527"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>VHD non è supportato quando si crea una macchina virtuale in Azure

Questo articolo consente di correggere gli errori del disco rigido virtuale durante l'esecuzione di macchine virtuali in Windows o Linux.

## <a name="symptoms"></a>Sintomi

Quando si crea una macchina virtuale in Microsoft Azure usando un disco rigido virtuale caricato, la distribuzione ha esito negativo e restituisce il messaggio di errore seguente: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Causa

Questo problema si verifica per uno dei motivi seguenti:

- Il disco rigido virtuale non è conforme all'allineamento di 1 MB (offset). Le dimensioni del disco supportate devono essere pari a 1 MB * N. Ad esempio, il disco deve essere 102.401 MB.
- Il disco rigido virtuale è danneggiato o non è supportato. 

## <a name="resolution"></a>Soluzione

> [!NOTE]
> Per eseguire la correzione seguente, il cliente dovrà eseguire questi passaggi prima di caricare il disco rigido virtuale in Azure.

Per risolvere il problema, ridimensionare il disco per garantire la conformità con 1 MB di allineamento:

- Per risolvere il problema in Windows, usare il [cmdlet di PowerShell Resize-VHD](https://docs.microsoft.com/powershell/module/hyper-v/resize-vhd). Si noti che **Resize-VHD** non è un cmdlet di Azure PowerShell.

  1. [Installare il ruolo Hyper-V in Windows Server](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [Convertire il disco virtuale in un disco rigido virtuale a dimensione fissa](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Per risolvere il problema in Linux, usare il [comando qemu-img](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

Per altre informazioni su come creare e caricare un disco rigido virtuale per la creazione di una macchina virtuale di Azure, vedere gli articoli seguenti:

- [Caricare e creare una VM Linux da un'immagine disco personalizzata usando l'interfaccia della riga di comando di Azure 1.0](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)
- [Creare e caricare un disco rigido virtuale Windows Server in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

Problemi continui potrebbero indicare un disco rigido virtuale danneggiato. In questa situazione, si consiglia di ricompilare il disco rigido virtuale da zero.

Per altre informazioni, vedere gli articoli seguenti:

- [Informazioni sul disco rigido virtuale Windows](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#about-vhds)
- [Informazioni sul disco rigido virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds#about-vhds)