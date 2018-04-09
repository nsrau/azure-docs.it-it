---
title: Esempi di Azure PowerShell per una rete virtuale | Microsoft Docs
description: Esempi di Azure PowerShell per una rete virtuale.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: c9cb8da2a6a7512daa6721af90d5b21c6ba5e8e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Esempi di Azure PowerShell per una rete virtuale

La tabella seguente include collegamenti a script di Azure PowerShell:

| | |
|----|----|
| [Creare una rete virtuale per le applicazioni multilivello](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Crea una rete virtuale con subnet front-end e back-end. Il traffico verso la subnet front-end è limitato a HTTP, mentre il traffico verso la subnet back-end è limitato a SQL sulla porta 1433. |
| [Eseguire il peering di due reti virtuali](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Crea e connette due reti virtuali nella stessa area. |
| [Instradare il traffico attraverso un'appliance virtuale di rete](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Crea una rete virtuale con subnet front-end e back-end e una VM che è in grado di instradare il traffico tra le due subnet. |
| [Filtrare il traffico della VM in ingresso e in uscita](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Crea una rete virtuale con subnet front-end e back-end. Il traffico di rete in ingresso nella subnet front-end è limitato a HTTP e HTTPS. Non è consentito il traffico in uscita verso Internet dalla subnet back-end. |
