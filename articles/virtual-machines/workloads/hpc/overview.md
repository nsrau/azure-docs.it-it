---
title: High Performance Computing in macchine virtuali serie H - Macchine virtuali di Azure
description: Informazioni sulle funzionalità e le caratteristiche delle macchine virtuali serie H ottimizzate per HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271009"
---
# <a name="high-performance-computing-on-h-series-vms"></a>High Performance Computing in macchine virtuali serie H

La funzionalità High Performance Computing (HPC) nelle macchine virtuali serie HB e HC offre la massima ottimizzazione delle prestazioni di HPC di qualsiasi macchina virtuale in Azure. Le macchine virtuali ottimizzate per HPC vengono usate per risolvere alcuni dei più complessi problemi matematici, come la fluidodinamica, la simulazione di petrolio e gas e la creazione di modelli meteorologici.

Questo articolo descrive alcune delle funzionalità principali delle macchine virtuali serie HB e HC, spiega il motivo per cui queste macchine virtuali sono ideali in scenari HPC e illustra come iniziare a usarle.

## <a name="features-and-capabilities"></a>Funzionalità e caratteristiche

Le macchine virtuali serie HB e HC sono progettate per offrire le migliori prestazioni in termini di HPC, scalabilità MPI (Message Passing Interface) ed economicità per i carichi di lavoro HPC.

### <a name="message-passing-interface"></a>Message Passing Interface

Le serie HB e HC supportano quasi tutti i tipi e le versioni di MPI. Alcuni dei più comuni tipi MPI supportati sono: OpenMPI, MVAPICH2, Platform MPI, Intel MPI e tutti i verbi di Accesso diretto a memoria remota (RDMA). Per altre informazioni, vedere [Configurare Message Passing Interface per HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

L'interfaccia RDMA è standard nelle macchine virtuali serie HB e HC. Le istanze con supporto per RDMA comunicano attraverso una rete InfiniBand che opera a velocità dati avanzate (EDR) per le macchine virtuali serie HB e HC. Le istanze con supporto per RDMA possono migliorare la scalabilità e le prestazioni di alcune applicazioni MPI.

La configurazione InfiniBand che supporta le macchine virtuali serie HB e HC è costituita da strutture Fat Tree non bloccanti a diametro ridotto per prestazioni RDMA coerenti.

Per altre informazioni sulla configurazione di InfiniBand nelle macchine virtuali serie HB e HC, vedere [Abilitare InfiniBand](enable-infiniband.md).

## <a name="get-started"></a>Introduzione

Innanzitutto, decidere quale macchina virtuale serie H si intende usare. Per informazioni dettagliate sulle macchine virtuali ottimizzate per HPC, vedere la [panoramica sulla serie HB](hb-series-overview.md) e la [panoramica sulla serie HC](hc-series-overview.md). Per le specifiche, vedere l'articolo sulle [dimensioni delle macchine virtuali HPC](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Dopo aver selezionato e creato una macchina virtuale per l'applicazione, è necessario configurarla abilitando InfiniBand. Per informazioni su come abilitare InfiniBand in macchine virtuali Windows e Linux, vedere [Abilitare InfiniBand](enable-infiniband.md).

MPI è un componente essenziale dei carichi di lavoro HPC. Le serie HB e HC supportano quasi tutti i tipi e le versioni di MPI. Per altre informazioni, vedere [Configurare Message Passing Interface per HPC](setup-mpi.md).

Dopo aver scelto la serie di macchine virtuali, configurare Infiniband e MPI per iniziare a creare carichi di lavoro HPC.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle differenze principali e le specifiche, vedere la [panoramica della serie HB](hb-series-overview.md) e la [panoramica della serie HC](hc-series-overview.md).

- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
