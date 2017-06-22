---
title: Esempi di macchina virtuale di Azure PowerShell | Documentazione Microsoft
description: Esempi di macchina virtuale di Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/16/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c57bf0efc1eb97d34d76483b8889d884ccfd0988
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="azure-virtual-machine-powershell-samples"></a>Esempi di macchina virtuale di Azure PowerShell

La tabella seguente include collegamenti a esempi di script PowerShell che creano e gestiscono macchine virtuali Windows.

| | |
|---|---|
|**Creare macchine virtuali**||
| [Creare una macchina virtuale completamente configurata](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Consente di creare un gruppo di risorse, una macchina virtuale e tutte le risorse correlate.|
| [Creare una macchina virtuale ed eseguire script di configurazione](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione dello script personalizzato di Azure per installare IIS. |
| [Creare una macchina virtuale ed eseguire la configurazione DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione di configurazione dello stato desiderato di Azure per installare IIS. |
| [Creare una VM da un disco del sistema operativo gestito](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una macchina virtuale collegando un disco gestito esistente come disco del sistema operativo. |
| [Creare una VM da uno snapshot](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una macchina virtuale da uno snapshot creando prima un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. |
|**Monitorare le macchine virtuali**||
| [Monitorare una macchina virtuale con Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Consente di creare una macchina virtuale, installare l'agente Operations Management Suite e registrare la macchina virtuale in un'area di lavoro OMS.  |
| | |

