---
title: High Performance Computing in macchine virtuali serie H e serie N abilitate per InfiniBand - Macchine virtuali di Azure
description: Informazioni sulle funzionalità e le caratteristiche delle VM serie H e serie N abilitate per InfiniBand e ottimizzate per HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms-subservice: hpc
ms.topic: overview
ms.date: 07/29/2020
ms.reviewer: cynthn
ms.openlocfilehash: e2c3a1559b4a6642f17a2a998e68543a519b1225
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996713"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>High Performance Computing in macchine virtuali serie H e serie N abilitate per InfiniBand

Le macchine virtuali serie H e serie N abilitate per InfiniBand di Azure sono progettate per offrire prestazioni senza confronti, scalabilità MPI (Message Passing Interface) e convenienza per una varietà di carichi di lavoro HPC reali e di intelligenza artificiale. Queste VM ottimizzate per HPC (High Performance Computing) vengono usate per risolvere alcuni problemi che comportano un utilizzo elevato delle risorse di calcolo nel campo delle scienze e dell'ingegneria, come la fluidodinamica, la modellazione geologica, le simulazioni meteorologiche e così via.

Questi articoli forniscono informazioni introduttive sulle macchine virtuali serie H e serie N abilitate per InfiniBand in Azure e sulla configurazione ottimale dei carichi di lavoro HPC e di intelligenza artificiale nelle VM ai fini della scalabilità.

## <a name="features-and-capabilities"></a>Funzionalità e caratteristiche

Le macchine virtuali serie H e serie N abilitate per InfiniBand sono progettate per offrire le migliori prestazioni in termini di HPC, scalabilità MPI (Message Passing Interface) ed economicità per i carichi di lavoro HPC. Vedere gli articoli sulle VM [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) per altre informazioni sulle funzionalità e caratteristiche di queste VM.

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

Le macchine virtuali [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) [con supporto RDMA](../../sizes-hpc.md#rdma-capable-instances) comunicano attraverso la rete InfiniBand a bassa latenza e larghezza di banda elevata. La funzionalità RDMA su un'interconnessione di questo tipo è fondamentale per migliorare la scalabilità e le prestazioni dei carichi di lavoro HPC e di intelligenza artificiale su nodi distribuiti. Le macchine virtuali serie H e serie N abilitate per InfiniBand sono connesse in strutture Fat Tree non bloccanti a diametro ridotto per prestazioni RDMA ottimizzate e coerenti.
Per altre informazioni sulla configurazione di InfiniBand nelle macchine virtuali abilitate per Infiniband, vedere [Abilitare InfiniBand](enable-infiniband.md).

### <a name="message-passing-interface"></a>Message Passing Interface

Le serie H e N abilitate per SR-IOV supportano quasi tutte le librerie e le versioni MPI. Alcune delle più comuni librerie MPI supportate sono: Intel MPI, OpenMPI, MPICH, MVAPICH2, Platform MPI e tutti i verbi di Accesso diretto a memoria remota (RDMA).
Per altre informazioni sull'installazione delle varie librerie MPI supportate e sulla relativa configurazione ottimale, vedere [Configurare MPI](setup-mpi.md).

## <a name="get-started"></a>Introduzione

Il primo passaggio consiste nel selezionare il tipo di VM [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) ottimale per il carico di lavoro in base alle specifiche delle VM e alla [funzionalità RDMA](../../sizes-hpc.md#rdma-capable-instances).
Occorre poi configurare la VM abilitando InfiniBand. A questo scopo sono disponibili vari metodi, tra cui l'uso di immagini di VM ottimizzate con driver incorporati. Per altre informazioni, vedere [Ottimizzazione per Linux](configure.md) e [Abilitare InfiniBand](enable-infiniband.md).
Il terzo passaggio consiste nella scelta e nella configurazione di MPI, fondamentale per i carichi di lavoro nei nodi distribuiti. Per altre informazioni, vedere [Configurare MPI](setup-mpi.md).
Come quarto passaggio, ai fini delle prestazioni e della scalabilità, occorre configurare in modo ottimale i carichi di lavoro seguendo le indicazioni specifiche della famiglia di VM, ad esempio la [panoramica della serie HB](hb-series-overview.md) e la [panoramica della serie HC](hc-series-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [configurazione e l'ottimizzazione](configure.md) delle VM [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) abilitate per InfiniBand.
- Esaminare la [panoramica della serie HB](hb-series-overview.md) e la [panoramica della serie HC](hc-series-overview.md) per informazioni su come configurare in modo ottimale i carichi di lavoro ai fini delle prestazioni e della scalabilità.
- Leggere gli ultimi annunci e alcuni esempi HPC e risultati nei [blog della community tecnica di Calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
