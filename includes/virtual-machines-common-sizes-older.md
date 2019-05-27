---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7196a2ea794c1d17a2c55c05accb447d83929972
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145702"
---
Questa sezione vengono fornite informazioni sulle versioni precedenti di dimensioni delle macchine virtuali. Queste dimensioni sono ancora supportate ma non riceveranno capacità aggiuntiva. Sono disponibili più recenti o alternative dimensioni disponibili a livello generale. Consultare [macchine virtuali di dimensioni per Windows in Azure](../articles/virtual-machines/windows/sizes.md) oppure [dimensioni delle macchine virtuali Linux in Azure](../articles/virtual-machines/linux/sizes.md) per scegliere la macchina virtuale di dimensioni maggiormente esigenze.  

Per altre informazioni sul ridimensionamento di una VM Linux, vedere [ridimensionare una macchina virtuale Linux usando Azure CLI](../articles/virtual-machines/linux/change-vm-size.md). Se si usano macchine virtuali Windows e si preferisce usare PowerShell, vedere [ridimensionare una VM Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Indicazioni sulle dimensioni più recente**: [Serie Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Archiviazione Premium:  Non supportata

Memorizzazione nella cache archiviazione Premium:  Non supportata

Le dimensioni del livello Basic sono destinate principalmente ai carichi di lavoro di sviluppo e alle altre applicazioni che non necessitano di bilanciamento del carico, scalabilità automatica o macchine virtuali con utilizzo intensivo della memoria.

|Dimensioni – Dimensioni\Nome | CPU virtuale |Memoria|NIC (Max)|Dimensioni massime per il disco temporaneo |Max. (1023 GB ciascuno)|Max. IOPS (300 per disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="a-series"></a>Serie A  

**Indicazioni sulle dimensioni più recente**: [Serie Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Archiviazione Premium:  Non supportata

Memorizzazione nella cache archiviazione Premium:  Non supportata

| Dimensione | CPU virtuale | Memoria: GiB | Spazio di archiviazione temp (HDD): GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> La sottoscrizione della dimensione A0 è eccessiva nell'hardware fisico. Solo per questa dimensione specifica, altre distribuzioni dei clienti possono compromettere le prestazioni del carico di lavoro in esecuzione. Le prestazioni relative sono indicate di seguito come linea di base prevista, con variabilità approssimativa del 15%.

<br>

### <a name="a-series---compute-intensive-instances"></a>Serie A - Istanze a elevato utilizzo di calcolo  

**Indicazioni sulle dimensioni più recente**: [Serie Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Archiviazione Premium:  Non supportata

Memorizzazione nella cache archiviazione Premium:  Non supportata

Le dimensioni delle serie A8-A11 e H sono note anche come *istanze a elevato uso di calcolo*. L'hardware che esegue queste dimensioni è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, come applicazioni cluster HPC, modellazione e simulazioni. La serie A8-A11 usa Intel Xeon E5-2670 a 2,6 GHZ, mentre la serie H usa Intel Xeon E5-2667 v3 a 3,2 GHz.  

| Dimensione | CPU virtuale | Memoria: GiB | Spazio di archiviazione temp (HDD): GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>Per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR, che offre latenza estremamente bassa e larghezza di banda elevata.  

<br>

### <a name="d-series"></a>Serie D  

**Indicazioni sulle dimensioni più recente**: [Serie Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Non supportata

Memorizzazione nella cache archiviazione Premium:  Non supportata

| Dimensione         | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4/4 x 500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 4000                     |

<sup>1</sup> famiglia di macchine Virtuali eseguibili in una delle seguenti della CPU: 2,2 Intel GHz Xeon® E5 2660 v2, Intel a 2,4 GHz Xeon®-2673 E5 v3 (Haswell) a 2,3 GHz Intel XEON® o E5 2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Serie D - Con ottimizzazione per la memoria  

**Indicazioni sulle dimensioni più recente**: [Serie Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Non supportata

Memorizzazione nella cache archiviazione Premium:  Non supportata

| Dimensione         | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 8000                |

<sup>1</sup> famiglia di macchine Virtuali eseguibili in una delle seguenti della CPU: 2,2 Intel GHz Xeon® E5 2660 v2, Intel a 2,4 GHz Xeon®-2673 E5 v3 (Haswell) a 2,3 GHz Intel XEON® o E5 2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>Serie DS  

**Indicazioni sulle dimensioni più recente**: [Serie DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Supportato

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4.000/32 (43) |3.200/32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8.000/64 (86) |6.400/64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16.000/128 (172) |12.800/128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32.000/256 (344) |25.600/256 |8 / 4000 |

<sup>1</sup> famiglia di macchine Virtuali eseguibili in una delle seguenti della CPU: 2,2 Intel GHz Xeon® E5 2660 v2, Intel a 2,4 GHz Xeon®-2673 E5 v3 (Haswell) a 2,3 GHz Intel XEON® o E5 2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Serie DS - Con ottimizzazione per la memoria  

**Indicazioni sulle dimensioni più recente**: [Serie DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1,2</sup>

Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Supportato

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8.000/64 (72) |6.400/64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16.000/128 (144) |12.800/128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32.000/256 (288) |25.600/256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64.000/512 (576) |51.200/512 |8 / 8000 |

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> famiglia di macchine Virtuali eseguibili in una delle seguenti della CPU: 2,2 Intel GHz Xeon® E5 2660 v2, Intel a 2,4 GHz Xeon®-2673 E5 v3 (Haswell) a 2,3 GHz Intel XEON® o E5 2673 v4 (Broadwell)  

<br>
