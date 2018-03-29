---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: b29429e1efe46aef8263bed5edb3daeffdf41122
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
Le dimensioni delle macchine virtuali di utilizzo generico offrono un rapporto CPU-memoria equilibrato. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento. 

- Le VM di serie A e Av2 possono essere distribuite su una vasta gamma di tipi di hardware e processori. La dimensione è limitata in base all'hardware per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale.

- Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e vCPU superiore e un'unità SSD per il disco temporaneo. Per ulteriori informazioni, vedere l'annuncio sul blog di Azure, [Nuove dimensioni delle macchine virtuali serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

- Le serie Dv3 e Dv2, versioni successive della serie D originale, sono dotate di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswell) e grazie alla tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,1 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

- Le dimensioni del livello Basic sono destinate principalmente ai carichi di lavoro di sviluppo e alle altre applicazioni che non necessitano di bilanciamento del carico, scalabilità automatica o macchine virtuali con utilizzo intensivo della memoria.

## <a name="b-series"></a>Serie B

Le VM della serie B che prevedono burst sono ideali per carichi di lavoro che non necessitano in modo continuativo delle prestazioni complete della CPU, ad esempio server Web, database di piccole dimensioni e ambienti di sviluppo e test. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre a questi clienti la possibilità di acquistare dimensioni per la VM con prestazioni baseline in relazione al prezzo, che consentono all'istanza della VM di accumulare crediti quando la VM utilizza meno prestazioni di quelle base. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline della VM, usando fino al 100% della CPU quando l'applicazione richiede prestazioni superiori per la CPU.


| Dimensione             | vCPU  | Memoria: GiB | Unità SSD locale: GiB | Prestazioni di base di un core | Crediti accumulati/Ora | Crediti massimi accumulati | Valore massimo per dischi di dati | Prestazioni massime del disco locale: IOPS/MBps | Prestazioni massime del disco rimosso dalla cache: IOPS/MBps | Schede di interfaccia di rete max |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 6                  | 144            | 2                                      | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 12                 | 288            | 2                                      | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |


## <a name="dsv3-series-sup1sup"></a>Serie Dsv3 <sup>1</sup>

Unità di calcolo di Azure: 160-190

Le dimensioni della serie Dsv3 sono basate sul processore Intel Xeon® E5-2673 v3 (Haswell) a 2,4 GHz o sul processore più recente Intel XEON® E5-2673 v4 (Broadwell) a 2,3 GHz che con la tecnologia Intel Turbo Boost 2.0 possono arrivare fino a 3,5 GHz e usare Archiviazione Premium. Le dimensioni della serie Dsv3 offrono una potente combinazione di vCPU, memoria e spazio di archiviazione temporaneo ideale per la maggior parte delle applicazioni di produzione.


| Dimensione             | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Valore massimo per dischi di dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4,000 / 32 (50)                                                       | 3.200/48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8,000 / 64 (100)                                                      | 6.400/96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16,000 / 128 (200)                                                    | 12.800/192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32,000 / 256 (400)                                                    | 25.600/384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64,000 / 512 (800)                                                    | 51.200/768                              | 8 / 16000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128,000 / 1024 (1600)                                                    | 80,000 / 1200                              | 8 / 30000                                               |

<sup>1</sup> Le macchine virtuali serie Dsv3 integrano la tecnologia Intel® Hyper-Threading

## <a name="dv3-series-sup1sup"></a>Serie Dv3 <sup>1</sup>

Unità di calcolo di Azure: 160-190

Le dimensioni della serie Dv3 sono basate sul processore Intel Xeon® E5-2673 v3 (Haswell) a 2,4 GHz o sul processore Intel XEON® E5-2673 v4 (Broadwell) a 2,3 GHz che con la tecnologia Intel Turbo Boost 2.0 possono arrivare fino a 3,5 GHz. Le dimensioni della serie Dv3 offrono una potente combinazione di vCPU, memoria e spazio di archiviazione temporaneo ideale per la maggior parte delle applicazioni di produzione.

L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare dischi di archiviazione Premium, usare le dimensioni Dsv3. I prezzi e i contatori di fatturazione per le dimensioni Dsv3 sono uguali a quelli della serie Dv3. 


| Dimensione            | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Valore massimo per dischi di dati | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Larghezza di banda della rete/scheda NIC max |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128          | 800            | 32             | 48000/750/375                                            | 8 / 16000                             |
| Standard_D64_v3 | 64        | 256          | 1600            | 32             | 96000/1000/500                                            | 8 / 30000                             |

<sup>1</sup> Le macchine virtuali serie Dv3 integrano la tecnologia Intel® Hyper-Threading

## <a name="dsv2-series"></a>Serie DSv2

ACU: 210-250

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Valore massimo per dischi di dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4.000/32 (43) |3.200/48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8.000/64 (86) |6.400/96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16.000/128 (172) |12.800/192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32.000/256 (344) |25.600/384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64.000/512 (688) |51.200/768 |8 / 12000 |



## <a name="dv2-series"></a>Serie Dv2

ACU: 210-250

| Dimensione              | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1_v2    | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4/4 x 500                         | 2 / 750                 |
| Standard_D2_v2    | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1500                     |
| Standard_D3_v2    | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 3000                     |
| Standard_D4_v2    | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 6000                     |
| Standard_D5_v2    | 16        | 56          | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000  |


<br>

## <a name="ds-series"></a>Serie DS

ACU: 160

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Valore massimo per dischi di dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4.000/32 (43) |3.200/32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8.000/64 (86) |6.400/64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16.000/128 (172) |12.800/128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32.000/256 (344) |25.600/256 |8 / 4000 |

<br>

## <a name="d-series"></a>Serie D 

ACU: 160

| Dimensione         | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4/4 x 500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 4000                     |

<br>


## <a name="av2-series"></a>Serie Av2

ACU: 100

| Dimensione            | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2/2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4/4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8/8 x 500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16/16 x 500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4/4 x 500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8/8 x 500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16/16 x 500             | 8 / 2000                     |

<br>

## <a name="a-series"></a>Serie A

ACU: 50-100

| Dimensione | vCPU | Memoria: GiB | Spazio di archiviazione temp (HDD): GiB | Valore massimo per dischi di dati | Velocità effettiva del disco di dati max: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps)  |
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

|Dimensioni – Dimensioni\Nome | vCPU |Memoria|NIC (Max)|Dimensioni massime per il disco temporaneo |Max. dischi dati da 1023 GB ciascuno|Max. IOPS (300 per disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|
