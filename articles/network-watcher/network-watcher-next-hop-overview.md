---
title: Introduzione all'hop successivo in Azure Network Watcher | Microsoft Docs
description: Questo articolo fornisce una panoramica della funzionalità hop successivo di Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bbb782e700781dcfedbbd340c7d10db53767b035
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180386"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Usare l'hop successivo per diagnosticare problemi di routing di una macchina virtuale

Il traffico proveniente da una macchina virtuale (VM) viene inviato a una destinazione in base alle route valide associate a una scheda di interfaccia di rete (NIC). L'hop successivo ottiene il tipo di hop successivo e l'indirizzo IP di un pacchetto da una VM e una scheda di interfaccia di rete specifiche. Conoscere l'hop successivo consente di determinare se il traffico è indirizzato verso la destinazione desiderata, o se non viene inviato affatto. Una configurazione errata delle route in cui il traffico viene indirizzato a un percorso locale oppure a un'appliance virtuale, può causare problemi di connettività. L'hop successivo restituisce anche la tabella di route associata all'hop successivo. Se la route è definita dall'utente, viene restituita quella route. In caso contrario l'hop successivo restituisce la **route di sistema**.

![Panoramica dell'hop successivo](./media/network-watcher-next-hop-overview/figure1.png)

Gli hop successivi che potrebbero essere restituiti dalla funzionalità hop successivo sono i seguenti:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* VnetPeering
* Nessuna

Per altre informazioni sui tipi di hop successivo, vedere [panoramica del routing](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare l'hop successivo per diagnosticare problemi di routing di rete VM, vedere l'argomento relativo a [come diagnosticare un problema di routing di rete di una macchina virtuale](diagnose-vm-network-routing-problem.md).
