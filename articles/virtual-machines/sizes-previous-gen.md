---
title: Dimensioni delle macchine virtuali di Azure-generazioni precedenti | Microsoft Docs
description: Elenca le generazioni precedenti di dimensioni disponibili per le macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/02/2020
ms.author: jushiman
ms.openlocfilehash: afad68be53637cf258223eafab0a13f525a92f4c
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666620"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Generazioni precedenti delle dimensioni delle macchine virtuali

In questa sezione vengono fornite informazioni sulle generazioni precedenti di dimensioni di macchine virtuali. Queste dimensioni possono ancora essere usate, ma sono disponibili generazioni più recenti.

## <a name="f-series"></a>Serie F

La serie F è basata sul processore Intel Xeon® E5-2673 v3 (Haswell) a 2,4 GHz che può raggiungere velocità di clock fino a 3,1 GHz con la tecnologia Intel Turbo Boost 2.0. Si tratta delle stesse prestazioni CPU della serie Dv2 di VM.  

Le VM serie F sono un'ottima scelta per i carichi di lavoro che richiedono CPU più veloci, ma che non necessitano della stessa memoria o risorsa di archiviazione temporanea per ogni vCPU.  Carichi di lavoro come server Web, analisi, giochi ed elaborazione batch trarranno vantaggio dal valore della serie F.

ACU: 210 - 250

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/Mbps di scrittura | Numero massimo di dischi dati/velocità effettiva: IOPS | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32X500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Serie Fs <sup>1</sup>

La serie Fs offre tutti i vantaggi della serie F, oltre all'archiviazione Premium.

ACU: 210 - 250

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una macchina virtuale serie Fs può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per informazioni dettagliate, vedere [progettazione per prestazioni elevate](premium-storage-performance.md).


## <a name="nvv2-series"></a>Serie NVv2

**Raccomandazione sulle dimensioni più recenti**: [serie NVv3](nvv3-series.md)

Le macchine virtuali serie NVv2 sono basate su GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e sulla tecnologia NVIDIA GRID con CPU Intel Broadwell. Queste macchine virtuali sono specifiche per applicazioni con grafica accelerata per GPU e desktop virtuali in cui i clienti vogliono visualizzare i propri dati, simulare risultati da visualizzare, lavorare in CAD o eseguire il rendering e lo streaming di contenuti. Queste macchine virtuali possono anche eseguire carichi di lavoro con precisione singola, ad esempio per la codifica e il rendering. Le macchine virtuali NVv2 supportano Archiviazione Premium e offrono una memoria di sistema (RAM) doppia rispetto alla serie NV precedente.  

Ogni GPU nelle istanze NVv2 viene fornita con una licenza GRID. Questa licenza consente di usare un'istanza NV come workstation virtuale per un singolo utente oppure consente a 25 utenti simultanei di connettersi a una macchina virtuale per uno scenario di applicazione virtuale.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max | Workstation virtuali | Applicazioni virtuali |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>Generazioni precedenti di dimensioni di macchine virtuali

Questa sezione fornisce informazioni sulle generazioni precedenti di dimensioni di macchine virtuali. Queste dimensioni sono ancora supportate ma non riceveranno capacità aggiuntiva. Sono disponibili dimensioni più recenti o alternative disponibili a livello generale. Per scegliere le dimensioni delle VM più adatte alle proprie esigenze, vedere [dimensioni delle macchine virtuali Linux in Azure](./sizes.md) .  

Per altre informazioni sul ridimensionamento di una VM Linux, vedere [ridimensionare una VM Linux](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Raccomandazione sulle dimensioni più recenti**: [serie AV2](av2-series.md)

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Le dimensioni del livello Basic sono destinate principalmente ai carichi di lavoro di sviluppo e alle altre applicazioni che non necessitano di bilanciamento del carico, scalabilità automatica o macchine virtuali con utilizzo intensivo della memoria.

| Dimensioni – Dimensioni\Nome | vCPU | Memoria|NIC (Max)| Dimensioni massime per il disco temporaneo | Max. (1023 GB ciascuno)| Max. IOPS (300 per disco) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1x300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2x300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4x300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8x300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16x300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 che usa l'interfaccia della riga di comando e PowerShell

Nel modello di distribuzione classico, alcuni nomi di dimensioni VM sono leggermente diversi in PowerShell e nell'interfaccia della riga di comando:

* Standard_A0 è ExtraSmall
* Standard_A1 è Small
* Standard_A2 è Medium
* Standard_A3 è Large
* Standard_A4 è ExtraLarge

### <a name="a-series"></a>Serie A  

**Raccomandazione sulle dimensioni più recenti**: [serie AV2](av2-series.md)

ACU: 50-100

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione | vCPU | Memoria: GiB | Spazio di archiviazione temp (HDD): GiB | Numero massimo di dischi dati | Velocità effettiva del disco di dati max: IOPS | NIC massimo/larghezza di banda di rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0,768 | 20 | 1 | 1x500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3,5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8x500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16x500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8x500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16x500 | 4/2000 |

<sup>1</sup> La sottoscrizione della dimensione A0 è eccessiva nell'hardware fisico. Solo per questa dimensione specifica, altre distribuzioni dei clienti possono compromettere le prestazioni del carico di lavoro in esecuzione. Le prestazioni relative sono indicate di seguito come linea di base prevista, con variabilità approssimativa del 15%.

<br>

### <a name="a-series---compute-intensive-instances"></a>Serie A - Istanze a elevato utilizzo di calcolo  

**Raccomandazione sulle dimensioni più recenti**: [serie AV2](av2-series.md)

ACU: 225

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Le dimensioni delle serie A8-A11 e H sono note anche come *istanze a elevato uso di calcolo*. L'hardware che esegue queste dimensioni è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, come applicazioni cluster HPC, modellazione e simulazioni. La serie A8-A11 usa Intel Xeon E5-2670 a 2,6 GHZ, mentre la serie H usa Intel Xeon E5-2667 v3 a 3,2 GHz.  

| Dimensione | vCPU | Memoria: GiB | Spazio di archiviazione temp (HDD): GiB | Numero massimo di dischi dati | Velocità effettiva del disco di dati max: IOPS | Schede di interfaccia di rete max|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup>Per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR, che offre latenza estremamente bassa e larghezza di banda elevata.  

> [!NOTE]
> Il ritiro delle macchine virtuali A8 -A11 è previsto per marzo 2021. Si consiglia vivamente di non creare nuove macchine virtuali a8-a11. Eseguire la migrazione di tutte le macchine virtuali a8-a11 esistenti a dimensioni di VM di calcolo ad alte prestazioni più recenti e potenti, ad esempio H, HB, HC, HBv2, nonché le dimensioni delle macchine virtuali di calcolo per utilizzo generico, ad esempio D, e e F, per migliorare le prestazioni dei prezzi. Per altre informazioni, vedere [Guida alla migrazione HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>Serie D  

**Raccomandazione di dimensioni più recenti**: serie [Dav4](dav4-dasv4-series.md), serie [dv4](dv4-dsv4-series.md) e [Ddv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/Mbps di scrittura | Numero massimo di dischi dati/velocità effettiva: IOPS | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3,5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32X500 | 8/4000 |

<sup>1</sup> famiglia di VM può essere eseguita in uno dei seguenti CPU: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 V3 (Haswell) o 2,3 GHz intel Xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Serie D - Con ottimizzazione per la memoria  

**Raccomandazione di dimensioni più recenti**: serie [Dav4](dav4-dasv4-series.md), serie [dv4](dv4-dsv4-series.md) e [Ddv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/Mbps di scrittura | Numero massimo di dischi dati/velocità effettiva: IOPS | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32X500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> famiglia di VM può essere eseguita in uno dei seguenti CPU: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 V3 (Haswell) o 2,3 GHz intel Xeon® E5-2673 V4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Anteprima: serie DC

**Raccomandazione sulle dimensioni più recenti**: [serie DCsv2](dcv2-series.md)

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

La serie DC USA la generazione più recente di processore Intel XEON E-2176G a 3,7 GHz con la tecnologia SGX e con la tecnologia Intel Turbo Boost può arrivare fino a 4,7 GHz. 

| Dimensione          | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Le macchine virtuali serie DC sono [macchine virtuali di seconda generazione](./linux/generation-2.md#creating-a-generation-2-vm) e supportano solo le `Gen2` Immagini.


### <a name="ds-series"></a>Serie DS  

**Raccomandazione di dimensioni più recenti**: serie [Dasv4](dav4-dasv4-series.md), serie [Dsv4](dv4-dsv4-series.md) e [Ddsv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> famiglia di VM può essere eseguita in uno dei seguenti CPU: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 V3 (Haswell) o 2,3 GHz intel Xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Serie DS - Con ottimizzazione per la memoria  

**Raccomandazione di dimensioni più recenti**: serie [Dasv4](dav4-dasv4-series.md), serie [Dsv4](dv4-dsv4-series.md) e [Ddsv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1, 2</sup>

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per informazioni dettagliate, vedere [progettazione per prestazioni elevate](premium-storage-performance.md).
<sup>2</sup> la famiglia di macchine virtuali può essere eseguita su una delle CPU seguenti: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 V3 (Haswell) o 2,3 GHz intel Xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="ls-series"></a>Serie Ls

**Raccomandazione sulle dimensioni più recenti**: [serie Lsv2](lsv2-series.md)

La serie Ls offre fino a 32 vCPU e usa il [processore Intel® Xeon® della famiglia E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). La serie Ls offre le stesse prestazioni CPU della serie G/GS ed è dotato di 8 GiB di memoria per ogni vCPU.

La serie Ls non supporta la creazione di una cache locale per aumentare il numero di operazioni di I/O al secondo che è possibile ottenere tramite i dischi dati permanenti. La velocità effettiva elevata e gli IOPS del disco locale rendono le VM serie LS ideali per gli archivi NoSQL, ad esempio Apache Cassandra e MongoDB, che replicano i dati tra più macchine virtuali per ottenere la persistenza in caso di errore di una singola macchina virtuale.

ACU: 180-240

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione | vCPU | Memoria (GiB) | Spazio di archiviazione temp (GiB) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea (IOPS/MBps) | Numero massimo di velocità effettiva del disco non memorizzato nella cache (IOPS/MBps) | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

La massima velocità effettiva del disco possibile con le macchine virtuali serie Ls può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per informazioni dettagliate, vedere [progettazione per prestazioni elevate](premium-storage-performance.md).

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

### <a name="gs-series"></a>Serie GS

**Raccomandazione di dimensioni più recenti**: serie [Easv4](eav4-easv4-series.md), serie [Esv4](ev4-esv4-series.md), serie [Edsv4](edv4-edsv4-series.md) e [serie M](m-series.md)

ACU: 180 - 240 <sup>1</sup>

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie GS può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per informazioni dettagliate, vedere [progettazione per prestazioni elevate](premium-storage-performance.md).

<sup>2</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

<sup>3</sup> Disponibili dimensioni core vincolate.

<br>

### <a name="g-series"></a>Serie G

**Raccomandazione di dimensioni più recenti**: serie [Eav4](eav4-easv4-series.md), serie [Ev4](ev4-esv4-series.md) e [Edv4](edv4-edsv4-series.md) e serie [M](m-series.md)

ACU: 180 - 240

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/Mbps di scrittura | Numero massimo di dischi dati/velocità effettiva: IOPS | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32X500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.
<br>

## <a name="nv-series"></a>Serie NV
**Raccomandazione sulle dimensioni più recenti**: serie [NVv3](nvv3-series.md) e [serie NVv4](nvv4-series.md)

Le macchine virtuali delle serie NV sono basate su GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e sulla tecnologia NVIDIA GRID per applicazioni con accelerazione grafica per desktop e desktop virtuali con cui i clienti possono visualizzare i propri dati o le proprie simulazioni. Gli utenti possono visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore ed eseguire anche singoli carichi di lavoro di precisione, come la codifica e il rendering. Le macchine virtuali serie NV sono basate anche su CPU Intel Xeon E5-2690 V3 (Haswell).

Ogni GPU di istanze NV viene fornita con una licenza GRID. Questa licenza consente di usare un'istanza NV come workstation virtuale per un singolo utente oppure consente a 25 utenti simultanei di connettersi a una macchina virtuale per uno scenario di applicazione virtuale.

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Live Migration: Non supportato

Manutenzione con mantenimento della memoria: Non supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max | Workstation virtuali | Applicazioni virtuali |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = Pari a metà scheda M60.
<br>

## <a name="other-sizes"></a>Altre dimensioni

* [Utilizzo generico](sizes-general.md)
* [Ottimizzate per il calcolo](sizes-compute.md)
* [Ottimizzate per la memoria](sizes-memory.md)
* [Ottimizzate per l'archiviazione](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [High Performance Computing (HPC)](sizes-hpc.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
