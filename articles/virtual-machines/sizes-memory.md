---
title: Dimensioni delle macchine virtuali in Azure - Memoria | Microsoft Docs
description: Elenca le diverse dimensioni ottimizzate per la memoria disponibili per le macchine virtuali disponibili in Azure. Elenca informazioni sul numero di vCPU, dischi dati e schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Isolamento di VM,VM isolata,isolamento,isolata
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 688508df8b13e00b21da78c1274400ea4b18484a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421332"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per la memoria

Le dimensioni delle macchine virtuali con ottimizzazione per la memoria offrono un rapporto elevato tra memoria e CPU, ideale per server di database relazionali, cache medio-grandi e analisi in memoria. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento.

- Le [Serie Dv2 e DSv2](dv2-dsv2-series-memory.md), un'evoluzione della serie D originale, sono dotate di una CPU più potente. La serie Dv2 è circa il 35% più veloce rispetto alla serie D. È dotata di processori Intel&reg; Xeon&reg; 8171M 2.1 GHz (Skylake) o Intel&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) oppure Intel&reg; Xeon&reg; E5-2673 v3 2.4 GHz (Haswell) e della tecnologia Intel Turbo Boost 2.0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

    Le serie Dv2 e DSv2 sono ideali per le applicazioni che richiedono vCPU più veloci, prestazioni migliori di archiviazione temporanea o requisiti di memoria superiori. Offrono una potente combinazione per molte applicazioni di livello aziendale.

- Le [serie Eav4 e Easv4](eav4-easv4-series.md), dotate di processore AMD 2.35 GHz EPYC<sup>TM</sup> 7452 in una configurazione con tecnologia Multithreading con una cache L3 fino a 256 MB, offrono più opzioni per eseguire la maggior parte dei carichi di lavoro ottimizzati per la memoria. La serie Eav4 e Easv4 hanno le stesse configurazioni di memoria e disco della serie EV3 & Esv3.

- Le serie [Ev3 e Esv3](ev3-esv3-series.md), dotate di processore Intel&reg; Xeon&reg; 8171M 2.1 GHz (Skylake) o Intel&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) in una configurazione con tecnologia Hyper-Threading, costituiscono una proposta di valore di livello più alto per i carichi di lavoro di utilizzo più generico e allineano la serie Ev3 alle macchine virtuali per utilizzo generico della maggior parte degli altri cloud. La memoria è stata estesa (da 7 GiB/vCPU a 8 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading. La serie Ev3 rappresenta un passo avanti rispetto alle dimensioni delle macchine virtuali con memoria elevata delle famiglie D/Dv2.

- La [serie Ev4 e Esv4](ev4-esv4-series.md) è in esecuzione su processori Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) di seconda generazione in una configurazione con Hyper-Threading, ideale per varie applicazioni aziendali con utilizzo intensivo della memoria e funzionalità fino a 504 GiB di RAM. È dotato della tecnologia [Intel &reg; Turbo Boost 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [della &reg; tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e di [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Le serie Ev4 e Esv4 non includono un disco temporaneo locale. Per altre informazioni, vedere [dimensioni delle macchine virtuali di Azure senza dischi temporanei locali](azure-vms-no-temp-disk.md).

- La [serie Edv4 e Edsv4](edv4-edsv4-series.md) è in esecuzione su processori Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) di seconda generazione, ideale per database molto grandi o altre applicazioni che traggono vantaggio da conteggi di vCPU elevati e grandi quantità di memoria. Inoltre, le dimensioni delle VM includono un archivio SSD locale veloce e più grande per le applicazioni che traggono vantaggio dalla bassa latenza e dall'archiviazione locale ad alta velocità. Queste macchine virtuali sono dotate di una velocità di clock Turbo all-core sostenuta di 3,4 GHz e delle tecnologie [Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

- La [serie M](m-series.md) offre un elevato numero di vCPU (fino a 128 vCPU) e una grande quantità di memoria (fino a 3,8 TiB). È ideale anche per database molto grandi o altre applicazioni che traggono vantaggio da un elevato numero di vCPU e una grande quantità di memoria.

- La serie [Mv2](mv2-series.md) offre il maggior numero di vCPU (fino a 416 vCPU) e la maggiore quantità di memoria (fino a 11,4 TiB) rispetto a qualsiasi altra macchina virtuale nel cloud. È ideale per database molto grandi o altre applicazioni che traggono vantaggio da un elevato numero di vCPU e una grande quantità di memoria.

Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente. Queste dimensioni delle macchine virtuali sono particolarmente adatte ai carichi di lavoro che richiedono un elevato livello di isolamento dagli altri clienti, per i carichi di lavoro con aspetti come i requisiti normativi e di conformità. I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Per le opzioni di macchine virtuali isolate, vedere le pagine delle famiglie di macchine virtuali di seguito.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

Per altre informazioni sul modo in cui Azure denomina le proprie macchine virtuali, vedere [convenzioni di denominazione di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/vm-naming-conventions).
