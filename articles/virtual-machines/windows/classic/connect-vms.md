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
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: 0c7a21461e5bb111c4359df8e949d48382b591c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Connettere le macchine virtuali Windows create con il modello di distribuzione classica con un servizio cloud o rete virtuale
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

Le macchine virtuali Windows create con il modello di distribuzione classica vengono sempre inserite in un servizio cloud. Il servizio cloud funge da contenitore e fornisce un nome DNS pubblico univoco, un indirizzo IP pubblico e un set di endpoint per accedere alla macchina virtuale su Internet. Il servizio cloud può essere in una rete virtuale, ma questo non è un requisito. È inoltre possibile [connettere le macchine virtuali Linux con una rete virtuale o un servizio cloud](../../linux/classic/connect-vms.md).

Se un servizio cloud non si trova in una rete virtuale, è detto servizio cloud *autonomo* . Le macchine virtuali in un servizio cloud autonomo comunicano con altre macchine virtuali usando i nomi DNS pubblici delle altre macchine virtuali e il traffico viene trasmesso attraverso Internet. Se un servizio cloud si trova in una rete virtuale, le macchine virtuali in tale servizio cloud possono comunicare con tutte le altre macchine virtuali nella rete virtuale senza inviare traffico su Internet.

Se si inseriscono le macchine virtuali nello stesso servizio cloud autonomo, si potranno continuare a usare il bilanciamento del carico e i set di disponibilità. Per altre informazioni, vedere gli articoli relativi a [bilanciamento del carico delle macchine virtuali](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gestione della disponibilità delle macchine virtuali](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tuttavia, non è possibile organizzare le macchine virtuali nelle subnet o connettere un servizio cloud autonomo alla rete locale. Ad esempio:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato una macchina virtuale, è consigliabile [aggiungere un disco dati](attach-disk.md) , in modo che i servizi e i carichi di lavoro possano usarlo per archiviare i dati.
