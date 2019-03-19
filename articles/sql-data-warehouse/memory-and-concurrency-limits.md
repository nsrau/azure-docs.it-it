---
title: Limiti di memoria e concorrenza - Azure SQL Data Warehouse | Microsoft Docs
description: Visualizzare i limiti di memoria e concorrenza allocati ai livelli di prestazione e alle classi di risorse in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 141112b8b6b44706a750d8a97780e018d96a5006
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890791"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limiti di memoria e concorrenza per Azure SQL Data Warehouse
Visualizzare i limiti di memoria e concorrenza allocati ai livelli di prestazione e alle classi di risorse in Azure SQL Data Warehouse. Per altre informazioni e per applicare queste funzionalità al piano di gestione del carico di lavoro, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md). 

Attualmente sono disponibili due generazioni con SQL Data Warehouse – Prima e seconda generazione. Si consiglia di sfruttare Seconda generazione di SQL Data Warehouse per ottenere prestazioni ottimali per il carico di lavoro di data warehouse. Seconda generazione introduce una nuova cache NVMe a tinta unita stato disco che mantiene i dati a cui si accede più frequentemente vicino le CPU. Questa operazione rimuove l'I/O remoto per i carichi di lavoro più complessi e intensivi. Oltre alle prestazioni, Seconda generazione offre il massimo livello di scalabilità consentendo di aumentare fino a 30.000 unità Data Warehouse e fornendo un'archiviazione a colonne senza limiti. Verrà comunque supportata la generazione precedente (Prima generazione) di SQL Data Warehouse e verranno mantenute le stesse funzionalità; tuttavia, si consiglia di [eseguire l'aggiornamento alla Seconda generazione](upgrade-to-latest-generation.md) non appena possibile. 

## <a name="data-warehouse-capacity-settings"></a>Impostazioni di capacità di Data Warehouse
Nelle tabelle seguenti viene illustrata la capacità massima per il data warehouse a diversi livelli di prestazioni. Per modificare il livello di prestazioni, vedere [Ridimensionare le risorse di calcolo: portale](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Seconda generazione

Seconda generazione fornisce 2.5 x in più di memoria per ogni query rispetto alla Prima generazione. Questa memoria aggiuntiva aiuta la Seconda generazione a fornire prestazioni veloci.  I livelli di prestazioni per la Seconda generazione sono compresi tra DW100c e DW30000c. 

| Livello di prestazioni | Nodi di calcolo | Distribuzioni per nodo di calcolo | Memoria per data warehouse (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Il DWU della Seconda generazione a elevato utilizzo di calcolo massimo è DW30000c, che ha 60 nodi di calcolo e una distribuzione per ogni nodo di calcolo. Ad esempio, un data warehouse da 600 TB a DW30000c elabora circa 10 TB per ogni nodo di calcolo.

### <a name="gen1"></a>Prima generazione

I livelli di servizio per la Prima generazione sono compresi tra DW100 e DW6000. 

| Livello di prestazioni | Nodi di calcolo | Distribuzioni per nodo di calcolo | Memoria per data warehouse (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1.440                           |

## <a name="concurrency-maximums"></a>Valori massimi di concorrenza
Affinché ogni query abbia risorse sufficienti per operare in modo efficace, SQL Data Warehouse tiene traccia dell'uso delle risorse assegnando slot di concorrenza a ogni query. Il sistema inserisce le query in una coda in base a importanza e slot di concorrenza. Le query in attesa nella coda fino a quando non sono disponibili slot di concorrenza sufficienti. [Importanza](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) e slot di concorrenza determinano priorità della CPU. Per altre informazioni, vedere [Analyze your workload](analyze-your-workload.md) (Analisi del carico di lavoro)

### <a name="gen2"></a>Seconda generazione
 
**Classi di risorse statiche**

La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse statica](resource-classes-for-workload-management.md).  

| Contratto | Numero massimo di query simultanee | Slot di concorrenza disponibili | Slot utilizzati da staticrc10 | Slot utilizzati da staticrc20 | Slot utilizzati da staticrc30 | Slot utilizzati da staticrc40 | Slot utilizzati da staticrc50 | Slot utilizzati da staticrc60 | Slot utilizzati da staticrc70 | Slot utilizzati da staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Classi di risorse dinamiche**

> [!NOTE]
> La classe di risorse smallrc in Seconda generazione aggiunge dinamicamente memoria con l'aumento del livello di servizio e supporta solo al massimo 32 query simultanee per DW1000c e 4 query simultanee per DW100c.  Se la capacità dell'istanza supera il livello DW1500c, la memoria e gli slot di concorrenza usati da smallrc aumentano in proporzione al livello di servizio. 
>
>

La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse dinamica](resource-classes-for-workload-management.md). A differenza della Prima generazione, le classi di risorse dinamiche nella Seconda generazione sono veramente dinamiche.  Seconda generazione usa un'allocazione di percentuale di memoria 3-10-22-70 per le classi di risorse small-medie-grandi-xlarge per tutti i livelli di servizio.

| Contratto | Numero massimo di query simultanee | Slot di concorrenza disponibili | Slot utilizzati da smallrc | Slot utilizzati da mediumrc | Slot utilizzati da largerc | Slot utilizzati da xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |



#### <a name="gen1"></a>Prima generazione

Classi di risorse statiche

La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse statica](resource-classes-for-workload-management.md) nella **Prima generazione**.

| Livello di servizio | Numero massimo di query simultanee | Numero massimo di slot di concorrenza | Slot utilizzati da staticrc10 | Slot utilizzati da staticrc20 | Slot utilizzati da staticrc30 | Slot utilizzati da staticrc40 | Slot utilizzati da staticrc50 | Slot utilizzati da staticrc60 | Slot utilizzati da staticrc70 | Slot utilizzati da staticrc80 |
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
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Classi di risorse dinamiche
> [!NOTE]
> La classe di risorse smallrc in Prima generazione alloca una quantità fissa di memoria per ogni query, allo stesso modo della classe di risorse statiche staticrc10.  Poiché smallrc è statica, ha la capacità di applicare la scalabilità a 128 query simultanee. 
>
>

La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse dinamica](resource-classes-for-workload-management.md) nella **Prima generazione**.

| Livello di servizio | Numero massimo di query simultanee | Slot di concorrenza disponibili | Slot utilizzati da smallrc | Slot utilizzati da mediumrc | Slot utilizzati da largerc | Slot utilizzati da xlargerc |
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
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Quando viene raggiunta una di queste soglie, le nuove query vengono accodate e vengono eseguite in base al principio FIFO (First-In-First-Out).  Al termine dell'esecuzione delle query e quando il numero di query e di slot risulta inferiore ai limiti, SQL Data Warehouse rilascia le query accodate. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come sfruttare le classi di risorse per ottimizzare ulteriormente il carico di lavoro vedere gli articoli seguenti:
* [Resource classes for workload management](resource-classes-for-workload-management.md) (Classi di risorse per la gestione del carico di lavoro)
* [Analyzing your workload](analyze-your-workload.md) (Analisi del carico di lavoro)

