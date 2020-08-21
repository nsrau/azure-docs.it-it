---
title: Panoramica delle VM serie HB-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sul supporto in anteprima per le dimensioni della macchina virtuale serie HB in Azure.
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
ms.openlocfilehash: 7c66af5184c4a943fd4b3185a87623112fe0d954
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691242"
---
# <a name="hb-series-virtual-machines-overview"></a>Panoramica delle macchine virtuali serie HB

Per ottimizzare le prestazioni delle applicazioni HPC (High Performance Computing) in AMD EPYC, è necessario un approccio ponderato della località di memoria e del posizionamento del processo. Di seguito viene illustrata l'architettura AMD EPYC e la relativa implementazione in Azure per le applicazioni HPC. Il termine "pNUMA" viene usato per fare riferimento a un dominio NUMA fisico e "vNUMA" per fare riferimento a un dominio NUMA virtualizzato.

Fisicamente, un server della [serie HB](../../hb-series.md) è 2 * 32-core EPYC 7551 CPU per un totale di 64 core fisici. Questi 64 core sono divisi in 16 domini pNUMA (8 per socket), ognuno dei quali è costituito da quattro core e noto come "complesso di CPU" (o "CCX"). Ogni CCX ha una propria cache L3, che rappresenta il modo in cui un sistema operativo visualizzerà un limite di pNUMA/vNUMA. Una coppia di CCXs adiacenti condivide l'accesso a due canali di DRAM fisico (32 GB di DRAM nei server della serie HB).

Per consentire il funzionamento dell'hypervisor di Azure senza interferire con la macchina virtuale, viene riservato il dominio pNUMA fisico 0 (il primo CCX). Si assegnano quindi i domini pNUMA 1-15 (le unità CCX rimanenti) per la macchina virtuale. La VM visualizzerà:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` Core per macchina virtuale

La macchina virtuale non sa che pNUMA 0 non è stato assegnato. La macchina virtuale riconosce pNUMA 1-15 come vNUMA 0-14, con 7 vNUMA su vSocket 0 e 8 vNUMA su vSocket 1. Sebbene sia asimmetrica, il sistema operativo dovrebbe avviarsi e funzionare normalmente. Più avanti in questa guida, viene indicato come eseguire le applicazioni MPI in questo layout NUMA asimmetrico.

Il blocco dei processi funziona sulle VM serie HB perché il silicio sottostante viene esposto così com'è alla macchina virtuale guest. Si consiglia vivamente di bloccare i processi per ottenere prestazioni e coerenza ottimali.

Il diagramma seguente illustra la separazione dei core riservati per l'hypervisor di Azure e la macchina virtuale della serie HB.

![Separazione dei core riservati per la VM di Azure hypervisor e della serie HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifiche hardware

| Specifiche hardware                | VM serie HB                     |
|----------------------------------|----------------------------------|
| Core                            | 60 (SMT disabilitato)                |
| CPU                              | AMD EPYC 7551                    |
| Frequenza CPU (non-AVX)          | ~ 2,55 GHz (singolo + tutti i core)   |
| Memoria                           | 4 GB/Core (Totale 240 GB)         |
| Disco locale                       | SSD 700 GB                       |
| Infiniband                       | 100 GB EDR Mellanox ConnectX-5 |
| Rete                          | 50 GB Ethernet (40 GB utilizzabili) Azure Second gen SmartNIC |

## <a name="software-specifications"></a>Specifiche del software

| Specifiche del software           |VM serie HB           |
|-----------------------------|-----------------------|
| Dimensioni massime del processo MPI            | 18000 Core (300 VM in un singolo set di scalabilità di macchine virtuali con singlePlacementGroup = true)  |
| Supporto MPI                 | HPC-X, Intel MPI, OpenMPi, MVAPICH2, MPICH, Platform MPI  |
| Framework aggiuntivi       | Comunicazione unificata X, libfabric, PGA |
| Supporto per archiviazione di Azure       | Dischi standard e Premium (massimo 4 dischi) |
| Supporto del sistema operativo per SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Supporto per l'agente di orchestrazione        | CycleCloud, batch  |

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sull' [architettura AMD EPYC](https://bit.ly/2Epv3kC) e sulle architetture a più [chip](https://bit.ly/2GpQIMb). Per informazioni più dettagliate, vedere la [Guida all'ottimizzazione HPC per processori AMD EPYC](https://bit.ly/2T3AWZ9).
- Leggere gli ultimi annunci e alcuni esempi HPC e risultati nei [blog della community tecnica di Calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
