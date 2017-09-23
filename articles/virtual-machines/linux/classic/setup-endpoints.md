---
title: Configurare gli endpoint in una VM Linux classica | Microsoft Docs
description: Informazioni su come configurare gli endpoint per una macchina virtuale Linux nel portale di Azure classico per consentire la comunicazione con una VM Linux in Azure
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 4fd8b847b0f60648d1661ce5a8667c641e616ed4
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017

---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Come configurare gli endpoint in una macchina virtuale Linux classica in Azure
Tutte le macchine virtuali Linux create in Azure con il modello di distribuzione classico possono comunicare automaticamente mediante un canale di rete privato con altre macchine virtuali dello stesso servizio cloud o nella stessa rete virtuale. Tuttavia, i computer in Internet o altre reti virtuali richiedono gli endpoint per indirizzare il traffico di rete in ingresso a una macchina virtuale. Questo articolo è disponibile anche per le [macchine virtuali Windows](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

Nel modello di distribuzione **Resource Manager** gli endpoint vengono configurati tramite **Gruppi di sicurezza di rete**. Per altre informazioni, vedere [Apertura di porte e di endpoint](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Quando si crea una macchina virtuale Linux nel portale di Azure, viene in genere creato automaticamente un endpoint per Secure Shell (SSH). È possibile configurare altri endpoint durante la creazione della macchina virtuale o successivamente all'occorrenza.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Passaggi successivi
* È anche possibile creare un endpoint di VM con l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Eseguire il comando **azure vm endpoint create** .
* Se una macchina virtuale è stata creata nel modello di distribuzione Resource Manager, è possibile usare l'interfaccia della riga di comando di Azure in modalità Resource Manager per [creare gruppi di sicurezza di rete](../../../virtual-network/virtual-networks-create-nsg-arm-cli.md) per controllare il traffico verso la macchina virtuale.

