---
title: Dimensioni delle macchine virtuali in Azure - Utilizzo generico | Microsoft Docs
description: Elenca le diverse dimensioni disponibili per l'utilizzo generico per le macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dischi dati e schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: a0d095c22de28368edc11fe9ab8e658c0d3ae7f6
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90053804"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali di utilizzo generico

Le dimensioni delle macchine virtuali di utilizzo generico offrono un rapporto CPU-memoria equilibrato. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. Questo articolo offre informazioni sulle offerte per un utilizzo generico dei computer.

- Le macchine virtuali [serie Av2](av2-series.md) possono essere distribuite su una vasta gamma di tipi di hardware e processori. Le VM serie A offrono configurazioni di memoria e prestazioni della CPU ideali per i carichi di lavoro di base, ad esempio quelli di sviluppo e test. La dimensione è limitata in base all'hardware per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale. I casi d'uso di esempio includono server di sviluppo e test, server Web con poco traffico, database da piccoli a medi, modelli di prova e repository di codice.

  > [!NOTE]
  > Il ritiro delle macchine virtuali A8 -A11 è previsto per marzo 2021. Per altre informazioni, vedere [Guida alla migrazione HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

- Le VM [serie B con possibilità di burst](sizes-b-series-burstable.md) sono ideali per carichi di lavoro che non necessitano in modo continuativo delle prestazioni complete della CPU, ad esempio server Web, database di piccole dimensioni e ambienti di sviluppo e test. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre a questi clienti la possibilità di acquistare dimensioni per la VM con prestazioni baseline in relazione al prezzo, che consentono all'istanza della VM di accumulare crediti quando la VM utilizza meno prestazioni di quelle base. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline della VM, usando fino al 100% della CPU quando l'applicazione richiede prestazioni superiori per la CPU.

- Le [serie Dav4 e Dasv4](dav4-dasv4-series.md) sono nuove dimensioni dotate di processore AMD 2.35Ghz EPYC<sup>TM</sup> 7452 in una configurazione con tecnologia Multithreading e cache L3 fino a 256 MB. 8 MB della cache L3 sono dedicati a tutti gli 8 core, aumentando così la possibilità di eseguire carichi di lavoro per utilizzo generico. Le serie Dav4 e Dasv4 hanno le stesse configurazioni di memoria e disco della serie D e Dsv3.

- [Serie DV4 e Dsv4](dv4-dsv4-series.md) La serie DV4 e Dsv4 viene eseguita sui processori Intel® Xeon® Platinum 8272CL (Cascade Lake) in una configurazione con Hyper-Threading, offrendo una proposta di valore migliore per i carichi di lavoro di uso generale. Questa funzionalità è stata sostenuta per tutte le velocità di clock di base del Turbo di 3,4 GHz.

- [Serie Ddv4 e Ddsv4](ddv4-ddsv4-series.md) La serie Ddv4 e Ddsv4 viene eseguita sui processori Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) in una configurazione con Hyper-Threading, garantendo una proposta di valore migliore per la maggior parte dei carichi di lavoro per uso generico. Queste macchine virtuali sono dotate di una velocità di clock Turbo all-core sostenuta di 3,4 GHz e delle tecnologie [Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Supportano anche il [ &reg; potenziamento di Intel Deep Learning](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Le nuove dimensioni di queste macchine virtuali prevedono una risorsa di archiviazione locale più grande del 50%, oltre a un numero di operazioni di I/O al secondo, sia in lettura che in scrittura, più alto rispetto alle dimensioni [Dv3/Dsv3](./dv3-dsv3-series.md) delle [macchine virtuali Gen2](./linux/generation-2.md).

- [Serie dv3 e Dsv3](dv3-dsv3-series.md) Le macchine virtuali vengono eseguite in una seconda generazione Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) in una configurazione con Hyper-Threading, garantendo una proposta di valore migliore per la maggior parte La memoria è stata estesa (da ~3,5 GiB/vCPU a 4 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading. La serie Dv3 non ha più le dimensioni delle macchine virtuali con memoria elevata della serie D/Dv2, che sono state spostate nella nuova [serie Ev3 e Esv3](ev3-esv3-series.md) ottimizzata per la memoria.

- Le macchine virtuali [serie Dv2 e Dsv2](dv2-dsv2-series.md), un'evoluzione della serie D originale, offrono una CPU più potente e una configurazione ottimale del rapporto tra CPU e memoria e sono quindi ideali per la maggior parte dei carichi di lavoro di produzione. La serie Dv2 è circa il 35% più veloce rispetto alla serie D. La serie dv2 viene eseguita su una seconda generazione Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) con la tecnologia Intel Turbo Boost 2,0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

- La [serie DCv2](dcv2-series.md) consente di proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Queste macchine sono supportate dall'ultima generazione di processori Intel XEON E-2288G con tecnologia SGX. Con la tecnologia Intel Turbo Boost, queste macchine possono arrivare fino a 5,0 GHz. Le istanze della serie DCv2 consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati mentre sono in uso.

## <a name="other-sizes"></a>Altre dimensioni

- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

Per altre informazioni sul modo in cui Azure denomina le proprie macchine virtuali, vedere [convenzioni di denominazione di macchine virtuali di Azure](./vm-naming-conventions.md).
