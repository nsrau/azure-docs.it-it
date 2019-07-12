---
title: Panoramica VM di serie HB - macchine virtuali di Azure | Microsoft Docs
description: Informazioni sul supporto dell'anteprima per le dimensioni di macchine virtuali serie HB in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707766"
---
# <a name="hb-series-virtual-machines-overview"></a>Panoramica delle macchine virtuali serie HB

Ottimizzare le prestazioni dell'applicazione di compute (HPC) ad alte prestazioni su AMD EPYC richiede una località e processo di selezione host per memoria approccio ponderato. Di seguito viene descritta l'architettura di AMD EPYC e l'implementazione di esso in Azure per le applicazioni HPC. Si userà il termine "pNUMA" per fare riferimento a un dominio NUMA fisica e "vNUMA" per fare riferimento a un dominio NUMA virtuale.

Fisicamente, una serie HB è 2 * 32 core EPYC 7551 CPU per un totale di 64 core fisici. Questi 64 core sono suddiviso in 16 domini pNUMA (8 per ogni socket), ognuno dei quali è quattro core e noti come "CPU" complessi"(o"CCX"). Ogni CCX ha la propria cache L3, ovvero come un sistema operativo verrà visualizzato un limite pNUMA/vNUMA. Una coppia di CCXs adiacenti condivide l'accesso a due canali di DRAM fisico (32 GB di DRAM nei server HB serie).

Per lasciare spazio per l'hypervisor operare senza interferire con la macchina virtuale di Azure, Microsoft si riserva pNUMA fisico Domino 0 (il primo CCX). Assegniamo quindi pNUMA domini 1-15 (l'unità CCX rimanenti) per la macchina virtuale. Verrà visualizzata la macchina virtuale:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` core per ogni macchina virtuale

La macchina virtuale stessa, non sa che pNUMA 0 non è stato assegnato a esso. La macchina virtuale riconosce pNUMA. 1 a 15 come vNUMA 0 a 14, con vNUMA 7 su vNUMA vSocket 0 e 8 su vSocket 1. Mentre questo è asimmetrico, il sistema operativo deve eseguire l'avvio e funzionare normalmente. Più avanti in questa Guida, indichiamo il modo migliore per eseguire applicazioni MPI in questo layout NUMA asimmetrico.

L'aggiunta di processo funzionerà in macchine virtuali della serie HB perché è necessario esporre la silicon sottostante come-consiste nella macchina virtuale guest. Si consiglia di associazione del processo per coerenza e prestazioni ottimali.

Vedi altro sul [architettura AMD EPYC](https://bit.ly/2Epv3kC) e [multi-chip architetture](https://bit.ly/2GpQIMb) su LinkedIn. Per informazioni più dettagliate, vedere la [HPC Tuning Guide per i processori AMD EPYC](https://bit.ly/2T3AWZ9).

Il diagramma seguente mostra la separazione di core riservati per l'Hypervisor di Azure e la macchina virtuale serie HB.

![Separazione dei core riservati per l'Hypervisor di Azure e macchine virtuali serie HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifiche hardware

| Specifiche hardware                | Macchine Virtuali serie HB                     |
|----------------------------------|----------------------------------|
| Core                            | 60 (LAM disabilitato)                |
| CPU                              | AMD EPYC 7551*                   |
| Frequenza della CPU (non-AVX)          | ~2.55 GHz (singolo + tutti i core)   |
| Memoria                           | 4 GB/core (240 totale)            |
| Disco locale                       | NVMe 700 GB                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| Rete                          | 50 Gb Ethernet (40 Gb utilizzabile) Azure seconda generazione SmartNIC * * * |

## <a name="software-specifications"></a>Specifiche del software

| Specifiche di SW           |Macchine Virtuali serie HB           |
|-----------------------------|-----------------------|
| Dimensioni di processi MPI max            | 12000 core 6000 core (100 set di scalabilità di macchine virtuali) (200 set di scalabilità di macchine virtuali)  |
| Supporto di MPI                 | MVAPICH2, OpenMPI, MPICH, piattaforma MPI, Intel MPI  |
| Framework aggiuntivi       | Unified Communication X, libfabric, PGAS |
| Supporto di archiviazione di Azure       | Standard e Premium (massimi 4 dischi) |
| Supporto per RDMA SRIOV del sistema operativo   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Supporto per Azure CycleCloud    | Yes                         |
| Supporto di Azure Batch         | Yes                         |

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle dimensioni delle VM di HPC per [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Altre informazioni sulle [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
