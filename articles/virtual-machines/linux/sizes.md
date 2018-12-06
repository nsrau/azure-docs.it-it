---
title: Dimensioni delle macchine virtuali Linux | Documentazione Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali Linux in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/14/2018
ms.author: jonbeck
ms.openlocfilehash: edca3bbc8fd90f0cea328d19471d723e43e91ce3
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705600"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Dimensioni delle macchine virtuali Linux in Azure
Questo articolo descrive le dimensioni e le opzioni disponibili per le macchine virtuali di Azure che è possibile usare per eseguire le app Linux e i carichi di lavoro. Offre anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse. Questo articolo è disponibile anche per le [macchine virtuali Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| type                     | Dimensioni           |    DESCRIZIONE       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Utilizzo generico](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Rapporto equilibrato tra CPU e memoria. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. |
| [Ottimizzate per il calcolo](sizes-compute.md)        | Fsv2, Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server applicazioni.        |
| [Ottimizzate per la memoria](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Rapporto elevato tra memoria e CPU. Soluzione ideale per server di database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](sizes-storage.md)        | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| [GPU](sizes-gpu.md)            | NV, NVv2, NC, NCv2, NCv3, ND, NDv2 (Anteprima)            | Macchine virtuali specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video, nonché per il training e l'inferenza dei modelli (ND) con apprendimento profondo. disponibili con GPU singole o più GPU.       |
| [High Performance Computing (HPC)](sizes-hpc.md) | H       | Le nostre macchine virtuali con CPU più veloci e potenti, con interfacce di rete ad alta velocità effettiva facoltative (RDMA). |


<br>

- Per informazioni sui prezzi di varie dimensioni, vedere [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Per la disponibilità delle dimensioni di VM nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).
- Per trovare i limiti generali delle VM di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-subscription-service-limits.md).
- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.


## <a name="rest-api"></a>API REST

Per informazioni sull'uso dell'API REST per query relative alle dimensioni delle macchine virtuali, vedere gli argomenti seguenti:

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes) (Elencare le dimensioni delle macchine virtuali disponibili per il ridimensionamento)
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) (Elencare le dimensioni delle macchine virtuali disponibili per una sottoscrizione)
- [List available virtual machine sizes in an availability set](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes) (Elencare le dimensioni delle macchine virtuali disponibili in un set di disponibilità)

## <a name="acu"></a>Unità di calcolo di Azure

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

## <a name="benchmark-scores"></a>Punteggi di benchmark

Altre informazioni sulle prestazioni di calcolo per le macchine virtuali Linux usando i [punteggi di benchmark CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle diverse dimensioni di macchina virtuale disponibili:
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- Vedere la pagina [Generazione precedente](sizes-previous-gen.md) per informazioni sulle serie A Standard, Dv1 (D1-4 e D11-14 v1) e A8-A11



