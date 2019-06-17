---
title: Panoramica VM di serie HC - macchine virtuali di Azure | Microsoft Docs
description: Informazioni sul supporto dell'anteprima per le dimensioni di macchine virtuali serie HC in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: f96a1179c103dd9dfb4d358572f9a9adbe24b977
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809873"
---
# <a name="hc-series-virtual-machine-overview"></a>Panoramica delle macchine virtuali serie connessione ibrida

Ottimizzare le prestazioni di applicazioni HPC sui processori Intel Xeon scalabile richiede un approccio con attenzione alla selezione host di processo su questa nuova architettura. In questo caso, viene descritta l'implementazione di esso in macchine virtuali di Azure serie connessione ibrida per le applicazioni HPC. Si userà il termine "pNUMA" per fare riferimento a un dominio NUMA fisica e "vNUMA" per fare riferimento a un dominio NUMA virtuale. Analogamente, si userà il termine "pCore" per fare riferimento ai core CPU fisici e "vCore" per fare riferimento a virtualizzato di core CPU.

Fisicamente, un server di connessione ibrida è 2 * 24-core Intel Xeon Platinum 8168 CPU per un totale di core fisici 48. Ciascuna CPU è un dominio singolo pNUMA e dispone di unified access ai sei canali di DRAM. Funzionalità di Intel Xeon Platinum CPU a 4 volte più grandi della cache L2 rispetto in previa generazioni (256 KB/core-> 1 MB/core), riducendo al tempo la cache L3 rispetto alle precedenti CPU Intel (MB/core 1.375 2,5 MB/core->).

La topologia precedente è ancora presente anche la configurazione di hypervisor HC-series. Per lasciare spazio per l'hypervisor operare senza interferire con la macchina virtuale di Azure, Microsoft si riserva pCores 0-1 e 24-25 (vale a dire, i primi 2 pCores su ogni socket). Quindi, assegniamo pNUMA domini tutti i core rimanenti per la macchina virtuale. In questo modo, verrà visualizzata la macchina virtuale:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` core per ogni macchina virtuale

La macchina virtuale non è a conoscenza che pCores 0-1 e 24-25 non sono stati assegnati a esso. Pertanto, espone ogni vNUMA come se avesse in modo nativo 22 Core.

Intel Xeon Platinum, Gold e Silver CPU introducono anche una rete mesh 2D in dado per la comunicazione all'interno ed esterno nel socket della CPU. Si consiglia di associazione del processo per coerenza e prestazioni ottimali. L'aggiunta di processo funzionerà in macchine virtuali della serie HC perché la silicon sottostante è esposta come-consiste nella macchina virtuale guest. Altre informazioni su architettura Intel Xeon SP in: https://bit.ly/2RCYkiE

Il diagramma seguente mostra la separazione di core riservati per l'Hypervisor di Azure e la macchina virtuale serie connessione ibrida.

![Separazione dei core riservati per l'Hypervisor di Azure e macchine virtuali serie connessione ibrida](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifiche hardware

| Specifiche hardware          | Macchine Virtuali serie connessione ibrida                     |
|----------------------------------|----------------------------------|
| Core                            | 40 (Hyper-Threading disabilitato)                 |
| CPU                              | Intel Xeon Platinum 8168 *        |
| Frequenza della CPU (non-AVX)          | Fino a 3,7 GHz (core singolo), 2.7, 3.4 GHz (tutti i core) |
| Memoria                           | 8 GB/core (352 totale)            |
| Disco locale                       | NVMe 700 GB                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| Rete                          | 50 Gb Ethernet (40 Gb utilizzabile) Azure seconda generazione SmartNIC * * * |

## <a name="software-specifications"></a>Specifiche del software

| Specifiche del software     | Macchine Virtuali serie connessione ibrida          |
|-----------------------------|-----------------------|
| Dimensioni di processi MPI max            | 4400 core (100 set di scalabilità), 8800 core (200 set di scalabilità di macchine virtuali) |
| Supporto di MPI                 | MVAPICH2, OpenMPI, MPICH, piattaforma MPI, Intel MPI  |
| Framework aggiuntivi       | Unified Communication X, libfabric, PGAS |
| Supporto di archiviazione di Azure       | Standard e Premium (massimi 4 dischi) |
| Supporto per RDMA SRIOV del sistema operativo   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Supporto per Azure CycleCloud    | Yes                         |
| Supporto di Azure Batch         | Yes                         |

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle dimensioni delle VM di HPC per [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) in Azure.

* Altre informazioni sulle [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
