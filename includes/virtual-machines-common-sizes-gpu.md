---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: c0d73b2e37046f75a1cea700d9099c5bbb5f6e0c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170394"
---
Le dimensioni delle macchine virtuali ottimizzate per la GPU sono macchine virtuali specializzate disponibili con una o più GPU NVIDIA. Queste dimensioni sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica. Questo articolo fornisce informazioni relative a numero e tipo di GPU, vCPU, dischi dati e schede di rete. Anche velocità effettiva di archiviazione e larghezza di banda della rete sono incluse per ogni dimensione di questo raggruppamento. 

* Le dimensioni **NC, NCv2, NCv3, ND e NDv2** sono ottimizzate per le applicazioni e gli algoritmi a elevato utilizzo di calcolo e di rete. Alcuni esempi sono applicazioni e simulazioni basate su CUDA e OpenCL, intelligenza artificiale e Deep Learning. La serie NCv3 è progettata per i carichi di lavoro HPC (High-Performance Computing) e dotata di GPU NVIDIA Tesla V100.  La serie ND è progettata per gli scenari di training e inferenza per l'apprendimento profondo. Questa serie usa GPU NVIDIA Tesla P40.

* La **serie NC** è dotata di un processore Intel Xeon® E5-2690 versione 3 da 2,60 GHz.

* Le dimensioni **NCSv3**, **NCSv2** e **ND** sono dotate di un processore Intel Xeon® E5-2690 versione 4 da 2,60 GHz.
                      
* **NV e NVv3** dimensioni sono ottimizzate e progettate per la visualizzazione remota, streaming, giochi, codifica e scenari VDI tramite Framework come OpenGL e DirectX.  Queste macchine virtuali hanno GPU NVIDIA Tesla M60.


## <a name="nc-series"></a>Serie NC

Archiviazione Premium:  Non supportata

Memorizzazione nella cache archiviazione Premium:  Non supportata

Le macchine virtuali serie NC sono basate sulla scheda [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf). Gli utenti possono elaborare i dati più velocemente sfruttando i core CUDA per eseguire l'analisi del consumo delle applicazioni, simulazioni di arresto anomalo, rendering con ray tracing, Deep Learning e altro ancora. La configurazione NC24r offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.


| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1.440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1.440 | 4 | 48 | 64 | 4 |

1 GPU = Pari a metà scheda K80.

*Con supporto di RDMA

## <a name="ncv2-series"></a>Serie NCv2

Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Supportato

Le macchine virtuali serie NCv2 sono basate sulle GPU [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/). Queste GPU possono offrire prestazioni di calcolo più che raddoppiate rispetto a quelle della serie NC. I clienti possono sfruttare i vantaggi di queste GPU aggiornate per carichi di lavoro HPC tradizionali, come la modellazione delle riserve, il sequenziamento del DNA, l'analisi di proteine, le simulazioni Monte Carlo e altro ancora. La configurazione NC24rs v2 offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) nella sottoscrizione è impostata inizialmente su 0 in ogni area. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = Pari a una scheda P100.

*Con supporto di RDMA

## <a name="ncv3-series"></a>Serie NCv3

Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Supportato

Le macchine virtuali serie NCv3 sono basate sulle GPU [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/). Queste GPU possono offrire prestazioni di calcolo una volta e mezzo superiori rispetto a quelle della serie NCv2. I clienti possono sfruttare i vantaggi di queste GPU aggiornate per carichi di lavoro HPC tradizionali, come la modellazione delle riserve, il sequenziamento del DNA, l'analisi di proteine, le simulazioni Monte Carlo e altro ancora. La configurazione NC24rs v3 offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) nella sottoscrizione è impostata inizialmente su 0 in ogni area. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = Pari a una scheda V100.

*Con supporto di RDMA

## <a name="ndv2-series-preview"></a>Serie NDv2 (anteprima)


Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Supportato

InfiniBand: Non supportate


Le macchine virtuali della serie NDv2 sono una novità della famiglia di GPU e sono state progettate per le esigenze di carichi di lavoro di tipo HPC, intelligenza artificiale e Machine Learning. Sono basate su 8 GPU interconnesse di tipo NVIDIA Tesla V100 NVLINK e su 40 core Intel Skylake e offrono 672 GiB di memoria del sistema. Un'istanza NDv2 offre prestazioni eccellenti di tipo FP32 e FP64 per carichi di lavoro per HPC e intelligenza artificiale mediante Cuda, TensorFlow, Pytorch, Caffe e altri framework.

[Iscriviti e ottieni l'accesso a queste macchine virtuali durante l'anteprima](https://aka.ms/ndv2signup).
<br>


| Dimensione              | CPU virtuale | GPU              | Memoria  | NIC (Max) | Max. Dimensioni disco           | Max. (1023 GB ciascuno) | Larghezza di banda di rete massima | 
|-------------------|------|------------------|---------|------------|--------------------------|--------------------------------|-----------------------|
| Standard_ND40s_v2 | 40   | 8 V100 (NVLink) | 672 GiB | 8          | Temporanea 1344 / 2948XIO | 32                             | 24.000 Mbps           |

## <a name="nd-series"></a>Serie ND

Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Supportato

Le macchine virtuali della serie ND sono una novità della famiglia di GPU progettata per carichi di lavoro di intelligenza artificiale e Deep Learning. Offrono prestazioni ottimali per il training e l'inferenza. Le istanze ND sono basate su GPU [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Queste istanze offrono prestazioni eccellenti per le operazioni con precisione singola e virgola mobile e per carichi di lavoro di intelligenza artificiale che usano Microsoft Cognitive Toolkit, TensorFlow, Caffe e altri framework. La serie ND offre anche dimensioni di memoria di GPU significativamente superiori (24 GB), ottimali per modelli di rete neurale molto più grandi. Analogamente alla serie NC, la serie ND offre una configurazione con latenza bassa secondaria, rete con velocità effettiva elevata tramite RDMA e connettività InfiniBand, in modo che sia possibile eseguire processi di training su vasta scala per molte GPU.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) per area nella sottoscrizione è impostata inizialmente su 0. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 96 | 32 | 8 |

1 GPU = Pari a una scheda P40.

*Con supporto di RDMA

## <a name="nv-series"></a>Serie NV

Archiviazione Premium:  Non supportata

Memorizzazione nella cache archiviazione Premium:  Non supportata

Le macchine virtuali delle serie NV sono basate su GPU [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e sulla tecnologia NVIDIA GRID per applicazioni con accelerazione grafica per desktop e desktop virtuali con cui i clienti possono visualizzare i propri dati o le proprie simulazioni. Gli utenti possono visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore ed eseguire anche singoli carichi di lavoro di precisione, come la codifica e il rendering. 

Ogni GPU di istanze NV viene fornita con una licenza GRID. Questa licenza consente di usare un'istanza NV come workstation virtuale per un singolo utente oppure consente a 25 utenti simultanei di connettersi a una macchina virtuale per uno scenario di applicazione virtuale.

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max | Workstation virtuali | Applicazioni virtuali | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1.440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = Pari a metà scheda M60.

## <a name="nvv3-series-preview-sup1sup"></a>Serie NVv3 (anteprima) <sup>1</sup>

Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Supportato

Sono basate su macchine virtuali serie NVv3 [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU e NVIDIA GRID tecnologia con CPU Intel Broadwell. Queste macchine virtuali sono specifiche per applicazioni con grafica accelerata per GPU e desktop virtuali in cui i clienti vogliono visualizzare i propri dati, simulare risultati da visualizzare, lavorare in CAD o eseguire il rendering e lo streaming di contenuti. Queste macchine virtuali possono anche eseguire carichi di lavoro con precisione singola, ad esempio per la codifica e il rendering. Le macchine virtuali NVv3 supportano archiviazione Premium e sono dotate di due volte la memoria di sistema (RAM) quando vengono confrontati con il suo predecessore serie NV.  

Ogni GPU di istanze NVv3 dotata di una licenza GRID. Questa licenza consente di usare un'istanza NV come workstation virtuale per un singolo utente oppure consente a 25 utenti simultanei di connettersi a una macchina virtuale per uno scenario di applicazione virtuale.

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max | Workstation virtuali | Applicazioni virtuali | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v3 | 12 | 112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v3 | 24 | 224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v3 | 48 | 448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 GPU = Pari a metà scheda M60.

<sup>1</sup> macchine virtuali serie NVv3 integrano la tecnologia Intel® Hyper-Threading

 
