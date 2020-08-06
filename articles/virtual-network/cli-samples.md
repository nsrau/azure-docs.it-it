---
title: Esempi dell'interfaccia della riga di comando di Azure per una rete virtuale
description: Informazioni sui vari script di esempio che è possibile usare per completare le attività nell'interfaccia della riga di comando di Azure, inclusa la creazione di una rete virtuale per applicazioni multilivello.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 539d11205ffead52d7f40526f2c712e8cf8b5cdd
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501441"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Esempi dell'interfaccia della riga di comando di Azure per una rete virtuale

La tabella seguente include collegamenti a script Bash con i comandi dell'interfaccia della riga di comando di Azure:

| Script | Descrizione |
|----|----|
| [Creare una rete virtuale per le applicazioni multilivello](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Crea una rete virtuale con subnet front-end e back-end. Il traffico verso la subnet front-end è limitato a HTTP e SSH, mentre il traffico verso la subnet back-end è limitato a MySQL sulla porta 3306. |
| [Eseguire il peering di due reti virtuali](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Crea e connette due reti virtuali nella stessa area. |
| [Instradare il traffico attraverso un'appliance virtuale di rete](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Crea una rete virtuale con subnet front-end e back-end e una VM che è in grado di instradare il traffico tra le due subnet. |
| [Filtrare il traffico della VM in ingresso e in uscita](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Crea una rete virtuale con subnet front-end e back-end. Il traffico di rete in ingresso alla subnet front-end è limitato a HTTP, HTTPS e SSH. Non è consentito il traffico in uscita verso Internet dalla subnet back-end. |
|[Configurare una rete virtuale dual stack IPv4+IPv6 con un servizio Load Balancer Basic](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Distribuisce una rete virtuale dual stack (IPv4+IPv6) con due macchine virtuali e un Azure Load Balancer Basic con indirizzi IP pubblici IPv4 e IPv6. |
|[Configurare una rete virtuale dual stack IPv4+IPv6 con un servizio Load Balancer Standard](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Distribuisce una rete virtuale dual stack (IPv4+IPv6) con due macchine virtuali e un servizio Azure Load Balancer Standard con indirizzi IP pubblici IPv4 e IPv6. |
|[Esercitazione: Creare e testare un gateway NAT - Interfaccia della riga di comando di Azure](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Creare e convalidare un gateway NAT usando una macchina virtuale di origine e di destinazione. |
