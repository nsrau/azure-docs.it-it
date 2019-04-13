---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: cd06326b22b227490798b2b89c0439940cb4575f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551534"
---
<!-- F-series, Fs-series* -->

Le dimensioni delle macchine virtuali ottimizzate per il calcolo hanno un rapporto elevato tra CPU e memoria e sono ottimali per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server applicazioni. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento.

La serie Fsv2 è basata sul processore Intel® Xeon® Platinum 8168, con una velocità di clock Turbo all-core sostenuta di 3,4 GHz e una frequenza turbo massima single-core di 3,7 GHz. Le istruzioni di Intel® AVX-512, che rappresentano una novità per i processori Intel Scalable, offriranno fino al doppio delle prestazioni per carichi di lavoro di elaborazione vettoriale nelle operazioni a virgola mobile a precisione singola e doppia. In altre parole, sono molto veloci per qualsiasi carico di lavoro di calcolo. 

Con un prezzo di listino orario più basso, la serie Fsv2 presenta il migliore rapporto prezzo-prestazioni nel portfolio Azure basato sull'unità di calcolo di Azure (ACU, Azure Compute Unit) per ogni vCPU.

## <a name="fsv2-series-sup1sup"></a>Serie Fsv2 <sup>1</sup>

ACU: 195 - 210

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

| Dimensione             | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31 (32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63 (64)           | 6400/95                | 2 / 1.750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000/127 (128)        | 12800/190              | 4 / 3.500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255 (256)        | 25600/380              | 4 / 7.000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512 (512)        | 51200/750              | 8 / 14.000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000/1024 (1024)     | 80000/1100             | 8 / 28.000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000/1152 (1520)     | 80000/1100             | 8 / 30000              |


<sup>1</sup> Le macchine virtuali Serie Fsv2 integrano la tecnologia Intel® Hyper-Threading

<sup>2</sup> Data la presenza di più di 64 vCPU, è necessario uno dei sistemi operativi guest supportati seguenti: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7.3 o Oracle Linux 7.3 con LIS 4.2.1

<sup>3</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.