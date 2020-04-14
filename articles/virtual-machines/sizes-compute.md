---
title: Dimensioni delle macchine virtuali di Azure - Ottimizzazione del calcolo Documenti Microsoft
description: Elenca le diverse dimensioni ottimizzate per il calcolo disponibili per le macchine virtuali in Azure.Lists the different compute optimized sizes available for virtual machines in Azure. Elenca le informazioni sul numero di vCPU, dischi dati e schede di interfaccia di rete, nonché la velocità effettiva di archiviazione e la larghezza di banda di rete per le dimensioni in questa serie.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269636"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per il calcolo

Le dimensioni delle macchine virtuali ottimizzate per il calcolo hanno un rapporto elevato tra CPU e memoria. Queste dimensioni sono adatte per server Web con traffico medio, appliance di rete, processi batch e server applicazioni. In questo articolo vengono fornite informazioni sul numero di vCPU, dischi dati e schede di interfaccia di rete. Include inoltre informazioni sulla velocità effettiva di archiviazione e sulla larghezza di banda di rete per ogni dimensione in questo raggruppamento.

La [serie Fsv2](fsv2-series.md) è basata sul processore Intel® Xeon® Platinum 8168. È dotato di una velocità di clock Turbo a tutto il core di 3,4 GHz e una frequenza massima di turbo single-core di 3,7 GHz. Le istruzioni Intel® AVX-512 sono una novità sui processori scalabili Intel. Queste istruzioni forniscono fino a un incremento delle prestazioni 2X ai carichi di lavoro di elaborazione vettoriale su operazioni a virgola mobile a precisione singola e doppia. In altre parole, sono molto veloci per qualsiasi carico di lavoro computazionale.

Con un prezzo di listino orario più basso, la serie Fsv2 presenta il migliore rapporto prezzo-prestazioni nel portfolio Azure basato sull'unità di calcolo di Azure (ACU, Azure Compute Unit) per ogni vCPU.

## <a name="other-sizes"></a>Altre dimensioni

- [Scopo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
