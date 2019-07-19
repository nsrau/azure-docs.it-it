---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 0325fc8cabc43988fb27a307921977b9b487c123
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286204"
---
Le dimensioni delle macchine virtuali ottimizzate per la GPU sono macchine virtuali specializzate disponibili con una o più GPU NVIDIA. Queste dimensioni sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica. Questo articolo fornisce informazioni relative a numero e tipo di GPU, vCPU, dischi dati e schede di rete. Anche velocità effettiva di archiviazione e larghezza di banda della rete sono incluse per ogni dimensione di questo raggruppamento.

* Le dimensioni **NC, NCv2 e NCv3** sono ottimizzate per le applicazioni e gli algoritmi a elevato utilizzo di calcolo e di rete. Alcuni esempi sono applicazioni e simulazioni basate su CUDA e OpenCL, intelligenza artificiale e Deep Learning. La serie NCv3 è progettata per i carichi di lavoro HPC (High-Performance Computing) e dotata di GPU NVIDIA Tesla V100. La serie NC USA il processore Intel Xeon E5-2690 V3 2.60 GHz V3 (Haswell) e le VM serie NCv2 e NCv3 usano il processore Intel Xeon E5-2690 V4 (Broadwell).

* **ND e NDv2** La serie ND si concentra sugli scenari di formazione e inferenza per l'apprendimento avanzato. Usa la GPU NVIDIA Tesla P40 e il processore Intel Xeon E5-2690 V4 (Broadwell). La serie NDv2 usa il processore Intel Xeon Platinum 8168 (Skylake).

* Le dimensioni **NV e NVv3** sono ottimizzate e progettate per scenari di visualizzazione remota, streaming, giochi, codifica e VDI usando Framework come OpenGL e DirectX.  Queste macchine virtuali hanno GPU NVIDIA Tesla M60.

## <a name="nc-series"></a>Serie NC

Archiviazione Premium:  Non supportato

Caching archiviazione Premium:  Non supportato

Le macchine virtuali della serie NC sono basate sulla scheda [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) e sul processore Intel Xeon E5-2690 V3 (Haswell). Gli utenti possono elaborare i dati più velocemente sfruttando i core CUDA per eseguire l'analisi del consumo delle applicazioni, simulazioni di arresto anomalo, rendering con ray tracing, Deep Learning e altro ancora. La configurazione NC24r offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1\.440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1\.440 | 4 | 48 | 64 | 4 |

1 GPU = Pari a metà scheda K80.

*Con supporto di RDMA

## <a name="ncv2-series"></a>Serie NCv2

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

Le macchine virtuali serie NCv2 sono basate sulle GPU [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/). Queste GPU possono offrire prestazioni di calcolo più che raddoppiate rispetto a quelle della serie NC. I clienti possono sfruttare i vantaggi di queste GPU aggiornate per carichi di lavoro HPC tradizionali, come la modellazione delle riserve, il sequenziamento del DNA, l'analisi di proteine, le simulazioni Monte Carlo e altro ancora. Oltre alle GPU, le macchine virtuali della serie NCv2 sono basate anche su CPU Intel Xeon E5-2690 V4 (Broadwell).

La configurazione NC24rs v2 offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) nella sottoscrizione è impostata inizialmente su 0 in ogni area. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = Pari a una scheda P100.

*Con supporto di RDMA

## <a name="ncv3-series"></a>Serie NCv3

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

Le macchine virtuali serie NCv3 sono basate sulle GPU [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/). Queste GPU possono offrire prestazioni di calcolo una volta e mezzo superiori rispetto a quelle della serie NCv2. I clienti possono sfruttare i vantaggi di queste GPU aggiornate per carichi di lavoro HPC tradizionali, come la modellazione delle riserve, il sequenziamento del DNA, l'analisi di proteine, le simulazioni Monte Carlo e altro ancora. La configurazione NC24rs v3 offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati. Oltre alle GPU, le macchine virtuali della serie NCv3 sono basate anche su CPU Intel Xeon E5-2690 V4 (Broadwell).

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) nella sottoscrizione è impostata inizialmente su 0 in ogni area. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = Pari a una scheda V100.

*Con supporto di RDMA

## <a name="ndv2-series-preview"></a>Serie NDv2 (anteprima)

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

InfiniBand: Non supportate

Le macchine virtuali della serie NDv2 sono una novità della famiglia di GPU e sono state progettate per le esigenze di carichi di lavoro di tipo HPC, intelligenza artificiale e Machine Learning. È alimentato da 8 GPU NVIDIA Tesla V100 NVLINK interconnesse e 40 core Intel Xeon Platinum 8168 (Skylake) e 672 GiB della memoria di sistema. Un'istanza NDv2 offre prestazioni eccellenti di tipo FP32 e FP64 per carichi di lavoro per HPC e intelligenza artificiale mediante Cuda, TensorFlow, Pytorch, Caffe e altri framework.

[Iscriviti e ottieni l'accesso a queste macchine virtuali durante l'anteprima](https://aka.ms/ndv2signup).
<br>

| Dimensione | vCPU | GPU | Memoria | NIC (Max) | Archiviazione temporanea (SSD) GiB | Max. dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Larghezza di banda di rete massima | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND40s_v2 | 40 | 8 V100 (NVLink) | 672 GiB | 8 | 2948 | 32 | 80000/800 | 24000 Mbps |

## <a name="nd-series"></a>Serie ND

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

Le macchine virtuali della serie ND sono una novità della famiglia di GPU progettata per carichi di lavoro di intelligenza artificiale e Deep Learning. Offrono prestazioni ottimali per il training e l'inferenza. Le istanze ND sono basate su GPU [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) e CPU Intel Xeon E5-2690 V4 (Broadwell). Queste istanze offrono prestazioni eccellenti per le operazioni con precisione singola e virgola mobile e per carichi di lavoro di intelligenza artificiale che usano Microsoft Cognitive Toolkit, TensorFlow, Caffe e altri framework. La serie ND offre anche dimensioni di memoria di GPU significativamente superiori (24 GB), ottimali per modelli di rete neurale molto più grandi. Analogamente alla serie NC, la serie ND offre una configurazione con latenza bassa secondaria, rete con velocità effettiva elevata tramite RDMA e connettività InfiniBand, in modo che sia possibile eseguire processi di training su vasta scala per molte GPU.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) per area nella sottoscrizione è impostata inizialmente su 0. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = Pari a una scheda P40.

*Con supporto di RDMA

## <a name="nv-series"></a>Serie NV

Archiviazione Premium:  Non supportato

Caching archiviazione Premium:  Non supportato

Le macchine virtuali delle serie NV sono basate su GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e sulla tecnologia NVIDIA GRID per applicazioni con accelerazione grafica per desktop e desktop virtuali con cui i clienti possono visualizzare i propri dati o le proprie simulazioni. Gli utenti possono visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore ed eseguire anche singoli carichi di lavoro di precisione, come la codifica e il rendering. Le macchine virtuali serie NV sono basate anche su CPU Intel Xeon E5-2690 V3 (Haswell).

Ogni GPU di istanze NV viene fornita con una licenza GRID. Questa licenza consente di usare un'istanza NV come workstation virtuale per un singolo utente oppure consente a 25 utenti simultanei di connettersi a una macchina virtuale per uno scenario di applicazione virtuale.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max | Workstation virtuali | Applicazioni virtuali |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1\.440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = Pari a metà scheda M60.

## <a name="nvv3-series--sup1sup"></a>Serie NVv3 <sup>1</sup>

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

Le macchine virtuali della serie NVv3 sono basate su GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e sulla tecnologia NVIDIA Grid con CPU Intel E5-2690 V4 (Broadwell). Queste macchine virtuali sono specifiche per applicazioni con grafica accelerata per GPU e desktop virtuali in cui i clienti vogliono visualizzare i propri dati, simulare risultati da visualizzare, lavorare in CAD o eseguire il rendering e lo streaming di contenuti. Queste macchine virtuali possono anche eseguire carichi di lavoro con precisione singola, ad esempio per la codifica e il rendering. Le macchine virtuali NVv3 supportano archiviazione Premium e sono disponibili due volte la memoria di sistema (RAM) rispetto alla serie NV precedente.  

Ogni GPU nelle istanze di NVv3 viene fornita con una licenza GRID. Questa licenza consente di usare un'istanza NV come workstation virtuale per un singolo utente oppure consente a 25 utenti simultanei di connettersi a una macchina virtuale per uno scenario di applicazione virtuale.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max | Workstation virtuali | Applicazioni virtuali | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = Pari a metà scheda M60.

<sup>1</sup> VM serie NVv3 funzionalità tecnologia Hyper-Threading Intel
