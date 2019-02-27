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
ms.openlocfilehash: dbbfea183454b1068558111bf62b45f5fa6415cc
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56334028"
---
Le dimensioni delle macchine virtuali ottimizzate per l'archiviazione offrono I/O e velocità effettiva del disco elevati, ideali per i database NoSQL, SQL e Big Data, data warehousing e database transazionali di grandi dimensioni.  Tra gli esempi sono inclusi Cassandra, MongoDB, Cloudera e Redis. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda di rete per ogni dimensione ottimizzata.

La serie Lsv2 offre velocità effettiva elevata, bassa latenza, risorse di archiviazione NVMe locali con mapping diretto in esecuzione sul [processore &trade;7551](https://www.amd.com/en/products/epyc-7000-series) AMD EPYC con un turbo boost "all core" di 2,55 GHz e un turbo boost massimo di 3 GHz. Le macchine virtuali Serie Lsv2 sono disponibili in dimensioni comprese tra 8 e 80 vCPU in una configurazione multi-thread simultanea.  Sono disponibili 8 GiB di memoria per ogni vCPU e un dispositivo NVMe SSD M.2 da 1,92 TB per 8 vCPU, fino a 19,2 TB (10 x 1,92 TB) disponibili nella versione L80s v2.

La serie Ls offre fino a 32 vCPU e usa il [processore Intel® Xeon® della famiglia E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). La serie Ls offre le stesse prestazioni CPU della serie G/GS ed è dotato di 8 GiB di memoria per ogni vCPU.

> [!NOTE]
> Le macchine virtuali serie Lsv2 sono ottimizzate per l'utilizzo del disco locale presente nel nodo direttamente collegato alla macchina virtuale e non di dischi dati permanenti.  Questo consente un maggior numero di operazioni di I/O al secondo o un maggiore velocità effettiva per i carichi di lavoro.  Le serie Ls e Lsv2 non supportano la creazione di una cache locale per aumentare il numero di operazioni di I/O al secondo che è possibile ottenere tramite i dischi dati permanenti. I valori elevati di velocità effettiva e operazioni di I/O al secondo del disco locale rendono le macchine virtuali serie Lsv2 e Ls ideali per gli archivi NoSQL come Apache Cassandra e MongoDB che replicano i dati tra più macchine virtuali per ottenere persistenza in caso di errore di una singola macchina virtuale. 

## <a name="lsv2-series"></a>Serie Lsv2
ACU: 150-175

Archiviazione Premium: Supportato

Memorizzazione nella cache Archiviazione Premium: Non supportato

| Dimensione          | vCPU | Memoria (GiB) | Disco temporaneo<sup>1</sup> (GiB) | Dischi NVMe<sup>2</sup> | Velocità effettiva dischi NVMe<sup>3</sup> (IOPS di lettura/MBps) | Dimensione cache host<sup>4</sup> | Numero massimo di dischi dati | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1 x 1,92 TB  | 340.000 / 2.000 | N/D | 16 | 2 / 3.200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2 x 1,92 TB  | 680.000 / 4.500 | N/D | 32 | 4 / 6.400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4 x 1,92 TB  | 1,4 M / 9.000    | N/D | 32 | 8 / 12.800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8 x 1,92 TB  | 2,7 M / 18.000   | N/D | 32 | 8 / 25.600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10 x 1,92 TB   | 3,4 M / 22.000   | N/D | 32 | 8 / 32.000 |
 
<sup>1</sup> Le macchine virtuali serie Lsv2 hanno un disco risorse temporaneo basato su SCSI standard per l'uso di un file di scambio/paging del sistema operativo (D: in Windows, /dev/sdb in Linux). Il disco offre 80 GiB di archiviazione, 4.000 IOPS e una velocità di trasferimento di 80 MBps ogni 8 vCPU (ad esempio, il modello Standard_L80s_v2 fornisce 800 GiB a 40.000 IOPS e 800 MBPS). In questo modo, le unità NVMe possono essere completamente dedicate all'utilizzo dell'applicazione. Questo disco è temporaneo e tutti i dati andranno persi al momento dell'arresto/deallocazione.

<sup>2</sup> I dischi NVMe locali sono temporanei: i dati dei dischi andranno persi se si arresta/dealloca la macchina virtuale.

<sup>3</sup> La tecnologia Hyper-V NVMe Direct offre un accesso senza limitazione alle unità NVMe locali mappate in modo sicuro nello spazio della macchina virtuale guest.  Per ottenere le massime prestazioni è necessario usare la build WS2019 più recente o Ubuntu 18.04 o 16.04 disponibile in Azure Marketplace.  Le prestazioni di scrittura variano in base alle dimensioni di I/O, al caricamento delle unità e all'utilizzo della capacità.

<sup>4</sup> Le macchine virtuali serie Lsv2 non prevedono la cache dell'host per il disco dati perché non genera alcun vantaggio per i carichi di lavoro Lsv2.  Possono tuttavia prevedere l'opzione disco del sistema operativo temporaneo della macchina virtuale di Azure (fino a 30 GiB). 



## <a name="ls-series"></a>Serie Ls
ACU: 180-240

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato
 
| Dimensione          | vCPU | Memoria (GiB) | Spazio di archiviazione temp (GiB) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea (IOPS/Mbps) | Velocità effettiva massima del disco non memorizzato nella cache (IOPS/MBps) | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5.000/125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10.000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80.000 / 800 | 20.000 / 500 | 8 / 16.000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40.000 /1.000     | 8 / 20,000 | 
 

La massima velocità effettiva del disco possibile con le macchine virtuali serie Ls può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

## <a name="size-table-definitions"></a>Definizioni delle tabelle delle dimensioni

- La capacità di archiviazione viene visualizzata in unità di GiB o 1.024^3 byte. Quando si confrontano dischi misurati in GB (1.000^3 byte) con dischi misurati in GiB (1.024^3), tenere presente che i valori di capacità specificati in GiB potrebbero apparire inferiori. Ad esempio, 1.023 GiB = 1.098,4 GB
- La velocità effettiva del disco viene misurata in operazioni di input/output al secondo (IOPS) e MBps, dove il valore di MBps corrisponde a 10^6 byte al secondo.
- Se si vogliono ottenere prestazioni ottimali per le macchine virtuali, è necessario limitare il numero di dischi dati a 2 per ogni vCPU.
- La **larghezza di banda della rete prevista** è la [larghezza di banda aggregata massima allocata per ogni tipo di macchina virtuale](../articles/virtual-network/virtual-machine-network-throughput.md) in tutte le schede di interfaccia di rete, per tutte le destinazioni. I limiti massimi non sono garantiti, ma hanno lo scopo di fornire indicazioni per la selezione del tipo di macchina virtuale corretto per l'applicazione desiderata. Le prestazioni di rete effettive dipenderanno da svariati fattori, tra cui congestione della rete, carichi dell'applicazione e impostazioni di rete. Per informazioni sull'ottimizzazione della velocità effettiva della rete, vedere [Ottimizzazione della velocità effettiva di rete per Windows e Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Per realizzare le prestazioni di rete previste in Linux o Windows, potrebbe essere necessario selezionare una versione specifica o ottimizzare la macchina virtuale. Per ulteriori informazioni, vedere [Come eseguire test affidabili della velocità effettiva della macchina virtuale](../articles/virtual-network/virtual-network-bandwidth-testing.md).
