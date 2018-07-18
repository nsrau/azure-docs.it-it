---
title: Esempi dell'interfaccia della riga di comando di Azure per Windows | Documentazione Microsoft
description: Esempi dell'interfaccia della riga di comando di Azure per Windows
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
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c9837ae7b218fd4fdf6d0b97c0218fdfc9de3c53
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726194"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Esempi dell'interfaccia della riga di comando di Azure per macchine virtuali Windows

La tabella seguente fornisce collegamenti a script Bash, compilati tramite l'interfaccia della riga di comando di Azure, che distribuiscono macchine virtuali di Windows.

| | |
|---|---|
|**Creare macchine virtuali**||
| [Creare una macchina virtuale](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Consente di creare una macchina virtuale Windows con la configurazione minima. |
| [Creare una macchina virtuale completamente configurata](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Consente di creare un gruppo di risorse, una macchina virtuale e tutte le risorse correlate.|
| [Creare macchine virtuali a disponibilità elevata](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Consente di creare più macchine virtuali in una configurazione a disponibilità elevata e con bilanciamento del carico. |
| [Creare una macchina virtuale ed eseguire script di configurazione](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione dello script personalizzato di Azure per installare IIS. |
| [Creare una macchina virtuale ed eseguire la configurazione DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Consente di creare una macchina virtuale e usa l'estensione di configurazione dello stato desiderato di Azure per installare IIS. |
|**Macchine virtuali di rete**||
| [Proteggere il traffico di rete tra le macchine virtuali](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Consente di creare due macchine virtuali, tutte le risorse correlate, un gruppo di sicurezza di rete interno e uno esterno. |
|**Proteggere le macchine virtuali**||
| [Crittografare una macchina virtuale e i dischi dati](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'istanza di Azure Key Vault, una chiave di crittografia e un'entità servizio, quindi crittografa una macchina virtuale. |
|**Monitorare le macchine virtuali**||
| [Monitorare una macchina virtuale con Operations Management Suite](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Consente di creare una macchina virtuale, installare l'agente Operations Management Suite e registrare la macchina virtuale in un'area di lavoro OMS.  |
| | |
