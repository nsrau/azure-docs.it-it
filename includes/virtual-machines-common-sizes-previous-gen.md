---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 07/06/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 937ac1d892cdea849d7e5a89c8e69c6b069f2efd
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56334009"
---
Questo articolo fornisce informazioni sulle generazioni precedenti delle dimensioni delle macchine virtuali. Queste dimensioni possono ancora essere usate, ma sono disponibili generazioni più recenti.


## <a name="ds-series"></a>Serie DS

ACU: 160

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4.000/32 (43) |3.200/32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8.000/64 (86) |6.400/64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16.000/128 (172) |12.800/128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32.000/256 (344) |25.600/256 |8 / 4000 |

<br>

## <a name="ds-series---memory-optimized"></a>Serie DS - Con ottimizzazione per la memoria

ACU: 160 <sup>1</sup>

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8.000/64 (72) |6.400/64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16.000/128 (144) |12.800/128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32.000/256 (288) |25.600/256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64.000/512 (576) |51.200/512 |8 / 8000 |

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).



## <a name="d-series"></a>Serie D 

ACU: 160

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione         | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4/4 x 500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 4000                     |

<br>

## <a name="d-series---memory-optimized"></a>Serie D - Con ottimizzazione per la memoria

ACU: 160

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione         | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 8000                |

<br>

## <a name="a-series---compute-intensive-instances"></a>Serie A - Istanze a elevato utilizzo di calcolo

ACU: 225

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Le dimensioni delle serie A8-A11 e H sono note anche come *istanze a elevato uso di calcolo*. L'hardware che esegue queste dimensioni è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, come applicazioni cluster HPC, modellazione e simulazioni. La serie A8-A11 usa Intel Xeon E5-2670 a 2,6 GHZ, mentre la serie H usa Intel Xeon E5-2667 v3 a 3,2 GHz.  Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento. 

| Dimensione | vCPU | Memoria: GiB | Spazio di archiviazione temp (HDD): GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco dati: IOPS | Schede di interfaccia di rete max|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>Per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR, che offre latenza estremamente bassa e larghezza di banda elevata.

<br>

## <a name="a-series"></a>Serie A

ACU: 50-100

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione | vCPU | Memoria: GiB | Spazio di archiviazione temp (HDD): GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco dati: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 <sup>1</sup> |1 |0,768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |
<br>

<sup>1</sup> La sottoscrizione della dimensione A0 è eccessiva nell'hardware fisico. Solo per questa dimensione specifica, altre distribuzioni dei clienti possono compromettere le prestazioni del carico di lavoro in esecuzione. Le prestazioni relative sono indicate di seguito come linea di base prevista, con variabilità approssimativa del 15%.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 che usa l'interfaccia della riga di comando e PowerShell

Nel modello di distribuzione classico, alcuni nomi di dimensioni VM sono leggermente diversi in PowerShell e nell'interfaccia della riga di comando:

* Standard_A0 è ExtraSmall 
* Standard_A1 è Small
* Standard_A2 è Medium
* Standard_A3 è Large
* Standard_A4 è ExtraLarge

## <a name="basic-a"></a>Basic A

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Le dimensioni del livello Basic sono destinate principalmente ai carichi di lavoro di sviluppo e alle altre applicazioni che non necessitano di bilanciamento del carico, scalabilità automatica o macchine virtuali con utilizzo intensivo della memoria.

|Dimensioni – Dimensioni\Nome | vCPU |Memoria|NIC (Max)|Dimensioni massime per il disco temporaneo |Max. (1023 GB ciascuno)|Max. IOPS (300 per disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|
 


