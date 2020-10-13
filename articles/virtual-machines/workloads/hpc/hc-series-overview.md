---
title: Panoramica delle VM serie HC-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sul supporto in anteprima per le dimensioni della macchina virtuale della serie HC in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: de6051e8880bbe3df42031a0d0d7b60abc27d2b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88689800"
---
# <a name="hc-series-virtual-machine-overview"></a>Panoramica delle macchine virtuali della serie HC

Per ottimizzare le prestazioni delle applicazioni HPC nei processori Intel Xeon scalabili è necessario un approccio riflessivo per elaborare la selezione host in questa nuova architettura. In questo esempio viene illustrata l'implementazione delle macchine virtuali della serie HC di Azure per le applicazioni HPC. Il termine "pNUMA" viene usato per fare riferimento a un dominio NUMA fisico e "vNUMA" per fare riferimento a un dominio NUMA virtualizzato. Analogamente, si userà il termine "pCore" per fare riferimento ai core CPU fisici e a "vCore" per fare riferimento ai core CPU virtualizzati.

Fisicamente, un server della [serie HC](../../hc-series.md) è costituito da 2 * CPU Intel Xeon Platinum 8168 a 24 core per un totale di 48 core fisici. Ogni CPU è un singolo dominio pNUMA e ha accesso unificato a sei canali di DRAM. Le CPU Intel Xeon Platinum presentano una cache L2 di dimensioni superiori a 4 volte superiore rispetto alle generazioni precedenti (256 KB/core-> 1 MB/Core), riducendo al tempo stesso la cache L3 rispetto alle CPU Intel precedenti (2,5 MB/Core-> 1,375 MB/Core).

La topologia precedente viene riportata anche alla configurazione dell'hypervisor della serie HC. Per consentire il funzionamento dell'hypervisor di Azure senza interferire con la macchina virtuale, si riservano pCores 0-1 e 24-25 (ovvero i primi 2 pCores in ogni socket). Si assegnano quindi i domini di pNUMA tutti i core rimanenti alla macchina virtuale. Quindi, la macchina virtuale visualizzerà:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` Core per macchina virtuale

La macchina virtuale non è a conoscenza del pCores 0-1 e 24-25. In questo modo, espone ogni vNUMA come se avesse 22 core a livello nativo.

Con le CPU Intel Xeon Platinum, Gold e Silver è stata introdotta anche una rete mesh 2D su disco per la comunicazione all'interno e all'esterno del socket della CPU. Si consiglia vivamente di bloccare i processi per ottenere prestazioni e coerenza ottimali. Il blocco dei processi funzionerà nelle macchine virtuali della serie HC perché il silicio sottostante viene esposto così com'è alla macchina virtuale guest.

Il diagramma seguente illustra la separazione dei core riservati per l'hypervisor di Azure e la macchina virtuale della serie HC.

![Separazione dei core riservati per la VM di Azure hypervisor e della serie HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifiche hardware

| Specifiche hardware          | VM serie HC                     |
|----------------------------------|----------------------------------|
| Core                            | 44 (HT disabilitato)                 |
| CPU                              | Intel Xeon Platinum 8168         |
| Frequenza CPU (non-AVX)          | 3,7 GHz (core singolo), 2.7-3,4 GHz (tutti i core) |
| Memoria                           | 8 GB/Core (Totale 352)            |
| Disco locale                       | SSD 700 GB                       |
| Infiniband                       | 100 GB EDR Mellanox ConnectX-5   |
| Rete                          | 50 GB Ethernet (40 GB utilizzabili) Azure Second gen SmartNIC    |

## <a name="software-specifications"></a>Specifiche del software

| Specifiche del software     |VM serie HC           |
|-----------------------------|-----------------------|
| Dimensioni massime del processo MPI            | 13200 Core (300 VM in un singolo set di scalabilità di macchine virtuali con singlePlacementGroup = true)  |
| Supporto MPI                 | HPC-X, Intel MPI, OpenMPi, MVAPICH2, MPICH, Platform MPI  |
| Framework aggiuntivi       | Comunicazione unificata X, libfabric, PGA |
| Supporto per archiviazione di Azure       | Dischi standard e Premium (massimo 4 dischi) |
| Supporto del sistema operativo per SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Supporto per l'agente di orchestrazione        | CycleCloud, batch  |

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sull' [architettura Intel Xeon SP](https://bit.ly/2RCYkiE).
- Leggere gli ultimi annunci e alcuni esempi HPC e risultati nei [blog della community tecnica di Calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
