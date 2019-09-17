---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: dce7cc2cd8e97eef81023eb803cace3f6d011171
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "70174631"
---
Le dimensioni delle macchine virtuali con ottimizzazione per la memoria offrono un rapporto memoria-CPU elevato, ideale per server di database relazionali, cache di medie e grandi dimensioni e analisi in memoria. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento.

* La serie Ev3 include il processore E5-2673 v4 a 2,3 GHz (Broadwell) in una configurazione con hyperthreading, assicurando una proposta di valore ottimizzata per la maggior parte dei carichi di lavoro per uso generico e garantendo l'allineamento della serie Ev3 alle macchine virtuali per uso generico della maggior parte degli altri cloud.  La memoria è stata estesa (da 7 GiB/vCPU a 8 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading.  La serie Ev3 rappresenta un passo avanti rispetto alle dimensioni delle macchine virtuali con memoria elevata delle famiglie D/Dv2.

* Le serie Eav3 e Easv3 usano il processore 2.35 GHz EPYC<sup>TM</sup> 7452V di AMD in una configurazione multithread con una cache L3 fino a 256 MB, aumentando le opzioni per l'esecuzione della maggior parte dei carichi di lavoro con ottimizzazione per la memoria.  La serie Eav3 e la serie Easv3 hanno le stesse configurazioni di memoria e disco della serie EV3 & Esv3.

* La serie Mv2 offre il numero massimo di vCPU (fino a 208 vCPU) e la memoria massima (fino a 5,7 TiB) di qualsiasi macchina virtuale nel cloud. È ideale per database molto grandi o altre applicazioni che traggono vantaggio da un elevato numero di vCPU e una grande quantità di memoria.

* La serie M offre un numero elevato di vCPU (fino a 128 vCPU) e una grande quantità di memoria (fino a 3,8 TiB). È ideale anche per database di dimensioni molto grandi o altre applicazioni che traggono vantaggio da conteggi vCPU elevati e grandi quantità di memoria.

* Le controparti serie dv2, G e DSv2/GS sono ideali per le applicazioni che richiedono un vCPU più veloce, prestazioni di archiviazione temporanee migliori o richieste di memoria più elevate. Offrono una potente combinazione per molte applicazioni di livello aziendale.

* Serie Dv2, una versione successiva della serie D originale, dotata di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. Si basa sui processori Intel Xeon® E5-2673 V3 2,4 GHz (Haswell) o E5-2673 v4 2,3 GHz (Broadwell) di ultima generazione a 2,4 GHz e con la tecnologia Intel Turbo Boost 2,0, può arrivare fino a 3,1 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

* Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente.  Queste dimensioni delle macchine virtuali sono particolarmente adatte ai carichi di lavoro che richiedono un elevato livello di isolamento dagli altri clienti, per i carichi di lavoro con aspetti come i requisiti normativi e di conformità.  I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Per le opzioni di VM con piano Isolato vedere le tabelle delle famiglie di macchine virtuali di seguito.

## <a name="esv3-series"></a>Serie Esv3

ACU: 160-190 <sup>1</sup>

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

Le istanze della serie ESv3 sono basate sul processore Intel Xeon® E5-2673 v4 (Broadwell) a 2,3 GHz e con la tecnologia Intel Turbo Boost 2.0 possono arrivare fino a 3,5 GHz e usare Archiviazione Premium. Le istanze della serie Ev3 sono ideali per applicazioni aziendali a uso intensivo di memoria.


| Dimensione             | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
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

## <a name="easv3-series"></a>Serie Easv3

Archiviazione Premium: Supportato

Caching archiviazione Premium: Supportato

Le dimensioni della serie Easv3 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> che può ottenere una fmax con boosting di 3.35 GHz e usare archiviazione Premium. Le dimensioni della serie Easv3 sono ideali per applicazioni aziendali a uso intensivo di memoria.

| Dimensione | CPU virtuale | Memoria: GiB | Archiviazione temporanea (SSD): GiB |
|---|---|---|---|
| Standard_E2as_v3  | 2  | 16  | 32  |
| Standard_E4as_v3  | 4  | 32  | 64  |
| Standard_E8as_v3  | 8  | 64  | 128 |
| Standard_E16as_v3 | 16 | 128 | 256 |
| Standard_E32as_v3 | 32 | 256 | 512 |
| Standard_E48as_v3 | 48 | 384 | 768 |
| Standard_E64as_v3 | 64 | 432 | 864 |

## <a name="ev3-series"></a>Serie Ev3 

ACU: 160 - 190 <sup>1</sup>

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

Le istanze della serie Ev3 sono basate sul processore Intel Xeon® E5-2673 v4 (Broadwell) a 2,3 GHz e con la tecnologia Intel Turbo Boost 2.0 possono arrivare fino a 3,5 GHz. Le istanze della serie Ev3 sono ideali per applicazioni aziendali a uso intensivo di memoria.

L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare dischi di archiviazione Premium, usare le dimensioni ESv3. I prezzi e i contatori di fatturazione per le dimensioni ESv3 sono uguali a quelli della serie Ev3. 


| Dimensione            | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Larghezza di banda della rete/scheda NIC max |
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

## <a name="eav3-series"></a>Serie Eav3

Archiviazione Premium: Non supportata

Caching archiviazione Premium: Non supportata

Le dimensioni della serie Eav3 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> che può ottenere una fmax con boosting di 3.35 GHz e usare archiviazione Premium. Le dimensioni della serie Eav3 sono ideali per applicazioni aziendali a uso intensivo di memoria. L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare i dischi di archiviazione Premium, usare le dimensioni della serie Easv3. I prezzi e i contatori di fatturazione per le dimensioni Easv3 sono uguali a quelli della serie Eav3.

| Dimensione | CPU virtuale | Memoria: GiB | Archiviazione temporanea (SSD): GiB |
|---|---|---|---|---|---|
| Standard_E2a_v3  | 2  | 16  | 50   |
| Standard_E4a_v3  | 4  | 32  | 100  |
| Standard_E8a_v3  | 8  | 64  | 200  |
| Standard_E16a_v3 | 16 | 128 | 400  |
| Standard_E32a_v3 | 32 | 256 | 800  |
| Standard_E48a_v3 | 48 | 384 | 1200 |
| Standard_E64a_v3 | 64 | 432 | 1600 |

## <a name="mv2-series"></a>Serie Mv2

Archiviazione Premium: Supportato

Caching archiviazione Premium: Supportato

Acceleratore di scrittura: [Supportato](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

La serie Mv2 offre una velocità effettiva elevata, una bassa latenza, un'archiviazione NVMe locale con mapping diretto in esecuzione su un processore Intel® Xeon® Platinum a 2,5 GHz a Hyper-Threading con una frequenza di base di tutti i core di 2,5 GHz e una frequenza massima turbo di 3,8 GHz. Tutte le dimensioni delle macchine virtuali della serie Mv2 possono usare dischi permanenti standard e Premium. Le istanze della serie Mv2 sono dimensioni di VM con ottimizzazione per la memoria che offrono prestazioni di calcolo ineguagliabili per supportare database e carichi di lavoro in memoria di grandi dimensioni, con un rapporto elevato tra memoria e CPU, ideale per server di database relazionali, cache di grandi dimensioni e in memoria analisi. 

|Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |

Funzionalità della VM della serie Mv2 Intel® tecnologia Hyper-Threading  

<sup>1</sup> queste macchine virtuali di grandi dimensioni richiedono uno di questi sistemi operativi guest supportati: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> le macchine virtuali della serie Mv2 sono solo di generazione 2. Se si usa Linux, vedere la sezione seguente per informazioni su come trovare e selezionare un'immagine SUSE Linux.

#### <a name="find-a-suse-image"></a>Trovare un'immagine SUSE

Per selezionare un'immagine SUSE Linux appropriata nel portale di Azure: 

1. Nella portale di Azure selezionare **Crea una risorsa** . 
1. Cercare "SUSE SAP" 
1. Le immagini SLES per SAP Generation 2 sono disponibili per le immagini con pagamento in base al consumo o bring your own Subscription (BYOS). Nei risultati della ricerca espandere la categoria immagine desiderata:

    * SUSE Linux Enterprise Server (SLES) per SAP
    * SUSE Linux Enterprise Server (SLES) per SAP (BYOS)
    
1. Le immagini SUSE compatibili con la serie Mv2 hanno come prefisso il nome `GEN2:`. Per le macchine virtuali della serie Mv2 sono disponibili le immagini SUSE seguenti:

    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SP4 per le applicazioni SAP
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 per le applicazioni SAP
    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SP4 per le applicazioni SAP (BYOS)
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 per le applicazioni SAP (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Selezionare un'immagine SUSE tramite l'interfaccia della riga di comando

Per visualizzare un elenco delle immagini SLES per SAP attualmente disponibili per le macchine virtuali della serie Mv2, usare il [`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) comando seguente:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

Il comando restituisce le VM di generazione 2 attualmente disponibili da SUSE per le macchine virtuali della serie Mv2. 

Output di esempio:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>Serie M 

ACU: 160-180 <sup>1</sup>

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

Acceleratore di scrittura:  [Supportato](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Dimensione            | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000/125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000 / 200 (1587) | 10000/250 | 8 / 4000 |
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

<sup>2</sup> Data la presenza di più di 64 vCPU, è necessario uno dei sistemi operativi guest supportati seguenti: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7.3 o Oracle Linux 7.3 con LIS 4.2.1.

<sup>3</sup> Disponibili dimensioni core vincolate.

<sup>4</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-series 11-15

ACU: 210 - 250 <sup>1</sup>

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

| Dimensione | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
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

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

| Dimensione              | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64/64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.  
<sup>2</sup> 25000 Mbps con rete accelerata. 
