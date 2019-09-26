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
ms.openlocfilehash: 5d89feaa5225aea56af86aa7f70d3666994fdaec
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266892"
---
Le dimensioni delle macchine virtuali di utilizzo generico offrono un rapporto CPU-memoria equilibrato. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione per ogni dimensione di questo raggruppamento.

- La [serie DC](#dc-series) è una famiglia di macchine virtuali in Azure che può aiutare a proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Queste macchine sono supportate dall'ultima generazione di processori Intel XEON E-2176G da 3,7 GHz con tecnologia SGX. Con la tecnologia Intel Turbo Boost, queste macchine possono arrivare fino a 4,7 GHz. Le istanze della serie DC consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati mentre sono in uso.

- Le macchine virtuali di serie Av2 possono essere distribuite su una vasta gamma di tipi di hardware e processori. Le VM serie A offrono configurazioni di memoria e prestazioni della CPU ideali per i carichi di lavoro di base, ad esempio quelli di sviluppo e test. La dimensione è limitata in base all'hardware per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale.

  I casi d'uso di esempio includono server di sviluppo e test, server Web con poco traffico, database da piccoli a medi, modelli di prova e repository di codice.

- La serie Dv2, un'evoluzione della serie D originale, offre una CPU più potente e una configurazione ottimale del rapporto tra CPU e memoria ed è quindi ideale per la maggior parte dei carichi di lavoro di produzione. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. Si basa sui processori Intel Xeon® E5-2673 V3 2,4 GHz (Haswell) o E5-2673 v4 2,3 GHz (Broadwell) di ultima generazione e con la tecnologia Intel Turbo Boost 2,0, può arrivare fino a 3,1 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

- La serie Dv3 include il processore da 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) o il processore più recente da 2,3 GHz Intel XEON ® E5-2673 v4 (Broadwell) in una configurazione con hyperthreading, assicurando una proposta di valore ottimizzata per la maggior parte dei carichi di lavoro per utilizzo generico.  La memoria è stata estesa (da ~3,5 GiB/vCPU a 4 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading.  La serie Dv3 non ha più le dimensioni delle macchine virtuali con memoria elevata delle famiglie D/Dv2, che sono state spostate nella nuova famiglia Ev3.

  Esempi di casi d'uso di serie D sono le applicazioni di livello aziendale, i database relazionali, la memorizzazione nella cache in memoria e l'analisi.

- Le serie da e Dasv3 sono nuove dimensioni che usano il processore 2.35 GHz EPYC<sup>TM</sup> 7452V di AMD in una configurazione multithread con una cache L3 fino a 256 GB che dedicano 8 GB di tale cache L3 a ogni 8 core che aumentano le opzioni dei clienti per l'esecuzione generale carichi di lavoro scopo. Le serie da e Dasv3 hanno le stesse configurazioni di memoria e disco della serie D & Dsv3.
  
## <a name="b-series"></a>Serie B

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Non supportata

Le VM della serie B che prevedono burst sono ideali per carichi di lavoro che non necessitano in modo continuativo delle prestazioni complete della CPU, ad esempio server Web, database di piccole dimensioni e ambienti di sviluppo e test. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre a questi clienti la possibilità di acquistare dimensioni per la VM con prestazioni baseline in relazione al prezzo, che consentono all'istanza della VM di accumulare crediti quando la VM utilizza meno prestazioni di quelle base. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline della VM, usando fino al 100% della CPU quando l'applicazione richiede prestazioni superiori per la CPU.

I casi d'uso di esempio includono server di sviluppo e test, server Web con traffico ridotto, database di piccole dimensioni, microservizi, server per modelli di prova e server di compilazione.


| Size             | CPU virtuale  | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Prestazioni base della CPU della VM | Prestazioni massime della CPU della VM | Crediti iniziali | Crediti accumulati/Ora | Crediti massimi accumulati | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200/10                                  | 160/10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480/75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640/100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800/125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls è supportato solo in Linux

## <a name="dsv3-series-sup1sup"></a>Serie Dsv3 <sup>1</sup>

ACU: 160-190

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

Le dimensioni della serie Dsv3 sono basate sul processore Intel Xeon® E5-2673 v3 (Haswell) a 2,4 GHz o sul processore più recente Intel XEON® E5-2673 v4 (Broadwell) a 2,3 GHz che con la tecnologia Intel Turbo Boost 2.0 possono arrivare fino a 3,5 GHz e usare Archiviazione Premium. Le dimensioni della serie Dsv3 offrono una potente combinazione di vCPU, memoria e spazio di archiviazione temporaneo ideale per la maggior parte delle applicazioni di produzione.


| Size             | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000/768 (1200)                                                    | 76800/1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000/1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> Le macchine virtuali serie Dsv3 integrano la tecnologia Intel® Hyper-Threading

## <a name="dasv3-series-preview"></a>Serie Dasv3 (anteprima)

Archiviazione Premium: Supportato

Caching archiviazione Premium: Supportato

Le dimensioni della serie Dasv3 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere un Fmax incrementato di 3.35 GHz e usare archiviazione Premium. Le dimensioni della serie Dasv3 offrono una combinazione di vCPU, memoria e archiviazione temporanea per la maggior parte dei carichi di lavoro di produzione.

[Fare clic qui per iscriversi all'anteprima](http://aka.ms/azureamdpreview).

| Size | CPU virtuale | Memoria: GiB | Archiviazione temporanea (SSD): GiB |
|---|---|---|---|
| Standard_D2as_v3  | 2  | 8   | 16  |
| Standard_D4as_v3  | 4  | 16  | 32  |
| Standard_D8as_v3  | 8  | 32  | 64  |
| Standard_D16as_v3 | 16 | 64  | 128 |
| Standard_D32as_v3 | 32 | 128 | 256 |
| Standard_D48as_v3 | 48 | 192 | 384 |
| Standard_D64as_v3 | 64 | 256 | 512 |

## <a name="dv3-series-sup1sup"></a>Serie Dv3 <sup>1</sup>

ACU: 160-190

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

Le dimensioni della serie Dv3 sono basate sul processore Intel Xeon® E5-2673 v3 (Haswell) a 2,4 GHz o sul processore Intel XEON® E5-2673 v4 (Broadwell) a 2,3 GHz che con la tecnologia Intel Turbo Boost 2.0 possono arrivare fino a 3,5 GHz. Le dimensioni della serie Dv3 offrono una potente combinazione di vCPU, memoria e spazio di archiviazione temporaneo ideale per la maggior parte delle applicazioni di produzione.

L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare dischi di archiviazione Premium, usare le dimensioni Dsv3. I prezzi e i contatori di fatturazione per le dimensioni Dsv3 sono uguali a quelli della serie Dv3. 


| Dimensione            | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Larghezza di banda della rete/scheda NIC max |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> Le macchine virtuali serie Dv3 integrano la tecnologia Intel® Hyper-Threading

## <a name="dav3-series-preview"></a>Serie Dav3 (anteprima)

Archiviazione Premium: Non supportata

Caching archiviazione Premium: Non supportata

Le dimensioni della serie Dav3 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere un Fmax incrementato di 3.35 GHz. Le dimensioni della serie Dav3 offrono una combinazione di vCPU, memoria e archiviazione temporanea per la maggior parte dei carichi di lavoro di produzione. L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare i dischi di archiviazione Premium, usare le dimensioni Dasv3. I prezzi e i contatori di fatturazione per le dimensioni Dasv3 sono uguali a quelli della serie Dav3.

[Fare clic qui per iscriversi all'anteprima](http://aka.ms/azureamdpreview).

| Size | CPU virtuale | Memoria: GiB | Archiviazione temporanea (SSD): GiB |
|---|---|---|---|
| Standard_D2a_v3  | 2  | 8   | 50   |
| Standard_D4a_v3  | 4  | 16  | 100  |
| Standard_D8a_v3  | 8  | 32  | 200  |
| Standard_D16a_v3 | 16 | 64  | 400  |
| Standard_D32a_v3 | 32 | 128 | 800  |
| Standard_D48a_v3 | 48 | 192 | 1200 |
| Standard_D64a_v3 | 64 | 256 | 1600 |

## <a name="dsv2-series"></a>Serie DSv2

ACU: 210-250

Archiviazione Premium:  Supportato

Caching archiviazione Premium:  Supportato

| Size | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4000 / 32 (43) |3200/48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400/96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000/128 (172) |12800/192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000/256 (344) |25600/384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000/512 (688) |51200/768 |8 / 12000 |

## <a name="dv2-series"></a>Serie Dv2

ACU: 210-250

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata

| Dimensione           | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Numero massimo di dischi dati | Throughput Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 4              | 4x500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8x500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Serie Av2

ACU: 100

Archiviazione Premium:  Non supportata

Caching archiviazione Premium:  Non supportata


| Dimensione            | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: Input/output al secondo | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2/2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4/4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8/8 x 500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16/16 x 500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4/4 x 500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8/8 x 500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16/16 x 500             | 8 / 2000                     |

## <a name="dc-series"></a>Serie DC

Archiviazione Premium: Supportato

Caching archiviazione Premium: Supportato



| Size          | CPU virtuale | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |
