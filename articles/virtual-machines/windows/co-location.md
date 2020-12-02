---
title: Condividere le VM con una latenza migliorata
description: Informazioni su come condividere il percorso delle risorse delle macchine virtuali di Azure per migliorare la latenza.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: eb7b5d3b477a511432dbd3ee8a6b382002aaab44
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488468"
---
# <a name="co-locate-resource-for-improved-latency"></a>Condivisione percorso risorse per una latenza migliorata

Quando si distribuisce l'applicazione in Azure, la distribuzione di istanze tra aree o zone di disponibilità crea una latenza di rete che può influisca sulle prestazioni complessive dell'applicazione. 


## <a name="proximity-placement-groups"></a>Gruppi di selezione host di prossimità 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passaggi successivi

Distribuire una macchina virtuale in un [gruppo di posizionamento di prossimità](proximity-placement-groups.md) usando Azure PowerShell.

Informazioni su come [testare la latenza di rete](../../virtual-network/virtual-network-test-latency.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Informazioni su come [ottimizzare la velocità effettiva della rete](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Informazioni su come [usare i gruppi di posizionamento di prossimità con le applicazioni SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).