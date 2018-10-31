---
title: Esempi di macchina virtuale di Azure PowerShell | Documentazione Microsoft
description: Esempi di macchina virtuale di Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/19/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3571ccfbb2f15738fd951c0fa55248745eac281a
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637434"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Esempi di macchina virtuale di Azure PowerShell

La tabella seguente include collegamenti a esempi di script di PowerShell che creano e gestiscono macchine virtuali (VM) Windows.

| | |
|---|---|
|**Creare macchine virtuali**||
| [Creare rapidamente una macchina virtuale](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un gruppo di risorse, una macchina virtuale e tutte le risorse correlate, con un numero minimo di istruzioni.|
| [Creare una macchina virtuale completamente configurata](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un gruppo di risorse, una macchina virtuale e tutte le risorse correlate.|
| [Creare macchine virtuali a disponibilità elevata](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea più macchine virtuali in una configurazione a disponibilità elevata e con bilanciamento del carico.|
| [Creare una macchina virtuale ed eseguire uno script di configurazione](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione dello script personalizzato di Azure per installare IIS. |
| [Creare una macchina virtuale ed eseguire una configurazione DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione di configurazione dello stato desiderato di Azure per installare IIS. |
| [Caricare un disco rigido virtuale e creare VM](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Carica un file del disco rigido virtuale locale in Azure, crea un'immagine dal disco rigido virtuale e quindi crea una macchina virtuale da tale immagine. |
| [Creare una VM da un disco del sistema operativo gestito](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una macchina virtuale collegando un disco gestito esistente come disco del sistema operativo. |
| [Creare una VM da uno snapshot](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una macchina virtuale da uno snapshot creando prima un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. |
|**Gestire l'archiviazione**||
| [Creare un disco gestito da un disco rigido virtuale nella stessa sottoscrizione o in una sottoscrizione diversa](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un disco gestito da un disco rigido virtuale specializzato come disco del sistema operativo o da un disco rigido virtuale di dati come disco dati nella stessa sottoscrizione o in una sottoscrizione diversa.  |
| [Creare un disco gestito da uno snapshot](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un disco gestito da uno snapshot. |
| [Copiare un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copia un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa nella stessa area del disco gestito padre. 
| [Esportare uno snapshot come disco rigido virtuale in un account di archiviazione](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Esporta uno snapshot gestito come disco rigido virtuale in un account di archiviazione in un'area diversa. |
| [Esportare il disco rigido virtuale di un disco gestito in un account di archiviazione](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Esporta il disco rigido virtuale sottostante di un disco gestito in un account di archiviazione in un'area diversa. |
| [Creare uno snapshot da un disco rigido virtuale](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea uno snapshot da un disco rigido virtuale e quindi usa tale snapshot per creare rapidamente più dischi gestiti identici.  |
| [Copiare uno snapshot nella stessa sottoscrizione o in una sottoscrizione diversa](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Copia uno snapshot nella stessa sottoscrizione o in una sottoscrizione diversa nella stessa area dello snapshot padre. |
|**Proteggere le macchine virtuali**||
| [Crittografare una macchina virtuale e i relativi dischi dati](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Crea un'istanza di Azure Key Vault, una chiave di crittografia e un'entità servizio, quindi crittografa una macchina virtuale. |
|**Monitorare le macchine virtuali**||
| [Monitorare una macchina virtuale con Log Analytics](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una macchina virtuale, installa l'agente di Azure Log Analytics e registra la macchina virtuale in un'area di lavoro di Log Analytics.  |
| | |
