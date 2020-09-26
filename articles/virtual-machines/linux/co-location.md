---
title: Condivisione percorso di macchine virtuali Linux
description: Informazioni sul modo in cui la condivisione percorso risorse di macchine virtuali di Azure per Linux può migliorare la latenza.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 6bf01f4da6ce96b413c87b81c14a998603cebd6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279724"
---
# <a name="co-locate-resources-for-improved-latency"></a>Condividere il percorso tra le risorse per una latenza ottimale

Quando si distribuisce l'applicazione in Azure, la distribuzione di istanze tra aree o zone di disponibilità crea una latenza di rete che può influisca sulle prestazioni complessive dell'applicazione. 

## <a name="proximity-placement-groups"></a>Gruppi di selezione host di prossimità

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passaggi successivi

Distribuire una macchina virtuale in un [gruppo di posizionamento di prossimità](proximity-placement-groups.md) usando l'interfaccia della riga di comando di Azure.

Informazioni su come [testare la latenza di rete](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Informazioni su come [ottimizzare la velocità effettiva della rete](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Informazioni su come [usare i gruppi di posizionamento di prossimità con le applicazioni SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
