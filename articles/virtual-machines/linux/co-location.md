---
title: Percorso condiviso di macchine virtuali Linux di Azure
description: Informazioni su come condividere il percorso delle risorse delle macchine virtuali di Azure per migliorare la latenza.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: c90e70900c019340cacc169325155ac74cf7807c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171477"
---
# <a name="co-locate-resources-for-improved-latency"></a>Condivisione percorso risorse per una latenza migliorata

Quando si distribuisce l'applicazione in Azure, la distribuzione di istanze tra aree o zone di disponibilità crea una latenza di rete che può influisca sulle prestazioni complessive dell'applicazione. 

## <a name="proximity-placement-groups"></a>Gruppi di posizionamento di prossimità

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passaggi successivi

Distribuire una macchina virtuale in un [gruppo di posizionamento di prossimità](proximity-placement-groups.md) usando l'interfaccia della riga di comando di Azure.

Informazioni su come [testare la latenza di rete](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Informazioni su come [ottimizzare la velocità effettiva della rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Informazioni su come [usare i gruppi di posizionamento di prossimità con le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
