---
title: Limiti di memoria e concorrenza - Azure SQL Data Warehouse | Microsoft Docs
description: Visualizzare i limiti di memoria e concorrenza allocati ai livelli di prestazione e alle classi di risorse in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4e6e95e8601e7ab8b836e2aa1aa21ef4d5779954
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limiti di memoria e concorrenza per Azure SQL Data Warehouse
Visualizzare i limiti di memoria e concorrenza allocati ai livelli di prestazione e alle classi di risorse in Azure SQL Data Warehouse. Per altre informazioni e per applicare queste funzionalità al piano di gestione del carico di lavoro, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md). 

## <a name="performance-tiers"></a>Livelli di prestazioni

Sono disponibili due livelli di prestazioni di SQL Data Warehouse ottimizzati per carichi di lavoro analitici. Un livello di prestazioni è un'opzione che determina la configurazione del data warehouse. Questa opzione è una delle prime scelte da effettuare durante la creazione di un data warehouse.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- Il **livello di prestazioni ottimizzato per l'elasticità** separa i livelli di calcolo e di archiviazione nell'architettura. Questa opzione è ideale per i carichi di lavoro che possono sfruttare la separazione tra calcolo e archiviazione, con scalabilità frequente a supporto di brevi periodi di attività di picco. Questo livello di calcolo garantisce il costo più basso, con scalabilità sufficiente a supportare la maggior parte dei carichi di lavoro dei clienti.

- Il **livello di prestazioni ottimizzato per il calcolo** usa l'hardware di Azure più recente per introdurre una nuova cache SSD (Solid State Disk) NVMe che mantiene i dati usati più spesso vicino alla CPU, ovvero nella posizione migliore possibile. Grazie alla suddivisione automatica dei livelli di archiviazione, questo livello di prestazioni è straordinario per le query complesse, perché tutte le operazioni di I/O avvengono in locale nel livello di calcolo. Il columnstore è stato migliorato per archiviare una quantità di dati illimitata in SQL Data Warehouse. Il livello di prestazioni ottimizzato per il calcolo offre la massima scalabilità, consentendo di arrivare fino a 30.000 DWU a elevato utilizzo di calcolo. Scegliere questo livello per i carichi di lavoro che richiedono prestazioni continue ed estremamente veloci.

## <a name="data-warehouse-limits"></a>Limiti relativi al data warehouse
Nelle tabelle seguenti viene illustrata la capacità massima per il data warehouse a diversi livelli di prestazioni. Per modificare il livello di prestazioni, vedere [Ridimensionare le risorse di calcolo: portale](quickstart-scale-compute-portal.md).

### <a name="optimized-for-elasticity"></a>Livello ottimizzato per l'elasticità

I livelli di servizio per il livello di prestazioni ottimizzato per l'elasticità hanno un intervallo compreso tra DW100 e DW6000. 

| Livello di prestazioni | Numero massimo di query simultanee | Nodi di calcolo | Distribuzioni per nodo di calcolo | Memoria massima per distribuzione (MB) | Memoria massima per data warehouse(GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1.600                            |  96                                |
| DW500         | 20                     | 5             | 12                             | 2.000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4.000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6000                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8.000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24,000                           | 1.440                               |

### <a name="optimized-for-compute"></a>Livello ottimizzato per il calcolo

Il livello di prestazioni ottimizzato per il calcolo offre 2,5 x di memoria in più per ogni query rispetto al livello di prestazioni ottimizzato per l'elasticità. Questa memoria aggiuntiva consente al livello di prestazioni ottimizzato per il calcolo di restituire prestazioni elevate.  I livelli di prestazioni per il livello di prestazioni ottimizzato per il calcolo hanno un intervallo compreso tra DW1000c e DW30000c. 

| Livello di prestazioni | Numero massimo di query simultanee | Nodi di calcolo | Distribuzioni per nodo di calcolo | Memoria massima per distribuzione (GB) | Memoria massima per data warehouse(GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

Il DWU a elevato utilizzo di calcolo massimo è DW30000c, che ha 60 nodi di calcolo e una distribuzione per ogni nodo di calcolo. Ad esempio, un data warehouse da 600 TB a DW30000c elabora circa 10 TB per ogni nodo di calcolo.


## <a name="concurrency-maximums"></a>Valori massimi di concorrenza
Affinché ogni query abbia risorse sufficienti per operare in modo efficace, SQL Data Warehouse tiene traccia dell'uso delle risorse di calcolo assegnando slot di concorrenza a ogni query. Il sistema inserisce le query in una coda dove rimangono in attesa fino a quando non sono disponibili [slot di concorrenza](resource-classes-for-workload-management.md#concurrency-slots) sufficienti. 

Gli slot di concorrenza determinano anche la priorità della CPU. Per altre informazioni, vedere [Analyze your workload](analyze-your-workload.md) (Analisi del carico di lavoro)

### <a name="optimized-for-compute"></a>Livello ottimizzato per il calcolo
La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse dinamica](resource-classes-for-workload-management.md). I valori fanno riferimento al livello di prestazioni ottimizzato per il calcolo.

**Classi di risorse dinamiche**
| Livello di prestazioni | Numero massimo di query simultanee | Slot di concorrenza disponibili | Slot utilizzati da smallrc | Slot utilizzati da mediumrc | Slot utilizzati da largerc | Slot utilizzati da xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1200                        | 1                     | 64                     | 128                   | 256                    |

**Classi di risorse statiche**

La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse statica](resource-classes-for-workload-management.md).  

| Contratto | Numero massimo di query simultanee | Slot di concorrenza disponibili |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Livello ottimizzato per l'elasticità
La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse dinamica](resource-classes-for-workload-management.md).  I valori fanno riferimento al livello di prestazioni ottimizzato per l'elasticità.

**Classi di risorse dinamiche**

| Livello di servizio | Numero massimo di query simultanee | Slot di concorrenza disponibili | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**Classi di risorse statiche** La tabella seguente illustra il numero massimo di query simultanee e gli slot di concorrenza per ogni[classe di risorse statica](resource-classes-for-workload-management.md).  I valori fanno riferimento al livello di prestazioni ottimizzato per l'elasticità.

| Livello di servizio | Numero massimo di query simultanee | Numero massimo di slot di concorrenza |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Quando viene raggiunta una di queste soglie, le nuove query vengono accodate e vengono eseguite in base al principio FIFO (First-In-First-Out).  Al termine dell'esecuzione delle query e quando il numero di query e di slot risulta inferiore ai limiti, SQL Data Warehouse rilascia le query accodate. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come sfruttare le classi di risorse per ottimizzare ulteriormente il carico di lavoro vedere gli articoli seguenti:
* [Resource classes for workload management](resource-classes-for-workload-management.md) (Classi di risorse per la gestione del carico di lavoro)
* [Analyzing your workload](analyze-your-workload.md) (Analisi del carico di lavoro)

