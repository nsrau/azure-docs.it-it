---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 489ac7fa37c10a27de971151f0be35c647d2186f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116677"
---
Le macchine virtuali ottimizzate per HPC di Azure sono progettate per offrire prestazioni di classe leadership, scalabilità MPI ed efficienza dei costi per un'ampia gamma di applicazioni reali.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>Rete InfiniBand per HPC su larga scala
Funzionalità VM HBv2 200 GB/sec Mellanox HDR InfiniBand, mentre le VM HB e HC includono 100 GB/sec Mellanox EDR InfiniBand. Ognuno di questi tipi di VM è connesso in un albero Fat senza blocco per prestazioni RDMA ottimizzate e coerenti.

Le macchine virtuali HBv2 supportano il routing adattivo e il trasporto con connessione dinamica (DCT, in aggiunta a trasporti standard RC e UD). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e l'utilizzo di tali funzionalità è fortemente consigliato.  

Le VM HBv2, HB e HC supportano i driver Mellanox/OFED standard. Di conseguenza, sono supportati tutti i verbi RDMA e i tipi MPI. Ognuno di questi tipi di VM supporta inoltre l'offload basato su hardware dei collettivi MPI per migliorare le prestazioni dell'applicazione.
 
Funzionalità VM serie H originali 56 GB/sec Mellanox FDR InfiniBand. Per sfruttare l'interfaccia InfiniBand sulla serie H, i clienti devono eseguire la distribuzione usando immagini supportate ufficialmente specifiche per questo tipo di macchina virtuale da Azure Marketplace. 


## <a name="hbv2-series"></a>Serie HBv2
Le macchine virtuali della serie HBv2 sono ottimizzate per le applicazioni basate sulla larghezza di banda della memoria, ad esempio fluidodinamica, analisi degli elementi finiti e simulazione del serbatoio. Macchine virtuali HBv2 funzionalità 120 processori AMD EPYC 7742 core, 4 GB di RAM per core CPU e nessun multithreading simultaneo. Ogni macchina virtuale HBv2 offre fino a 340 GB al secondo di larghezza di banda di memoria e fino a 4 teraflop di calcolo FP64. 

Archiviazione Premium: supportata

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Archiviazione temporanea (GB) | Valore massimo per dischi di dati | NIC Ethernet max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | Tutti | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>Serie HB
Le VM della serie HB sono ottimizzate per applicazioni basate sulla larghezza di banda della memoria, ad esempio applicazioni per fluidodinamica, analisi degli elementi espliciti limitati e modellazione di dati meteorologici. Macchine virtuali HB funzionalità 60 processori AMD EPYC 7551 core, 4 GB di RAM per core CPU e nessun multithreading simultaneo. Una macchina virtuale HB fornisce fino a 260 GB al secondo di larghezza di banda di memoria.  

ACU: 199-216

Archiviazione Premium: supportata

Memorizzazione nella cache Archiviazione Premium: supportata

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Archiviazione temporanea (GB) | Valore massimo per dischi di dati | NIC Ethernet max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Tutti | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Serie HC
Le macchine virtuali della serie HC sono ottimizzate per le applicazioni basate su un calcolo denso, ad esempio l'analisi degli elementi limitati implicita, le dinamiche molecolari e la chimica computazionale. Le VM HC offrono 44 core del processore Intel Xeon Platinum 8168, 8 GB di RAM per core CPU e nessun hyperthreading. La piattaforma Intel Xeon Platinum supporta l'ampio ecosistema di strumenti software di Intel, ad esempio la libreria del kernel matematico di Intel. 

ACU: 297-315

Archiviazione Premium: supportata

Memorizzazione nella cache Archiviazione Premium: supportata


| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Archiviazione temporanea (GB) | Valore massimo per dischi di dati | NIC Ethernet max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3,7 | 100 | Tutti | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Serie H
Le macchine virtuali serie H sono ottimizzate per le applicazioni basate su frequenze di CPU elevate o per i requisiti di memoria di grandi dimensioni. Le macchine virtuali serie H dispongono di core del processore Intel Xeon E5 2667 V3, fino a 14 GB di RAM per core CPU e senza hyperthreading. Una macchina virtuale della serie H offre fino a 80 GB al secondo di larghezza di banda di memoria.

ACU: 290-300

Archiviazione Premium: non supportata

Memorizzazione nella cache Archiviazione Premium: non supportata

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Archiviazione temporanea (GB) | Valore massimo per dischi di dati | NIC Ethernet max |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 V3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 V3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR.

<br>
