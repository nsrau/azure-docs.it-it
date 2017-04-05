---
title: Dimensioni delle macchine virtuali Linux | Documentazione Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali Linux in Azure.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 446f4257979e6725186b32ad65cef31cdd3e7ede
ms.lasthandoff: 03/24/2017

---

# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Dimensioni delle macchine virtuali Linux in Azure
Questo articolo descrive le dimensioni e le opzioni disponibili per le macchine virtuali di Azure che è possibile usare per eseguire le app Linux e i carichi di lavoro. Offre anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse. Questo articolo è disponibile anche per le [macchine virtuali Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> * Per informazioni sui prezzi di varie dimensioni, vedere [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
> * Per la disponibilità delle dimensioni di VM nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).
> * Per trovare i limiti generali delle VM di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
> * Altre informazioni su come le [unità di calcolo di Azure](virtual-machines-linux-acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
> 
> 

<br>   


| Tipo                     | Dimensioni           |    Descrizione       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Utilizzo generico](virtual-machines-linux-sizes-general.md)          | DSv2, Dv2, DS, D, Av2, A0-7  | Rapporto equilibrato tra CPU e memoria. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. |
| [Ottimizzate per il calcolo](virtual-machines-linux-sizes-compute.md)        | Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server applicazioni.        |
| [Ottimizzate per la memoria](virtual-machines-linux-sizes-memory.md)         | GS, G, DSv2, DS   | Rapporto elevato tra memoria e core. Soluzione ideale per server di database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](virtual-machines-linux-sizes-storage.md)        | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| [GPU](virtual-machines-linux-sizes-gpu.md)            | NV, NC            | Macchine virtuali specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video, disponibili con GPU singole o più GPU.       |
| [High Performance Computing (HPC)](virtual-machines-linux-sizes-hpc.md) | H, A8-11          | Le nostre macchine virtuali con CPU più veloci e potenti, con interfacce di rete ad alta velocità effettiva facoltative (RDMA). 

<br>

Altre informazioni su come le [unità di calcolo di Azure](virtual-machines-linux-acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

Altre informazioni sulle diverse dimensioni di macchina virtuale disponibili:
- [Utilizzo generico](virtual-machines-linux-sizes-general.md)
- [Ottimizzate per il calcolo](virtual-machines-linux-sizes-compute.md)
- [Ottimizzate per la memoria](virtual-machines-linux-sizes-memory.md)
- [Ottimizzate per l'archiviazione](virtual-machines-linux-sizes-storage.md)
- [GPU](virtual-machines-linux-sizes-gpu.md)
- [High Performance Computing (HPC)](virtual-machines-linux-sizes-hpc.md)




