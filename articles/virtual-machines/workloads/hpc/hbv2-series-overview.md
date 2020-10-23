---
title: Panoramica delle VM serie HBv2-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle dimensioni delle macchine virtuali della serie HBv2 in Azure.
services: virtual-machines
author: vermagit
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 48366f205ed8eb2d179bdc39c8da3d673f066a69
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332638"
---
# <a name="hbv2-series-virtual-machine-overview"></a>Panoramica delle macchine virtuali serie HBv2 

 
Per ottimizzare le prestazioni delle applicazioni HPC (High Performance Computing) in AMD EPYC, è necessario un approccio ponderato della località di memoria e del posizionamento del processo. Di seguito viene illustrata l'architettura AMD EPYC e la relativa implementazione in Azure per le applicazioni HPC. Si userà il termine **pNUMA** per fare riferimento a un dominio NUMA fisico e **vNUMA** per fare riferimento a un dominio NUMA virtualizzato. 

Fisicamente, un server della [serie HBv2](../../hbv2-series.md) è 2 * 64-core EPYC 7742 CPU per un totale di 128 core fisici. Questi 128 core sono divisi in domini 32 pNUMA (16 per socket), ognuno dei quali è costituito da 4 core e definito da AMD come un **nucleo complesso** (o **CCX**). Ogni CCX ha una propria cache L3, che rappresenta il modo in cui un sistema operativo visualizzerà un limite di pNUMA/vNUMA. Quattro CCXs adiacenti condividono l'accesso a 2 canali di MEMORIe fisiche. 

Per consentire il funzionamento dell'hypervisor di Azure senza interferire con la macchina virtuale, si riservano i domini pNUMA fisici 0 e 16 (ovvero il primo CCX di ogni socket della CPU). Tutti i restanti 30 domini pNUMA vengono assegnati alla macchina virtuale, a quel punto diventano vNUMA. Quindi, la macchina virtuale visualizzerà:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` Core per macchina virtuale 

La macchina virtuale non ha la consapevolezza che pNUMA 0 e 16 sono riservati. Enumera il vNUMA che vede come 0-29, con 15 vNUMA per socket in modo simmetrico, vNUMA 0-14 su vSocket 0 e vNUMA 15-29 su vSocket 1. Nella sezione successiva sono disponibili istruzioni sul modo migliore per eseguire applicazioni MPI in questo layout NUMA asimmetrico. 

Il blocco dei processi funzionerà sulle macchine virtuali della serie HBv2, perché il silicio sottostante viene esposto così com'è alla macchina virtuale guest. Si consiglia vivamente di bloccare i processi per ottenere prestazioni e coerenza ottimali. 


## <a name="hardware-specifications"></a>Specifiche hardware 

| Specifiche hardware          | VM serie HBv2                   | 
|----------------------------------|----------------------------------|
| Core                            | 120 (SMT disabilitato)               | 
| CPU                              | AMD EPYC 7742                    | 
| Frequenza CPU (non-AVX)          | ~ 3,1 GHz (singolo + tutti i core)    | 
| Memoria                           | 4 GB/Core (Totale 480 GB)         | 
| Disco locale                       | 960 GB NVMe (Block), 480 GB SSD (file di paging) | 
| Infiniband                       | 200 GB/s EDR Mellanox ConnectX-6 | 
| Rete                          | 50 GB/s Ethernet (40 GB/s utilizzabile) Azure Second gen SmartNIC | 


## <a name="software-specifications"></a>Specifiche del software 

| Specifiche del software     | VM serie HBv2                                            | 
|-----------------------------|-----------------------------------------------------------|
| Dimensioni massime del processo MPI            | 36000 Core (300 VM in un singolo set di scalabilità di macchine virtuali con singlePlacementGroup = true) |
| Supporto MPI                 | HPC-X, Intel MPI, OpenMPi, MVAPICH2, MPICH, Platform MPI  |
| Framework aggiuntivi       | Comunicazione unificata X, libfabric, PGA                  |
| Supporto per archiviazione di Azure       | Dischi standard e Premium (massimo 8 dischi)              |
| Supporto del sistema operativo per SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Supporto per l'agente di orchestrazione        | CycleCloud, batch                                         | 


## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sull' [architettura AMD EPYC](https://bit.ly/2Epv3kC) e sulle architetture a più [chip](https://bit.ly/2GpQIMb). Per informazioni più dettagliate, vedere la [Guida all'ottimizzazione HPC per processori AMD EPYC](https://bit.ly/2T3AWZ9).
- Per informazioni sugli annunci più recenti e su alcuni esempi HPC, vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).