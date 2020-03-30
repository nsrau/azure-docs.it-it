---
title: Co-localizzazione delle macchine virtuali di Windows AzureCo-locate Windows Azure VMs
description: Informazioni su come la co-localizzazione delle risorse delle macchine virtuali di Azure può migliorare la latenza.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266793"
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