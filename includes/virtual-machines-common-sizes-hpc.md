---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 16e2a9cfbd9f08428fddade290117b27bc3401f7
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44369143"
---
Le macchine virtuali di Azure serie H sono le più recenti macchine virtuali High Performance Computing allo scopo di gestire carichi di lavoro, ad esempio elaborazione batch, processi di analisi, modellazione molecolare e fluidodinamica. Queste VM a 8 e 16 vCPU sono basate sulla tecnologia del processore Intel Haswell E5-2667 V3 con memoria DDR4 e spazio di archiviazione temporaneo basato su unità SSD. 

Oltre alla sostanziale potenza della CPU, la serie H offre diverse opzioni per rete RDMA a bassa latenza con FDR InfiniBand e diverse configurazioni di memoria a supporto di requisiti di calcolo a elevato uso di memoria.



## <a name="h-series"></a>Serie H

ACU: 290-300

Archiviazione Premium: non supportata

Memorizzazione nella cache Archiviazione Premium: non supportata

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Valore massimo per dischi di dati | Velocità effettiva del disco max: IOPS | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32x500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32x500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> Per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR, che offre latenza estremamente bassa e larghezza di banda elevata.

<br>






