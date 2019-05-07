---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: f3c1a946afb7f51a1c7543dabae7f2f42ff466be
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199180"
---
Macchine virtuali di Azure serie H (VM) sono progettate per offrire prestazioni della classe di leadership, scalabilità MPI e convenienza per un'ampia gamma di carichi di lavoro HPC reali.

Le VM della serie HB sono ottimizzate per applicazioni basate sulla larghezza di banda della memoria, ad esempio applicazioni per fluidodinamica, analisi degli elementi espliciti limitati e modellazione di dati meteorologici. Le VM HB offrono 60 core del processore AMD EPYC 7551, 4 GB di RAM per core CPU e nessun hyperthreading. La piattaforma AMD EPYC fornisce più di 260 GB/sec di larghezza di banda della memoria.

Macchine virtuali della serie HC sono ottimizzate per le applicazioni dai calcoli ad alta densità, ad esempio analisi degli elementi limitati implicita, molecolare e anche gli equilibri interni calcolo. Le VM HC offrono 44 core del processore Intel Xeon Platinum 8168, 8 GB di RAM per core CPU e nessun hyperthreading. La piattaforma Intel Xeon Platinum supporta ecosistema avanzato di Intel di strumenti software, ad esempio Intel Math Kernel Library.

100 Gb al secondo delle funzionalità HB sia alle macchine virtuali HC Mellanox EDR InfiniBand in una file System fat non bloccante della struttura ad albero di configurazione per ottenere prestazioni coerenti RDMA. Macchine virtuali di connessione ibrida e HB supportano i driver Mellanox/OFED standard in modo che tutti i tipi di MPI e le versioni, nonché verbi RDMA, sono supportati anche.

Le macchine virtuali serie H sono ottimizzate per applicazioni da elevata delle frequenze della CPU o memoria di grandi dimensioni per requisiti di base. Serie H VM funzionalità 8 o 16 2667 di Intel Xeon E5 v3 processore core core, 7 o 14 GB di RAM per core della CPU e non l'hyperthreading. Serie H offre 56 Gb/sec Mellanox FDR InfiniBand in una file System fat non bloccante della struttura ad albero di configurazione per ottenere prestazioni coerenti RDMA. Le macchine virtuali serie H supportano Intel MPI 5.x e MS-MPI.

## <a name="hb-series"></a>Serie HB

Archiviazione Premium: Archiviazione Premium è supportata la memorizzazione nella cache: Supportato

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria GB/s | Frequenza della CPU di base (GHz) | Frequenza di all-core (GHz, picco) | Frequenza single core (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto di MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | Interfacce di rete Ethernet max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Tutti | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Serie HC

Archiviazione Premium: Archiviazione Premium è supportata la memorizzazione nella cache: Supportato


| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria GB/s | Frequenza della CPU di base (GHz) | Frequenza di all-core (GHz, picco) | Frequenza single core (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto di MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | Interfacce di rete Ethernet max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3,7 | 100 | Tutti | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Serie H

ACU: 290-300

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria GB/s | Frequenza della CPU di base (GHz) | Frequenza di all-core (GHz, picco) | Frequenza single core (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto di MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | Interfacce di rete Ethernet max |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 v3 di 2667 | 56 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 v3 di 2667 | 112 | 80 | 3.2 | 3.3 | 3.6 |  56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 v3 di 2667 | 112 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 v3 di 2667 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 v3 di 2667 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 v3 di 2667 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> Per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR, che offre latenza estremamente bassa e larghezza di banda elevata.

<br>
