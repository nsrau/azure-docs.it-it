---
title: Load Balancer Standard di Azure e set di scalabilità di macchine virtuali
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: Con questo percorso di apprendimento, iniziare a usare Azure Load Balancer Standard e i set di scalabilità di macchine virtuali.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: f5a8453f84854108facb4da4616a7d362e0fb38c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531722"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer con i set di scalabilità di macchine virtuali di Azure

Quando si usano i set di scalabilità di macchine virtuali e il servizio di bilanciamento del carico, è necessario considerare le linee guida seguenti:

## <a name="multiple-virtual-machine-scale-sets-cant-use-the-same-load-balancer"></a>Più set di scalabilità di macchine virtuali non possono usare lo stesso servizio di bilanciamento del carico
## <a name="port-forwarding-and-inbound-nat-rules"></a>Porting e regole NAT in ingresso:
  * Dopo la creazione del set di scalabilità, la porta back-end non può essere modificata per una regola di bilanciamento del carico usata da un probe di integrità del servizio di bilanciamento del carico. Per modificare la porta, è possibile rimuovere il probe di integrità aggiornando il set di scalabilità di macchine virtuali di Azure, aggiornare la porta e quindi configurare di nuovo il probe di integrità.
  * Quando si usa il set di scalabilità di macchine virtuali nel pool back-end del servizio di bilanciamento del carico, le regole NAT in ingresso predefinite vengono create automaticamente.
## <a name="inbound-nat-pool"></a>Pool NAT in ingresso:
  * Ogni set di scalabilità di macchine virtuali deve avere almeno un pool NAT in ingresso. 
  * Il pool NAT in ingresso è una raccolta di regole NAT in ingresso. Un pool NAT in ingresso non può supportare più set di scalabilità di macchine virtuali.
  
## <a name="load-balancing-rules"></a>Regole di bilanciamento del carico:
  * Quando si usa il set di scalabilità di macchine virtuali nel pool back-end del servizio di bilanciamento del carico, la regola di bilanciamento del carico predefinita viene creata automaticamente.
## <a name="outbound-rules"></a>Regole in uscita:
  *  Per creare una regola in uscita per un pool back-end a cui è già stato fatto riferimento da una regola di bilanciamento del carico, è prima necessario contrassegnare **"Crea regole in uscita implicite"** come **No** nel portale quando viene creata la regola di bilanciamento del carico in ingresso.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Creazione regola di bilanciamento del carico" border="true":::

I metodi seguenti possono essere usati per distribuire un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente.

* [Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando il portale di Azure](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal).
* [Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando Azure PowerShell](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell).
* [Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli).
