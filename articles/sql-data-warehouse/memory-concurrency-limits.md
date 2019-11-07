---
title: Limiti di memoria e concorrenza
description: Visualizzare i limiti di memoria e concorrenza allocati ai livelli di prestazione e alle classi di risorse in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 10/04/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3611e61b303997a4291f4436403bb0a95e647e65
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686029"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limiti di memoria e concorrenza per Azure SQL Data Warehouse
Visualizzare i limiti di memoria e concorrenza allocati ai livelli di prestazione e alle classi di risorse in Azure SQL Data Warehouse.  

## <a name="data-warehouse-capacity-settings"></a>Impostazioni di capacità di Data Warehouse
Nelle tabelle seguenti viene illustrata la capacità massima per il data warehouse a diversi livelli di prestazioni. Per modificare il livello di prestazioni, vedere [Ridimensionare le risorse di calcolo: portale](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Livelli di servizio

I livelli di servizio sono compresi tra DW100c e DW30000c.

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

Il livello di servizio massimo è DW30000c, che include 60 nodi di calcolo e una distribuzione per ogni nodo di calcolo. Ad esempio, un data warehouse da 600 TB a DW30000c elabora circa 10 TB per ogni nodo di calcolo.

## <a name="concurrency-maximums-for-workload-groups"></a>Valori massimi di concorrenza per i gruppi del carico di lavoro
Con l'introduzione dei gruppi del carico di lavoro-LINK TBD, il concetto di slot di concorrenza non è più applicabile.  Le risorse per ogni richiesta vengono allocate in base a una percentuale e specificate nella definizione del gruppo di carico di lavoro.  Tuttavia, anche con la rimozione degli slot di concorrenza, sono necessarie quantità minime di risorse per le query in base al livello di servizio.  La tabella seguente definisce la quantità minima di risorse necessarie per query tra i livelli di servizio e la concorrenza associata che è possibile ottenere. 

|Contratto|Numero massimo di query simultanee|Supporto minimo% per REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12,5%|
|DW300c|12|8|
|DW400c|16|6,25%|
|DW500c|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1,5%|
|DW5000c|64|1,5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Valori massimi di concorrenza per le classi di risorse
Affinché ogni query abbia risorse sufficienti per operare in modo efficace, SQL Data Warehouse tiene traccia dell'uso delle risorse assegnando slot di concorrenza a ogni query. Il sistema inserisce le query in una coda in base agli slot di importanza e concorrenza. Le query attendono la coda fino a quando non sono disponibili slot di concorrenza sufficienti. Gli slot di [importanza](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) e di concorrenza determinano la priorità della CPU. Per altre informazioni, vedere [Analyze your workload](analyze-your-workload.md) (Analisi del carico di lavoro)

**Classi di risorse statiche**

La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse statica](resource-classes-for-workload-management.md).  

| Contratto | Numero massimo di query simultanee | Slot di concorrenza disponibili | Slot usati da staticrc10 | Slot usati da staticrc20 | Slot usati da staticrc30 | Slot usati da staticrc40 | Slot usati da staticrc50 | Slot usati da staticrc60 | Slot usati da staticrc70 | Slot usati da staticrc80 |
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

La tabella seguente illustra il numero massimo di query simultanee e di slot di concorrenza per ogni [classe di risorse dinamica](resource-classes-for-workload-management.md). Le classi di risorse dinamiche usano un'allocazione percentuale di memoria 3-10-22-70 per le classi di risorse XLarge di piccole e medie dimensioni in tutti i livelli di servizio.

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


Quando non sono disponibili slot di concorrenza sufficienti per avviare l'esecuzione della query, le query vengono accodate ed eseguite in base alla priorità.  Se è presente un'importanza equivalente, le query vengono eseguite in base al principio First-in, First-out.  Al termine dell'esecuzione delle query e quando il numero di query e di slot risulta inferiore ai limiti, SQL Data Warehouse rilascia le query accodate. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come sfruttare le classi di risorse per ottimizzare ulteriormente il carico di lavoro vedere gli articoli seguenti:
* [Resource classes for workload management](resource-classes-for-workload-management.md) (Classi di risorse per la gestione del carico di lavoro)
* [Analyzing your workload](analyze-your-workload.md) (Analisi del carico di lavoro)