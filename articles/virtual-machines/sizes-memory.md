---
title: Dimensioni delle macchine virtuali di Azure - Memoria Documenti Microsoft
description: Elenca le diverse dimensioni ottimizzate per la memoria disponibili per le macchine virtuali in Azure.Lists the different memory optimized sizes available for virtual machines in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Isolamento di VM,VM isolata,isolamento,isolata
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77493527"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per la memoria

Le dimensioni delle macchine virtuali con ottimizzazione per la memoria offrono un rapporto memoria-CPU elevato, ideale per server di database relazionali, cache di medie e grandi dimensioni e analisi in memoria. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento.

- [Dv2 e DSv2-serie,](dv2-dsv2-series-memory.md)un follow-on alla serie D originale, dispone di una CPU più potente. La serie Dv2 è circa 35% più veloce rispetto alla serie D. Funziona sui processori Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 da 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 v3 da 2,4 GHz (Haswell) e con Intel Turbo Boost Technology 2.0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

    Le serie Dv2 e DSv2 sono ideali per le applicazioni che richiedono vCPU più veloci, migliori prestazioni di archiviazione temporanea o richiedono richieste di memoria più elevate. Offrono una potente combinazione per molte applicazioni di livello aziendale.

- Le [serie Eav4 ed Easv4](eav4-easv4-series.md) utilizzano il processore AMD 2.35Ghz EPYC<sup>TM</sup> 7452 in una configurazione multithread con una cache L3 fino a 256 MB, aumentando le opzioni per l'esecuzione della maggior parte dei carichi di lavoro ottimizzati per la memoria. La serie Eav4 e la serie Easv4 hanno le stesse configurazioni di memoria e disco della serie Ev3 & Esv3.

- Processore Intel® Xeon (Skylake) [serie Ev3 ed Esv3®](ev3-esv3-series.md) 8171M 2.1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 da 2,3 GHz (Broadwell) in una configurazione con hyperthreading, fornendo una migliore proposta di valore per la maggior parte dei carichi di lavoro generali e portando l'Ev3 all'allineamento con i VM di uso generale della maggior parte delle altre nuvole. La memoria è stata espansa (da 7 GiB/vCPU a 8 GiB/vCPU) mentre i limiti del disco e della rete sono stati regolati su base per core per allinearsi con il passaggio all'hyper-threading. La serie Ev3 rappresenta un passo avanti rispetto alle dimensioni delle macchine virtuali con memoria elevata delle famiglie D/Dv2.

- La [serie M](m-series.md) offre un elevato numero di vCPU (fino a 128 vCPU) e una grande quantità di memoria (fino a 3,8 TiB). È ideale anche per database estremamente grandi o altre applicazioni che traggono vantaggio da elevati conteggi vCPU e grandi quantità di memoria.

- La [serie Mv2](mv2-series.md) offre il numero di vCPU più alto (fino a 416 vCPU) e la memoria più grande (fino a 8.19 TiB) di qualsiasi VM nel cloud. È ideale per database molto grandi o altre applicazioni che traggono vantaggio da un elevato numero di vCPU e una grande quantità di memoria.

Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente. Queste dimensioni delle macchine virtuali sono particolarmente adatte ai carichi di lavoro che richiedono un elevato livello di isolamento dagli altri clienti, per i carichi di lavoro con aspetti come i requisiti normativi e di conformità. I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Vedere le pagine per le famiglie di macchine virtuali seguenti per le opzioni di VM isolate.

## <a name="other-sizes"></a>Altre dimensioni

- [Finalità generale](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.