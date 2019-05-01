---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: da1328ba826ce940115bc45ffc8d6f417eeda798
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744285"
---
Questa sezione vengono fornite informazioni su versioni precedenti di dimensioni delle macchine virtuali. Queste dimensioni possono ancora essere usate, ma sono disponibili generazioni più recenti. 

## <a name="f-series"></a>Serie F

La serie F è basata sul processore Intel Xeon® E5-2673 v3 (Haswell) a 2,4 GHz che può raggiungere velocità di clock fino a 3,1 GHz con la tecnologia Intel Turbo Boost 2.0. Si tratta delle stesse prestazioni CPU della serie Dv2 di VM.  

Le VM serie F sono un'ottima scelta per i carichi di lavoro che richiedono CPU più veloci, ma che non necessitano della stessa memoria o risorsa di archiviazione temporanea per ogni vCPU.  Carichi di lavoro come server Web, analisi, giochi ed elaborazione batch trarranno vantaggio dal valore della serie F.

ACU: 210 - 250

Archiviazione Premium:  Non supportato

Memorizzazione nella cache archiviazione Premium:  Non supportato

| Dimensione         | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Velocità effettiva massima del disco dati: IOPS | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4/4 x 500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8/8 x 500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16/16 x 500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32/32 x 500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Serie Fs <sup>1</sup>

La serie Fs offre tutti i vantaggi della serie F, oltre all'archiviazione Premium.

ACU: 210 - 250

Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4.000/32 (12) |3.200/48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8.000/64 (24) |6.400/96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16.000/128 (48) |12.800/192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32.000/256 (96) |25.600/384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64.000/512 (192) |51.200/768 |8 / 12000 |

MBps = 10^6 byte al secondo e GiB = 1024^3 byte.

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una macchina virtuale serie Fs può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Serie Ls

La serie Ls offre fino a 32 vCPU e usa il [processore Intel® Xeon® della famiglia E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). La serie Ls offre le stesse prestazioni CPU della serie G/GS ed è dotato di 8 GiB di memoria per ogni vCPU.

La serie Ls non supporta la creazione di una cache locale per aumentare il numero di operazioni di I/O al secondo che è possibile ottenere tramite i dischi dati permanenti. L'elevata velocità effettiva e IOPS del disco locale rende le VM serie Ls ideali per gli archivi NoSQL, ad esempio Apache Cassandra e MongoDB che replicano dati tra più macchine virtuali per eseguire il salvataggio permanente in caso di errore di una singola macchina virtuale.

ACU: 180-240

Archiviazione Premium:  Supportato

Memorizzazione nella cache archiviazione Premium:  Non supportato
 
| Dimensione          | vCPU | Memoria (GiB) | Spazio di archiviazione temp (GiB) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea (IOPS/Mbps) | Velocità effettiva massima del disco non memorizzato nella cache (IOPS/MBps) | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5.000/125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10.000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80.000 / 800 | 20.000 / 500 | 8 / 16.000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40.000 /1.000     | 8 / 20,000 | 

La massima velocità effettiva del disco possibile con le macchine virtuali serie Ls può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 che usa l'interfaccia della riga di comando e PowerShell

Nel modello di distribuzione classico, alcuni nomi di dimensioni VM sono leggermente diversi in PowerShell e nell'interfaccia della riga di comando:

* Standard_A0 è ExtraSmall
* Standard_A1 è Small
* Standard_A2 è Medium
* Standard_A3 è Large
* Standard_A4 è ExtraLarge
