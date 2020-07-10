---
title: Esempi dell'interfaccia della riga di comando di Azure
description: Esempi dell'interfaccia della riga di comando di Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 2d855a524129bb56248e270d43b300e5dfe97e27
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207456"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Esempi dell'interfaccia della riga di comando di Azure per macchine virtuali Linux

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

| Script | Descrizione |
|---|---|
|**Creare macchine virtuali**||
| [Creare una macchina virtuale](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Consente di creare una macchina virtuale Linux con la configurazione minima. |
| [Creare una macchina virtuale completamente configurata](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Consente di creare un gruppo di risorse, una macchina virtuale e tutte le risorse correlate.|
| [Creare macchine virtuali a disponibilità elevata](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Consente di creare più macchine virtuali in una configurazione a disponibilità elevata e con bilanciamento del carico. |
| [Creare una VM ed eseguire uno script di configurazione](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione dello script personalizzato di Azure per installare NGINX. |
| [Creare una VM con WordPress installato](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione dello script personalizzato di Azure per installare WordPress. |
| [Creare una VM da un disco del sistema operativo gestito](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una macchina virtuale collegando un disco gestito esistente come disco del sistema operativo. |
| [Creare una VM da uno snapshot](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una macchina virtuale da uno snapshot creando prima un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. |
|**Gestire l'archiviazione**||
| [Creare un disco gestito da un disco rigido virtuale](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Consente di creare un disco gestito da un disco rigido virtuale specializzato come disco del sistema operativo o da un disco rigido virtuale di dati come disco dati.  |
| [Creare un disco gestito da uno snapshot](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea un disco gestito da uno snapshot. |
| [Copiare un disco gestito nella stessa sottoscrizione o in una diversa](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa ma nella stessa area in cui si trova il disco gestito padre. 
| [Esportare uno snapshot come disco rigido virtuale in un account di archiviazione](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Esporta uno snapshot gestito come disco rigido virtuale in un account di archiviazione in un'area diversa. |
| [Esportare il disco rigido virtuale di un disco gestito in un account di archiviazione](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Esporta il disco rigido virtuale sottostante di un disco gestito in un account di archiviazione di un'area diversa. |
| [Copiare uno snapshot nella stessa sottoscrizione o in una diversa](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia uno snapshot nella stessa sottoscrizione o in una sottoscrizione diversa ma nella stessa area dello snapshot padre. |
|**Macchine virtuali di rete**||
| [Proteggere il traffico di rete tra le macchine virtuali](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Consente di creare due macchine virtuali, tutte le risorse correlate, un gruppo di sicurezza di rete interno e uno esterno. |
|**Proteggere le macchine virtuali**||
| [Crittografare una VM e i dischi dati](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea un'istanza di Azure Key Vault, una chiave di crittografia e un'entità servizio, quindi crittografa una macchina virtuale. |
|**Monitorare le macchine virtuali**||
| [Monitorare una macchina virtuale con i log di Monitoraggio di Azure](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una macchina virtuale, installa l'agente di Log Analytics e registra la macchina virtuale in un'area di lavoro Log Analytics.  |
|**Risolvere i problemi delle macchine virtuali**||
| [Risolvere i problemi del disco del sistema operativo della VM](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Consente di montare il disco del sistema operativo da una macchina virtuale come disco dati in una seconda macchina virtuale. |
| | |
