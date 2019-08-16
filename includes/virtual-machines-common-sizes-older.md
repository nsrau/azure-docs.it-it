---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: a4746a945f1a89c34308a3bd968f6341e0e25ac5
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541489"
---
Questa sezione fornisce informazioni sulle generazioni precedenti di dimensioni di macchine virtuali. Queste dimensioni sono ancora supportate ma non riceveranno capacità aggiuntiva. Sono disponibili dimensioni più recenti o alternative disponibili a livello generale. Vedere [dimensioni per le macchine virtuali Windows in Azure](../articles/virtual-machines/windows/sizes.md) o [dimensioni per le macchine virtuali Linux in Azure](../articles/virtual-machines/linux/sizes.md) per scegliere le dimensioni delle VM più adatte alle proprie esigenze.  

Per altre informazioni sul ridimensionamento di una VM Linux, vedere [ridimensionare una macchina virtuale Linux con l'interfaccia](../articles/virtual-machines/linux/change-vm-size.md)della riga di comando di Azure. Se si usano macchine virtuali Windows e si preferisce usare PowerShell, vedere [ridimensionare una VM Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Raccomandazione di dimensioni più recenti**: [Serie Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

Le dimensioni del livello Basic sono destinate principalmente ai carichi di lavoro di sviluppo e alle altre applicazioni che non necessitano di bilanciamento del carico, scalabilità automatica o macchine virtuali con utilizzo intensivo della memoria.

|Dimensioni – Dimensioni\Nome | CPU virtuale |Memoria|NIC (Max)|Dimensioni massime per il disco temporaneo |Max. (1023 GB ciascuno)|Max. IOPS (300 per disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 che usa l'interfaccia della riga di comando e PowerShell

Nel modello di distribuzione classico, alcuni nomi di dimensioni VM sono leggermente diversi in PowerShell e nell'interfaccia della riga di comando:

* Standard_A0 è ExtraSmall
* Standard_A1 è Small
* Standard_A2 è Medium
* Standard_A3 è Large
* Standard_A4 è ExtraLarge

### <a name="a-series"></a>Serie A  

**Raccomandazione di dimensioni più recenti**: [Serie Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

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

**Raccomandazione di dimensioni più recenti**: [Serie Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

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

**Raccomandazione di dimensioni più recenti**: [Serie dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

| Dimensione         | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4/4 x 500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 4000                     |

<sup>1</sup> famiglia di macchine virtuali può essere eseguita su una delle seguenti CPU: Intel Xeon 2,2 GHz® E5-2660 V2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) o 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Serie D - Con ottimizzazione per la memoria  

**Raccomandazione di dimensioni più recenti**: [Serie dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

| Dimensione         | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 8000                |

<sup>1</sup> famiglia di macchine virtuali può essere eseguita su una delle seguenti CPU: Intel Xeon 2,2 GHz® E5-2660 V2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) o 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell)  

<br>

### <a name="ds-series"></a>Serie DS  

**Raccomandazione di dimensioni più recenti**: [Serie DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4\.000/32 (43) |3\.200/32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8\.000/64 (86) |6\.400/64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16.000/128 (172) |12.800/128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32.000/256 (344) |25.600/256 |8 / 4000 |

<sup>1</sup> famiglia di macchine virtuali può essere eseguita su una delle seguenti CPU: Intel Xeon 2,2 GHz® E5-2660 V2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) o 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Serie DS - Con ottimizzazione per la memoria  

**Raccomandazione di dimensioni più recenti**: [Serie DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1,2</sup>

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8\.000/64 (72) |6\.400/64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16.000/128 (144) |12.800/128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32.000/256 (288) |25.600/256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64.000/512 (576) |51.200/512 |8 / 8000 |

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> la famiglia di macchine virtuali può essere eseguita su una delle seguenti CPU: Intel Xeon 2,2 GHz® E5-2660 V2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) o 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell)  

<br>

### <a name="ls-series"></a>Serie Ls

La serie Ls offre fino a 32 vCPU e usa il [processore Intel® Xeon® della famiglia E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). La serie Ls offre le stesse prestazioni CPU della serie G/GS ed è dotato di 8 GiB di memoria per ogni vCPU.

La serie Ls non supporta la creazione di una cache locale per aumentare il numero di operazioni di I/O al secondo che è possibile ottenere tramite i dischi dati permanenti. La velocità effettiva elevata e gli IOPS del disco locale rendono le VM serie LS ideali per gli archivi NoSQL, ad esempio Apache Cassandra e MongoDB, che replicano i dati tra più macchine virtuali per ottenere la persistenza in caso di errore di una singola macchina virtuale.

ACU: 180-240

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Non supportata
 
| Dimensione          | vCPU | Memoria (GiB) | Spazio di archiviazione temp (GiB) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea (IOPS/Mbps) | Velocità effettiva massima del disco non memorizzato nella cache (IOPS/MBps) | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8 / 20000 | 

La massima velocità effettiva del disco possibile con le macchine virtuali serie Ls può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

### <a name="gs-series"></a>Serie GS 

ACU: 180 - 240 <sup>1</sup>

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10.000/100 (264) |5\.000/125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20.000/200 (528) |10.000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40.000/400 (1.056) |20.000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80.000/800 (2,112) |40.000 /1.000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160.000/1.600 (4.224) |80.000/2.000 |8 / 20000 |

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie GS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

<sup>3</sup> Disponibili dimensioni core vincolate.

<br>

### <a name="g-series"></a>Serie G

ACU: 180 - 240

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

| Dimensione         | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8/8 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16/16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1\.536          | 24000 / 375 / 187                                        | 32/32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3\.072          | 48000 / 750 / 375                                        | 64/64 x 500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64/64 x 500                     | 8 / 20000           |

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.
<br>
