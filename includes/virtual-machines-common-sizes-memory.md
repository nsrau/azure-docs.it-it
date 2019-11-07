---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: da47a471ddcf2c083ed127b79e4d3da9713d2ed4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719069"
---
Le dimensioni delle macchine virtuali con ottimizzazione per la memoria offrono un rapporto memoria-CPU elevato, ideale per server di database relazionali, cache di medie e grandi dimensioni e analisi in memoria. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento.

* La serie EV3 include i processori Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) in una configurazione con Hyper-Threading, fornendo una proposta di valore migliore per la maggior parte dei carichi di lavoro per uso generico e portando i EV3 in allineamento con le VM per utilizzo generico della maggior parte degli altri cloud.  La memoria è stata estesa (da 7 GiB/vCPU a 8 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading.  La serie Ev3 rappresenta un passo avanti rispetto alle dimensioni delle macchine virtuali con memoria elevata delle famiglie D/Dv2.

* Le serie Eav4 e Easv4 usano il processore AMD 2.35 GHz EPYC<sup>TM</sup> 7452 in una configurazione multithread con una cache L3 fino a 256 MB, aumentando le opzioni per l'esecuzione della maggior parte dei carichi di lavoro con ottimizzazione per la memoria.  La serie Eav4 e la serie Easv4 hanno le stesse configurazioni di memoria e disco della serie EV3 & Esv3.

* La serie Mv2 offre il numero massimo di vCPU (fino a 416 vCPU) e la memoria massima (fino a 8,19 TiB) di qualsiasi macchina virtuale nel cloud. È ideale per database molto grandi o altre applicazioni che traggono vantaggio da un elevato numero di vCPU e una grande quantità di memoria.

* La serie M offre un numero elevato di vCPU (fino a 128 vCPU) e una grande quantità di memoria (fino a 3,8 TiB). È ideale anche per database di dimensioni molto grandi o altre applicazioni che traggono vantaggio da conteggi vCPU elevati e grandi quantità di memoria.

* Le controparti serie dv2, G e DSv2/GS sono ideali per le applicazioni che richiedono un vCPU più veloce, prestazioni di archiviazione temporanee migliori o richieste di memoria più elevate. Offrono una potente combinazione per molte applicazioni di livello aziendale.

* Serie Dv2, una versione successiva della serie D originale, dotata di una CPU più potente. La serie dv2 è più veloce del 35% rispetto alla serie D. Viene eseguito sui processori Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) e con la tecnologia Intel Turbo Boost 2,0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

* Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente.  Queste dimensioni delle macchine virtuali sono particolarmente adatte ai carichi di lavoro che richiedono un elevato livello di isolamento dagli altri clienti, per i carichi di lavoro con aspetti come i requisiti normativi e di conformità.  I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Per le opzioni di VM con piano Isolato vedere le tabelle delle famiglie di macchine virtuali di seguito.

## <a name="esv3-series"></a>Serie Esv3

ACU: 160-190 <sup>1</sup>

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Le istanze della serie ESv3 includono i processori Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) e possono raggiungere 3,5 GHz con la tecnologia Intel Turbo Boost 2,0 e usare archiviazione Premium. Le istanze della serie Ev3 sono ideali per applicazioni aziendali a uso intensivo di memoria.


| Dimensioni             | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800/1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> Le macchine virtuali Serie Esv3 integrano la tecnologia Intel® Hyper-Threading.

<sup>2</sup> Disponibili dimensioni core vincolate.

<sup>3</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

## <a name="easv4-series"></a>Serie Easv4

ACU: 230-260

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Le dimensioni della serie Easv4 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere una frequenza massima con boosting di 3.35 GHz e usare l'unità SSD Premium. Le dimensioni della serie Easv4 sono ideali per applicazioni aziendali a uso intensivo di memoria.

| Dimensioni | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | NIC massimo/larghezza di banda di rete prevista (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 / 16000 |
| <sup>**</sup> Standard_E48as_v4 |48|384|768|32|  | | 
| <sup>**</sup> Standard_E64as_v4 |64|512|1024|32| | | 
| <sup>**</sup> Standard_E96as_v4 |96|672|1344|32| | |  

<sup>**</sup>  Queste dimensioni sono disponibili in anteprima. Se si è interessati a provare queste dimensioni più grandi, iscriversi all' [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="ev3-series"></a>Serie Ev3 

ACU: 160 - 190 <sup>1</sup>

Archiviazione Premium: non supportata

Caching archiviazione Premium: non supportato

Le istanze della serie EV3 includono i processori Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) e possono raggiungere 3,5 GHz con la tecnologia Intel Turbo Boost 2,0. Le istanze della serie Ev3 sono ideali per applicazioni aziendali a uso intensivo di memoria.

L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare dischi di archiviazione Premium, usare le dimensioni ESv3. I prezzi e i contatori di fatturazione per le dimensioni ESv3 sono uguali a quelli della serie Ev3. 


| Dimensioni            | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Larghezza di banda della rete/scheda NIC max |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Le macchine virtuali serie Ev3 integrano la tecnologia Intel® Hyper-Threading.

<sup>2</sup> Disponibili dimensioni core vincolate.

<sup>3</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

## <a name="eav4-series"></a>Serie Eav4

ACU: 230-260

Archiviazione Premium: non supportata

Caching archiviazione Premium: non supportato

Le dimensioni della serie Eav4 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere una frequenza massima con boosting di 3.35 GHz e usare l'unità SSD Premium. Le dimensioni della serie Eav4 sono ideali per applicazioni aziendali a uso intensivo di memoria. L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare l'unità SSD Premium, usare le dimensioni della serie Easv4. I prezzi e i contatori di fatturazione per le dimensioni Easv4 sono uguali a quelli della serie Eav3.

| Dimensioni | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | NIC massimo/larghezza di banda di rete prevista (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| \_standard E2a\_V4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| \_standard E4a\_V4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| \_standard E8a\_V4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| \_standard E16a\_V4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| \_standard E20a\_V4|20|160|500|32|30000/468/234|8 / 10000 |
| \_standard E32a\_V4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| \_standard E48a\_V4 <sup>**</sup> |48|384|1200|32| | |
| \_standard E64a\_V4 <sup>**</sup> |64|512|1600|32| | |
| \_standard E96a\_V4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  Queste dimensioni sono disponibili in anteprima.  Se si è interessati a provare queste dimensioni più grandi, iscriversi all' [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="mv2-series"></a>Serie Mv2

ACU: 188-280<sup>1</sup>

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Acceleratore di scrittura: [supportato](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

La serie Mv2 offre una piattaforma a bassa latenza e velocità effettiva elevata in esecuzione su un processore Intel® Xeon® Platinum 8180M a 2,5 GHz (Skylake) con una frequenza di base di tutti i core di 2,5 GHz e una frequenza massima turbo di 3,8 GHz. Tutte le dimensioni delle macchine virtuali della serie Mv2 possono usare dischi permanenti standard e Premium. Le istanze della serie Mv2 sono dimensioni di VM con ottimizzazione per la memoria che offrono prestazioni di calcolo ineguagliabili per supportare database e carichi di lavoro in memoria di grandi dimensioni, con un rapporto elevato tra memoria e CPU, ideale per server di database relazionali, cache di grandi dimensioni e in memoria analisi.

|Dimensioni | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>2, 3</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8/32000 |
| Standard_M416s_v2<sup>2, 3</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8/32000 |

<sup>1</sup> funzionalità della VM della serie Mv2 Intel® tecnologia Hyper-Threading

<sup>2</sup> le macchine virtuali della serie Mv2 sono solo di generazione 2. Se si usa Linux, per istruzioni su come trovare e selezionare un'immagine, vedere [supporto per le macchine virtuali di seconda generazione in Azure](../articles/virtual-machines/linux/generation-2.md) .

<sup>3</sup> per le dimensioni M416ms_v2 e M416s_v2, si noti che è disponibile il supporto iniziale solo per l'immagine seguente: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 per le applicazioni SAP".

## <a name="m-series"></a>Serie M 

ACU: 160-180 <sup>1</sup>

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Le dimensioni della serie M sono basate su Intel (R) Xeon (R) CPU E7-8890 V3 a 2.50 GHz   

Acceleratore di scrittura: [supportato](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Dimensioni            | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000/125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000/800 (6348) | 40000/1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8/32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8/32000 |



<sup>1</sup> Le macchine virtuali serie M integrano la tecnologia Intel® Hyper-Threading

<sup>2</sup> più di 64 vCPU richiedono uno di questi sistemi operativi guest supportati: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7,3 o Oracle Linux 7,3 con LIS 4.2.1.

<sup>3</sup> Disponibili dimensioni core vincolate.

<sup>4</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-series 11-15

ACU: 210 - 250 <sup>1</sup>

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Le dimensioni della serie DSv2 vengono eseguite su Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell).

| Dimensioni | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600/384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DSv2 può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.  
<sup>3</sup> Disponibili dimensioni core vincolate.  
<sup>4</sup> 25000 Mbps con rete accelerata. 

<br>

## <a name="dv2-series-11-15"></a>Dv2-series 11-15

ACU: 210 - 250

Archiviazione Premium: non supportata

Caching archiviazione Premium: non supportato

Le dimensioni della serie DSv2 vengono eseguite su Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell).

| Dimensioni              | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64/64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.  
<sup>2</sup> 25000 Mbps con rete accelerata. 
