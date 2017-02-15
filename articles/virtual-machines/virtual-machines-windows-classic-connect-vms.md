---
title: Connettere le macchine virtuali Windows in un servizio cloud | Microsoft Docs
description: Connettere le macchine virtuali Windows create con il modello di distribuzione classica a un servizio cloud di Azure o a una rete virtuale.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 75f130c20819c8bf89f9f850ec41f2f137180bcc


---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Connettere le macchine virtuali Windows create con il modello di distribuzione classica con un servizio cloud o rete virtuale
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Le macchine virtuali Windows create con il modello di distribuzione classica vengono sempre inserite in un servizio cloud. Il servizio cloud funge da contenitore e fornisce un nome DNS pubblico univoco, un indirizzo IP pubblico e un set di endpoint per accedere alla macchina virtuale su Internet. Il servizio cloud può essere in una rete virtuale, ma questo non è un requisito. È inoltre possibile [connettere le macchine virtuali Linux con una rete virtuale o un servizio cloud](virtual-machines-linux-classic-connect-vms.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

Se un servizio cloud non si trova in una rete virtuale, è detto servizio cloud *autonomo* . Le macchine virtuali in un servizio cloud autonomo possono comunicare solo con altre macchine virtuali usando i nomi DNS pubblici delle altre macchine virtuali e il traffico verrà trasmesso attraverso Internet. Se un servizio cloud si trova in una rete virtuale, le macchine virtuali in tale servizio cloud possono comunicare con tutte le altre macchine virtuali nella rete virtuale senza inviare traffico su Internet.

Se si inseriscono le macchine virtuali nello stesso servizio cloud autonomo, si potranno continuare a usare il bilanciamento del carico e i set di disponibilità. Per altre informazioni, vedere gli articoli relativi a [bilanciamento del carico delle macchine virtuali](virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gestione della disponibilità delle macchine virtuali](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tuttavia, non è possibile organizzare le macchine virtuali nelle subnet o connettere un servizio cloud autonomo alla rete locale. Ad esempio:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato una macchina virtuale, è consigliabile [aggiungere un disco dati](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) , in modo che i servizi e i carichi di lavoro possano usarlo per archiviare i dati. 




<!--HONumber=Nov16_HO3-->


