---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 03/05/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 96826b2f8acd579cbfe30f2e524d94ce4867df30
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
Le dimensioni delle macchine virtuali ottimizzate per la GPU sono macchine virtuali specializzate disponibili con una o più GPU NVIDIA. Queste dimensioni sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica. Questo articolo offre informazioni sul numero e sul tipo di GPU, vCPU, dischi dati e schede di interfaccia di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento. 

* Le dimensioni **NC, NCv2, NCv3 e ND** sono adatte per applicazioni e algoritmi a elevato utilizzo di calcolo e reti, tra cui applicazioni e simulazioni basate su CUDA e OpenCL, intelligenza artificiale e Deep Learning. 
* Le dimensioni **NV** sono ottimizzate e progettate per le operazioni di visualizzazione remota, streaming, giochi, codifica e scenari VDI che utilizzano framework come OpenGL e DirectX.  


## <a name="nc-series"></a>Serie NC

Le macchine virtuali serie NC sono basate sulla scheda [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf). Gli utenti possono elaborare i dati più velocemente sfruttando i core CUDA per eseguire l'analisi del consumo delle applicazioni, simulazioni di arresto anomalo, rendering con ray tracing, Deep Learning e altro ancora. La configurazione NC24r offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.


| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Valore massimo per dischi di dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1.440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1.440 | 4 | 64 | 4 |

1 GPU = Pari a metà scheda K80.

*Con supporto di RDMA

## <a name="ncv2-series"></a>Serie NCv2

Le macchine virtuali serie NCv2 sono basate sulle GPU [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf). Queste GPU possono offrire prestazioni di calcolo più che raddoppiate rispetto a quelle della serie NC. I clienti possono sfruttare i vantaggi di queste GPU aggiornate per carichi di lavoro HPC tradizionali, come la modellazione delle riserve, il sequenziamento del DNA, l'analisi di proteine, le simulazioni Monte Carlo e altro ancora. La configurazione NC24rs v2 offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) nella sottoscrizione è impostata inizialmente su 0 in ogni area. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Valore massimo per dischi di dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 336 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 672 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 1344 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 1344 | 4 | 32 | 8 |

1 GPU = Pari a una scheda P100.

*Con supporto di RDMA

## <a name="ncv3-series"></a>Serie NCv3

Le macchine virtuali serie NCv3 sono basate sulle GPU [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf). Queste GPU possono offrire prestazioni di calcolo una volta e mezzo superiori rispetto a quelle della serie NCv2. I clienti possono sfruttare i vantaggi di queste GPU aggiornate per carichi di lavoro HPC tradizionali, come la modellazione delle riserve, il sequenziamento del DNA, l'analisi di proteine, le simulazioni Monte Carlo e altro ancora. La configurazione NC24rs v3 offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) nella sottoscrizione è impostata inizialmente su 0 in ogni area. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Valore massimo per dischi di dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 336 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 672 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 1344 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 1344 | 4 | 32 | 8 |

1 GPU = Pari a una scheda V100.

*Con supporto di RDMA

## <a name="nd-series"></a>Serie ND

Le macchine virtuali della serie ND sono una novità della famiglia di GPU progettata per carichi di lavoro di intelligenza artificiale e Deep Learning. Offrono prestazioni ottimali per il training e l'inferenza. Le istanze ND sono basate su GPU [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Queste istanze offrono prestazioni eccellenti per le operazioni con precisione singola e virgola mobile e per carichi di lavoro di intelligenza artificiale che usano Microsoft Cognitive Toolkit, TensorFlow, Caffe e altri framework. La serie ND offre anche dimensioni di memoria di GPU significativamente superiori (24 GB), ottimali per modelli di rete neurale molto più grandi. Analogamente alla serie NC, la serie ND offre una configurazione con latenza bassa secondaria, rete con velocità effettiva elevata tramite RDMA e connettività InfiniBand, in modo che sia possibile eseguire processi di training su vasta scala per molte GPU.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) per area nella sottoscrizione è impostata inizialmente su 0. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Valore massimo per dischi di dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 336 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 672 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 1344 | 4 | 32 | 8 |
| Standard_ND24rs* |24 |1448 | 1344 | 4 | 32 | 8 |

1 GPU = Pari a una scheda P40.

*Con supporto di RDMA

## <a name="nv-series"></a>Serie NV

Le macchine virtuali delle serie NV sono basate su GPU [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e sulla tecnologia NVIDIA GRID per applicazioni con accelerazione grafica per desktop e desktop virtuali con cui i clienti possono visualizzare i propri dati o le proprie simulazioni. Gli utenti possono visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore ed eseguire anche singoli carichi di lavoro di precisione, come la codifica e il rendering. 

Ogni GPU di istanze NV viene fornita con una licenza GRID. Questa licenza consente di usare un'istanza NV come workstation virtuale per un singolo utente oppure consente a 25 utenti simultanei di connettersi a una macchina virtuale per uno scenario di applicazione virtuale.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Valore massimo per dischi di dati | Schede di interfaccia di rete max | Workstation virtuali | Applicazioni virtuali | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1.440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = Pari a metà scheda M60.

 
