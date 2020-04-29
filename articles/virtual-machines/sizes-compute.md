---
title: Dimensioni delle macchine virtuali di Azure-ottimizzate per il calcolo | Microsoft Docs
description: Elenca le diverse dimensioni ottimizzate per il calcolo disponibili per le macchine virtuali in Azure. Elenca le informazioni sul numero di vCPU, dischi dati e NIC, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda di rete per le dimensioni di questa serie.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269636"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per il calcolo

Le dimensioni delle macchine virtuali ottimizzate per il calcolo hanno un rapporto elevato tra CPU e memoria. Queste dimensioni sono valide per server Web con traffico medio, appliance di rete, processi batch e server applicazioni. Questo articolo fornisce informazioni sul numero di vCPU, dischi dati e schede di rete. Sono inoltre incluse informazioni sulla velocità effettiva di archiviazione e sulla larghezza di banda di rete per ogni dimensione di questo raggruppamento.

La [serie Fsv2](fsv2-series.md) è basata sul processore Intel® Xeon® Platinum 8168. Include una velocità di clock di base di livello principale di 3,4 GHz e una frequenza Turbo Single Core massima di 3,7 GHz. Le istruzioni Intel® AVX-512 sono nuove per i processori Intel scalabili. Queste istruzioni forniscono fino a un incremento delle prestazioni di 2X ai carichi di lavoro di elaborazione vettoriali nelle operazioni a virgola mobile a precisione singola e doppia. In altre parole, sono molto veloci per qualsiasi carico di lavoro di calcolo.

Con un prezzo di listino orario più basso, la serie Fsv2 presenta il migliore rapporto prezzo-prestazioni nel portfolio Azure basato sull'unità di calcolo di Azure (ACU, Azure Compute Unit) per ogni vCPU.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
