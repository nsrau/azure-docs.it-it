---
title: Risolvere gli errori di avvio nelle macchine virtuali di Azure Linux Documenti Microsoft
description: Questo articolo consente di creare un collegamento ad articoli per la risoluzione degli errori di avvio nelle macchine virtuali Di Azure Linux.This article helps link you to articles to troubleshoot boot errors in Azure Linux Virtual Machines.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408742"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Risolvere gli errori di avvio di Macchine virtuali Linux di AzureTroubleshoot Azure Linux Virtual Machines boot errors

Questo articolo elenca gli errori di avvio comuni che possono essere visualizzati quando si avvia una macchina virtuale Linux (VM) in Microsoft Azure.This article lists the common boot errors that you may receive when you start a Linux virtual machine (VM) in Microsoft Azure. Per altre informazioni sugli errori, vedere gli articoli della sezione **Errori di avvio e soluzioni**.

## <a name="boot-errors-and-solutions"></a>Errori di avvio e soluzioni

* [Salvataggio GRUB](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Passaggi successivi

- [Console seriale per la macchina virtuale](serial-console-linux.md)

Risolvere i problemi di una macchina virtuale Linux collegando il disco del sistema operativo a una macchina virtuale di ripristino usando Azure:Troubleshoot a Linux VM by attaching the OS disk to a recovery VM using Azure:

- [Riparazione di macchine virtuali di AzureAzure VM repair](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Scambio di dischi – questo può essere automatizzato utilizzando:
- [Script di ripristino di Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [Bash Script di recupero](https://github.com/sribs/azure-support-scripts)

- [Cli](troubleshoot-recovery-disks-linux.md)
- [Portale di Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Video di scambio disco:

Se non si ha accesso a GRUB guardare [questo](https://youtu.be/m5t0GZ5oGAc) video e vedere, come è possibile automatizzare facilmente la procedura di scambio del disco per ripristinare la macchina virtuale

## <a name="unofficial-solution"></a>Soluzione non ufficiale

Il ripristino di una macchina virtuale può essere tentato anche con lo script BETA NON supportato [ALAR](https://github.com/malachma/azure-auto-recover)