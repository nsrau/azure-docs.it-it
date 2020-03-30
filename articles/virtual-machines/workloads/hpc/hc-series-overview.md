---
title: Panoramica delle macchine della serie HC - Macchine virtuali di Azure Documenti Microsoft
description: Informazioni sul supporto dell'anteprima per le dimensioni della macchina virtuale della serie HC in Azure.Learn about the preview support for the HC-series VM size in Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906751"
---
# <a name="hc-series-virtual-machine-overview"></a>Panoramica della macchina virtuale della serie HC

La massimizzazione delle prestazioni delle applicazioni HPC sui processori Intel Xeon Scalable richiede un approccio ponderato per elaborare il posizionamento su questa nuova architettura. In questa sezione viene descritta l'implementazione nelle macchine virtuali serie HC di Azure per le applicazioni HPC. Utilizzeremo il termine "pNUMA" per fare riferimento a un dominio NUMA fisico e "vNUMA" per fare riferimento a un dominio NUMA virtualizzato. Analogamente, useremo il termine "pCore" per fare riferimento ai core della CPU fisica e "vCore" per fare riferimento ai core della CPU virtualizzati.

Fisicamente, un server HC è composto da 2 CPU Intel Xeon Platinum 8168 per un totale di 48 core fisici. Ogni CPU è un singolo dominio pNUMA e ha accesso unificato a sei canali di DRAM. Le CPU Intel Xeon Platinum presentano una cache L2 4 volte più grande rispetto alle generazioni precedenti (256 KB/core -> 1 MB/core), riducendo al contempo la cache L3 rispetto alle CPU Intel precedenti (2,5 MB/core -> 1,375 MB/core).

La topologia di cui sopra viene riportata anche nella configurazione dell'hypervisor della serie HC. Per fornire spazio per l'hypervisor di Azure per operare senza interferire con la macchina virtuale, si riserva pCores 0-1 e 24-25 (vale a dire, i primi 2 pCore su ogni socket). Assegniamo quindi ai domini pNUMA tutti i core rimanenti alla macchina virtuale. Così, la macchina virtuale vedrà:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`core per VM

La macchina virtuale non è a conoscenza del fatto che i pCore 0-1 e 24-25 non sono stati assegnati. Pertanto, espone ogni vNUMA come se avesse originariamente 22 core.

Le CPU Intel Xeon Platinum, Gold e Silver introducono anche una rete mesh 2D in discarico per la comunicazione all'interno ed esterna alla presa della CPU. Si consiglia vivamente il blocco del processo per prestazioni e coerenza ottimali. Il blocco del processo funzionerà nelle macchine virtuali della serie HC perché il silicio sottostante viene esposto così com'è alla macchina virtuale guest. Per ulteriori informazioni, vedere [Architettura di Intel Xeon SP](https://bit.ly/2RCYkiE).

Il diagramma seguente mostra la separazione dei core riservati per Azure Hypervisor e la macchina virtuale della serie HC.

![Separazione dei core riservati per Azure Hypervisor e VM serie HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifiche hardware

| Specifiche hardware          | VM serie HC                     |
|----------------------------------|----------------------------------|
| Core                            | 44 (HT disabilitato)                 |
| CPU                              | Intel Xeon Platinum 8168        |
| Frequenza CPU (non AVX)          | 3,7 GHz (core singolo), 2,7-3,4 GHz (tutti i core) |
| Memoria                           | 8 GB/core (352 in totale)            |
| Disco locale                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 |
| Rete                          | 50 Gb Ethernet (40 Gb utilizzabili) Azure seconda Generazione SmartNIC |

## <a name="software-specifications"></a>Specifiche del software

| Specifiche del software     | VM serie HC          |
|-----------------------------|-----------------------|
| Dimensione massima processo MPI            | 13200 core (300 macchine virtuali in un singolo VMSS con singlePlacementGroup: true) |
| Supporto MPI                 | MVAPICH2, OpenMPI, MPICH, Piattaforma MPI, Intel MPI  |
| Framework aggiuntivi       | Comunicazione unificata X, libfabric, PGAS |
| Supporto di Archiviazione di AzureAzure Storage Support       | Std - Premium (massimo 4 dischi) |
| Supporto del sistema operativo per SRIOV RDMA   | CentOS/RHEL 7.6, SLES 12 SP4, WinServer 2016 |
| Azure CycleCloud Support    | Sì                         |
| Azure Batch Support         | Sì                         |

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle dimensioni delle macchine virtuali HPC per Linux e Windows in Azure.Learn more about HPC VM sizes for [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) and [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Altre informazioni su HPC in Azure.Learn more about [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
