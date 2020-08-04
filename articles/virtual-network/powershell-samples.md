---
title: Esempi di Azure PowerShell per una rete virtuale
description: Informazioni sugli esempi di Azure PowerShell per la gestione delle reti virtuali, incluso un esempio per la creazione di una rete virtuale per applicazioni multilivello.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: b3107d521fb79e3ea6cfe190abd2ec3fb7859cb3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288234"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Esempi di Azure PowerShell per una rete virtuale

La tabella seguente include collegamenti a script di Azure PowerShell:

| Script | Descrizione |
|----|----|
| [Creare una rete virtuale per le applicazioni multilivello](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Crea una rete virtuale con subnet front-end e back-end. Il traffico verso la subnet front-end è limitato a HTTP, mentre il traffico verso la subnet back-end è limitato a SQL sulla porta 1433. |
| [Eseguire il peering di due reti virtuali](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Crea e connette due reti virtuali nella stessa area. |
| [Instradare il traffico attraverso un'appliance virtuale di rete](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Crea una rete virtuale con subnet front-end e back-end e una VM che è in grado di instradare il traffico tra le due subnet. |
| [Filtrare il traffico della VM in ingresso e in uscita](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Crea una rete virtuale con subnet front-end e back-end. Il traffico di rete in ingresso nella subnet front-end è limitato a HTTP e HTTPS. Non è consentito il traffico in uscita verso Internet dalla subnet back-end. |
|[Configurare una rete virtuale dual stack IPv4+IPv6 con un servizio Load Balancer Basic](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Distribuisce una rete virtuale dual stack (IPv4+IPv6) con due macchine virtuali e un Azure Load Balancer Basic con indirizzi IP pubblici IPv4 e IPv6. |
|[Configurare una rete virtuale dual stack IPv4+IPv6 con un servizio Load Balancer Standard](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Distribuisce una rete virtuale dual stack (IPv4+IPv6) con due macchine virtuali e un servizio Azure Load Balancer Standard con indirizzi IP pubblici IPv4 e IPv6. |
