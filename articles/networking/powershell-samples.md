---
title: Esempi di Azure PowerShell | Microsoft Docs
description: Esempi di Azure PowerShell
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: georgewallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 0bca4fb6874bd265f0ae9faeb4219abeb4ffb6d4
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---
# <a name="azure-powershell-samples-for-networking"></a>Esempi di Azure PowerShell per la rete

La tabella seguente include i collegamenti agli script creati usando Azure PowerShell.

| | |
|-|-|
|**Connettività tra risorse di Azure**||
| [Creare una rete virtuale per le applicazioni multilivello](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end. Il traffico verso la subnet front-end è limitato a HTTP, mentre il traffico verso la subnet back-end è limitato a SQL sulla porta 1433. |
| [Eseguire il peering di due reti virtuali](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea e connette due reti virtuali nella stessa area. |
| [Instradare il traffico attraverso un'appliance virtuale di rete](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end e una VM che è in grado di instradare il traffico tra le due subnet. |
| [Filtrare il traffico della VM in ingresso e in uscita](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end. Il traffico di rete in ingresso alla subnet front-end è limitato a HTTP e HTTPS. Non è consentito il traffico in uscita verso Internet dalla subnet di back-end. |
|**Bilanciamento del carico e direzione del traffico**||
| [Eseguire il bilanciamento del carico per il traffico verso le VM per la disponibilità elevata](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Consente di creare più macchine virtuali in una configurazione a disponibilità elevata e con bilanciamento del carico. |
| [Eseguire il bilanciamento del carico per più siti Web sulle VM](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea due VM con più configurazioni IP, unite a un set di disponibilità di Azure, accessibili tramite un servizio di bilanciamento del carico di Azure. |
| [Dirigere il traffico su più aree per la disponibilità elevata delle applicazioni](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Crea due piani di servizio app, due app Web, un profilo di gestione traffico e due endpoint di gestione traffico. |
| | |

