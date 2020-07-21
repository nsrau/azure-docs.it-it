---
title: Condivisione percorso di macchine virtuali Linux
description: Informazioni su come condividere il percorso delle risorse delle macchine virtuali di Azure per migliorare la latenza.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 0a28b238701eeb0572149fb55e1633cdd40ec0a4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511025"
---
# <a name="co-locate-resources-for-improved-latency"></a>Condivisione percorso risorse per una latenza migliorata

Quando si distribuisce l'applicazione in Azure, la distribuzione di istanze tra aree o zone di disponibilità crea una latenza di rete che può influisca sulle prestazioni complessive dell'applicazione. 

## <a name="proximity-placement-groups"></a>Gruppi di selezione host di prossimità

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passaggi successivi

Distribuire una macchina virtuale in un [gruppo di posizionamento di prossimità](proximity-placement-groups.md) usando l'interfaccia della riga di comando di Azure.

Informazioni su come [testare la latenza di rete](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Informazioni su come [ottimizzare la velocità effettiva della rete](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Informazioni su come [usare i gruppi di posizionamento di prossimità con le applicazioni SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
