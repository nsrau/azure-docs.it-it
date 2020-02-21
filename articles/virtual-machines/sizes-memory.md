---
title: Dimensioni delle macchine virtuali di Azure-memoria | Microsoft Docs
description: Elenca le diverse dimensioni con ottimizzazione per la memoria disponibili per le macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
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
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493527"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per la memoria

Le dimensioni delle macchine virtuali con ottimizzazione per la memoria offrono un rapporto memoria-CPU elevato, ideale per server di database relazionali, cache di medie e grandi dimensioni e analisi in memoria. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento.

- La [serie dv2 e DSv2](dv2-dsv2-series-memory.md), una versione successiva alla serie D originale, offre una CPU più potente. La serie dv2 è più veloce del 35% rispetto alla serie D. Viene eseguito sui processori Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) e con la tecnologia Intel Turbo Boost 2,0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

    Le serie dv2 e DSv2 sono ideali per le applicazioni che richiedono un vCPU più veloce, prestazioni di archiviazione temporanee migliori o richieste di memoria più elevate. Offrono una potente combinazione per molte applicazioni di livello aziendale.

- La [serie Eav4 e Easv4](eav4-easv4-series.md) usa il processore AMD 2.35 GHz EPYC<sup>TM</sup> 7452 in una configurazione multithread con una cache L3 fino a 256 MB, aumentando le opzioni per l'esecuzione della maggior parte dei carichi di lavoro con ottimizzazione per la memoria. La serie Eav4 e la serie Easv4 hanno le stesse configurazioni di memoria e disco della serie EV3 & Esv3.

- La [serie EV3 e Esv3](ev3-esv3-series.md) Intel® Xeon® 8171M 2,1 GHz (Skylake) o il processore Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) in una configurazione con Hyper-Threading, garantendo una proposta di valore migliore per la maggior parte dei carichi di lavoro per uso generico e portando l'allineamento dei EV3 con le VM per utilizzo generico della maggior parte degli altri cloud. La memoria è stata espansa (da 7 GiB/vCPU a 8 GiB/vCPU), mentre i limiti del disco e della rete sono stati modificati in base al core per allinearsi al passaggio alla tecnologia Hyper-Threading. La serie Ev3 rappresenta un passo avanti rispetto alle dimensioni delle macchine virtuali con memoria elevata delle famiglie D/Dv2.

- La [serie M](m-series.md) offre un numero elevato di vCPU (fino a 128 vCPU) e una grande quantità di memoria (fino a 3,8 TIB). È ideale anche per database di dimensioni molto grandi o altre applicazioni che traggono vantaggio da conteggi vCPU elevati e grandi quantità di memoria.

- La [serie Mv2](mv2-series.md) offre il numero massimo di vCPU (fino a 416 vCPU) e la memoria massima (fino a 8,19 TIB) di qualsiasi macchina virtuale nel cloud. È ideale per database molto grandi o altre applicazioni che traggono vantaggio da un elevato numero di vCPU e una grande quantità di memoria.

Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente. Queste dimensioni delle macchine virtuali sono particolarmente adatte ai carichi di lavoro che richiedono un elevato livello di isolamento dagli altri clienti, per i carichi di lavoro con aspetti come i requisiti normativi e di conformità. I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Per le opzioni di VM isolate, vedere le pagine relative alle famiglie di macchine virtuali.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.