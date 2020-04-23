---
title: Colocalizzare le macchine virtuali per una migliore latenza
description: Informazioni su come la co-localizzazione delle risorse delle macchine virtuali di Azure può migliorare la latenza.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083173"
---
# <a name="co-locate-resource-for-improved-latency"></a>Co-localizzare le risorse per una migliore latenza

Quando si distribuisce l'applicazione in Azure, la distribuzione delle istanze tra aree o zone di disponibilità crea latenza di rete, che può influire sulle prestazioni complessive dell'applicazione. 


## <a name="proximity-placement-groups"></a>Gruppi di selezione host di prossimità 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passaggi successivi

Distribuire una macchina virtuale in un gruppo di posizionamento di prossimità usando Azure PowerShell.Deploy a VM to a [proximity placement group](proximity-placement-groups.md) using Azure PowerShell.

Informazioni su come [testare la latenza di rete](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Informazioni su come [ottimizzare la velocità effettiva](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)della rete.  

Informazioni su come utilizzare i gruppi di posizionamento di prossimità con le [applicazioni SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)