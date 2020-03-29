---
title: Panoramica delle macchine virtuali della serie HB - Macchine virtuali di Azure Documenti Microsoft
description: Informazioni sul supporto dell'anteprima per le dimensioni della macchina virtuale della serie HB in Azure.Learn about the preview support for the HB-series VM size in Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707766"
---
# <a name="hb-series-virtual-machines-overview"></a>Panoramica delle macchine virtuali della serie HB

La massimizzazione delle prestazioni delle applicazioni hPC (High Performance Computing) su AMD EPYC richiede una localizzazione della memoria di approccio ponderato e il posizionamento dei processi. Di seguito viene descritta l'architettura AMD EPYC e la sua implementazione in applicazioni Azure per HPC. Utilizzeremo il termine "pNUMA" per fare riferimento a un dominio NUMA fisico e "vNUMA" per fare riferimento a un dominio NUMA virtualizzato.

Fisicamente, una serie HB è di 2 CPU EPYC 7551 a 32 core per un totale di 64 core fisici. Questi 64 core sono divisi in 16 domini pNUMA (8 per socket), ognuno dei quali è costituito da quattro core e noto come "complesso CPU" (o "CCX"). Ogni CCX ha la propria cache L3, ovvero come un sistema operativo vedrà un limite pNUMA/vNUMA. Una coppia di CCX adiacenti condivide l'accesso a due canali di DRAM fisica (32 GB di DRAM nei server HB-serie).

Per fornire spazio per l'hypervisor di Azure per operare senza interferire con la macchina virtuale, si riserva il dominio pNUMA fisico 0 (il primo CCX). Assegniamo quindi i domini pNUMA da 1 a 15 (le unità CCX rimanenti) per la macchina virtuale. La macchina virtuale vedrà:The VM will see:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`core per VM

La macchina virtuale, di per sé, non sa che pNUMA 0 non è stato dato ad esso. La macchina virtuale riconosce pNUMA 1-15 come vNUMA 0-14, con 7 vNUMA su vSocket 0 e 8 vNUMA su vSocket 1. Mentre questo è asimmetrico, il sistema operativo dovrebbe avviarsi e funzionare normalmente. Più avanti in questa guida, istruiamo il modo migliore per eseguire applicazioni MPI su questo layout NUMA asimmetrico.

Il blocco del processo funzionerà nelle macchine virtuali della serie HB perché esponiamo il silicio sottostante così com'è alla macchina virtuale guest. Si consiglia vivamente il blocco del processo per prestazioni e coerenza ottimali.

Scopri di più [sull'architettura AMD EPYC](https://bit.ly/2Epv3kC) e sulle [architetture multi-chip](https://bit.ly/2GpQIMb) su LinkedIn. Per informazioni più dettagliate, vedere la [Guida all'ottimizzazione HPC per processori AMD EPYC](https://bit.ly/2T3AWZ9).

Il diagramma seguente mostra la separazione dei core riservati per Azure Hypervisor e la macchina virtuale della serie HB.

![Segregazione dei core riservati per Azure Hypervisor e HB-series VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifiche hardware

| Specifiche HW                | VM serie HB                     |
|----------------------------------|----------------------------------|
| Core                            | 60 (SMT disabilitato)                |
| CPU                              | AMD EPYC 7551                   |
| Frequenza CPU (non AVX)          | 2,55 GHz (singolo e tutti i core)   |
| Memoria                           | 4 GB/core (240 in totale)            |
| Disco locale                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 |
| Rete                          | 50 Gb Ethernet (40 Gb utilizzabili) Azure seconda Generazione SmartNIC |

## <a name="software-specifications"></a>Specifiche del software

| Specifiche SW           |VM serie HB           |
|-----------------------------|-----------------------|
| Dimensione massima processo MPI            | 6000 core (100 set di scalabilità di macchine virtuali) 12000 core (200 set di scalabilità di macchine virtuali)  |
| Supporto MPI                 | MVAPICH2, OpenMPI, MPICH, Piattaforma MPI, Intel MPI  |
| Framework aggiuntivi       | Comunicazione unificata X, libfabric, PGAS |
| Supporto di Archiviazione di AzureAzure Storage Support       | Std - Premium (massimo 4 dischi) |
| Supporto del sistema operativo per SRIOV RDMA   | CentOS/RHEL 7.6, SLES 12 SP4, WinServer 2016  |
| Azure CycleCloud Support    | Sì                         |
| Azure Batch Support         | Sì                         |

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle dimensioni delle macchine virtuali HPC per Linux e Windows in Azure.Learn more about HPC VM sizes for [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) and [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Altre informazioni su HPC in Azure.Learn more about [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
