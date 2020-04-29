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
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906751"
---
# <a name="hc-series-virtual-machine-overview"></a>Panoramica delle macchine virtuali della serie HC

Per ottimizzare le prestazioni delle applicazioni HPC nei processori Intel Xeon scalabili è necessario un approccio riflessivo per elaborare la selezione host in questa nuova architettura. In questo esempio viene illustrata l'implementazione delle macchine virtuali della serie HC di Azure per le applicazioni HPC. Il termine "pNUMA" viene usato per fare riferimento a un dominio NUMA fisico e "vNUMA" per fare riferimento a un dominio NUMA virtualizzato. Analogamente, si userà il termine "pCore" per fare riferimento ai core CPU fisici e a "vCore" per fare riferimento ai core CPU virtualizzati.

Fisicamente, un server HC è costituito da 2 * 24 core Intel Xeon Platinum 8168 per un totale di 48 core fisici. Ogni CPU è un singolo dominio pNUMA e ha accesso unificato a sei canali di DRAM. Le CPU Intel Xeon Platinum presentano una cache L2 di dimensioni superiori a 4 volte superiore rispetto alle generazioni precedenti (256 KB/core-> 1 MB/Core), riducendo al tempo stesso la cache L3 rispetto alle CPU Intel precedenti (2,5 MB/Core-> 1,375 MB/Core).

La topologia precedente viene riportata anche alla configurazione dell'hypervisor della serie HC. Per consentire il funzionamento dell'hypervisor di Azure senza interferire con la macchina virtuale, si riservano pCores 0-1 e 24-25 (ovvero i primi 2 pCores in ogni socket). Si assegnano quindi i domini di pNUMA tutti i core rimanenti alla macchina virtuale. Quindi, la macchina virtuale visualizzerà:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`Core per macchina virtuale

La macchina virtuale non è a conoscenza del pCores 0-1 e 24-25. In questo modo, espone ogni vNUMA come se avesse 22 core a livello nativo.

Con le CPU Intel Xeon Platinum, Gold e Silver è stata introdotta anche una rete mesh 2D su disco per la comunicazione all'interno e all'esterno del socket della CPU. Si consiglia vivamente di bloccare i processi per ottenere prestazioni e coerenza ottimali. Il blocco dei processi funzionerà nelle macchine virtuali della serie HC perché il silicio sottostante viene esposto così com'è alla macchina virtuale guest. Per altre informazioni, vedere [architettura Intel Xeon SP](https://bit.ly/2RCYkiE).

Il diagramma seguente illustra la separazione dei core riservati per l'hypervisor di Azure e la macchina virtuale della serie HC.

![Separazione dei core riservati per la VM di Azure hypervisor e della serie HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifiche hardware

| Specifiche hardware          | VM serie HC                     |
|----------------------------------|----------------------------------|
| Core                            | 44 (HT disabilitato)                 |
| CPU                              | Intel Xeon Platinum 8168 *        |
| Frequenza CPU (non-AVX)          | 3,7 GHz (core singolo), 2.7-3,4 GHz (tutti i core) |
| Memoria                           | 8 GB/Core (Totale 352)            |
| Disco locale                       | 700 GB NVMe                      |
| Infiniband                       | 100 GB EDR Mellanox ConnectX-5 * * |
| Rete                          | 50 GB Ethernet (40 GB utilizzabili) Azure Second gen SmartNIC * * * |

## <a name="software-specifications"></a>Specifiche del software

| Specifiche del software     | VM serie HC          |
|-----------------------------|-----------------------|
| Dimensioni massime del processo MPI            | 13200 Core (300 VM in un singolo VMSS con singlePlacementGroup = true) |
| Supporto MPI                 | MVAPICH2, OpenMPi, MPICH, Platform MPI, Intel MPI  |
| Framework aggiuntivi       | Comunicazione unificata X, libfabric, PGA |
| Supporto per archiviazione di Azure       | STD + Premium (max 4 dischi) |
| Supporto del sistema operativo per SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 + |
| Supporto di Azure CycleCloud    | Sì                         |
| Supporto Azure Batch         | Sì                         |

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulle dimensioni delle VM HPC per [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Scopri di più su [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
