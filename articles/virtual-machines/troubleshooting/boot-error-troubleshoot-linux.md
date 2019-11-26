---
title: Risolvere gli errori di avvio in Azure macchine virtuali Linux | Microsoft Docs
description: Questo articolo consente di collegare gli articoli per risolvere gli errori di avvio in Azure macchine virtuali Linux.
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
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408742"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Risolvere gli errori di avvio macchine virtuali Linux di Azure

Questo articolo elenca gli errori di avvio comuni che è possibile ricevere quando si avvia una macchina virtuale (VM) Linux in Microsoft Azure. Per altre informazioni sugli errori, vedere gli articoli della sezione **Errori di avvio e soluzioni**.

## <a name="boot-errors-and-solutions"></a>Errori di avvio e soluzioni

* [Salvataggio di GRUB](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Passaggi successivi

- [Console seriale per la macchina virtuale](serial-console-linux.md)

Risolvere i problemi di una VM Linux connettendo il disco del sistema operativo a una VM di ripristino tramite Azure:

- [Ripristino della macchina virtuale di Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Swap su disco: questa operazione può essere automatizzata usando:
- [Script di ripristino di Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [Script di ripristino bash](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Portale di Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Video di scambio disco:

Se non si ha accesso a GRUB, guardare [questo](https://youtu.be/m5t0GZ5oGAc) video e vedere come è possibile automatizzare facilmente la procedura di scambio del disco per ripristinare la macchina virtuale

## <a name="unofficial-solution"></a>Soluzione non ufficiale

Il ripristino di una macchina virtuale può anche essere eseguito con lo script BETA non supportato [Alar](https://github.com/malachma/azure-auto-recover)