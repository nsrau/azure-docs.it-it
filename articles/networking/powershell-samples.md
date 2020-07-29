---
title: Esempi di Azure PowerShell-rete
description: Informazioni sugli esempi di Azure PowerShell per la rete, incluso un esempio per la creazione di una rete virtuale per applicazioni multilivello.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: aa3304cbabb183368f0e47415e64120854f43535
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282076"
---
# <a name="azure-powershell-samples-for-networking"></a>Esempi di Azure PowerShell per la rete

La tabella seguente include i collegamenti agli script creati usando Azure PowerShell.

| Script | Descrizione |
|-|-|
|**Connettività tra le risorse di Azure**||
| [Creare una rete virtuale per le applicazioni multilivello](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end. Il traffico verso la subnet front-end è limitato a HTTP, mentre il traffico verso la subnet back-end è limitato a SQL sulla porta 1433. |
| [Eseguire il peering di due reti virtuali](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea e connette due reti virtuali nella stessa area. |
| [Instradare il traffico attraverso un'appliance virtuale di rete](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end e una VM che è in grado di instradare il traffico tra le due subnet. |
| [Filtrare il traffico della VM in ingresso e in uscita](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end. Il traffico di rete in ingresso alla subnet front-end è limitato a HTTP e HTTPS. Non è consentito il traffico in uscita verso Internet dalla subnet di back-end. |
|**Bilanciamento del carico e direzione del traffico**||
| [Eseguire il bilanciamento del carico per il traffico verso le macchine virtuali per la disponibilità elevata](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Consente di creare più macchine virtuali in una configurazione a disponibilità elevata e con bilanciamento del carico. |
| [Dirigere il traffico su più aree per la disponibilità elevata delle applicazioni](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Crea due piani di servizio app, due app Web, un profilo di gestione traffico e due endpoint di gestione traffico. |
| | |
