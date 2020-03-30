---
title: Ottimizzare la cache Gen2Optimize your Gen2 cache
description: Altre informazioni sul monitoraggio della cache Gen2 usando il portale di Azure.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bb5560164af2b573e6aaffd4e4c62bbe0dc24a51
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350413"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Come monitorare la cache Gen2

Questo articolo descrive come monitorare e risolvere i problemi di prestazione delle query lente, determinando se il carico di lavoro sta sfruttando in modo ottimale la cache Gen2.

L'architettura di archiviazione Gen2 automaticamente livella i segmenti columnstore più frequentemente sottoposti a query in una cache che si trova in unità SSD basate su NVMe, progettata per i data warehouse Gen2. Vengono realizzate prestazioni migliori quando le query recuperano i segmenti che si trovano nella cache.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Risolvere i problemi usando il portale di Azure

È possibile usare Monitoraggio di Azure per visualizzare la metrica della cache Gen2 per risolvere i problemi di prestazione delle query. Per prima cosa vai al portale di Azure e fai clic su **Monitora**, **Metriche** e **- Seleziona un ambito**:

![Monitoraggio di Azure](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Utilizzare le barre di ricerca e a discesa per individuare il data warehouse. Quindi selezionare Applica.

![Monitoraggio di Azure](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Le metriche chiave per la risoluzione dei problemi della cache Gen2 sono **Percentuale riscontro nella cache** e **Percentuale della cache usata**. Selezionare **Percentuale riscontri cache,** quindi utilizzare il pulsante **Aggiungi metrica** per aggiungere **la percentuale di cache utilizzata.** 

![Metrica della cache](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Riscontro nella cache e percentuale usata

La matrice seguente descrive scenari in base ai valori della metrica della cache:

|                                | **Elevata percentuale di riscontro nella cache** | **Bassa percentuale di riscontro nella cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Elevata percentuale usata della cache** |          Scenario 1           |          Scenario 2          |
| **Bassa percentuale usata della cache**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:**. Uso ottimale della cache. [Risolvere i problemi relativi](sql-data-warehouse-manage-monitor.md) ad altre aree che potrebbero rallentare le query.

**Scenario 2:**. Il set di dati di lavoro corrente non può entrare nella cache, ciò comporta una bassa percentuale di riscontro nella cache dovuta a letture fisiche. Si consiglia di aumentare il livello della prestazione ed eseguire nuovamente il carico di lavoro per popolare la cache.

**Scenario 3:** È probabile che la query venga eseguita lentamente per motivi non relativi alla cache. [Risolvere i problemi relativi](sql-data-warehouse-manage-monitor.md) ad altre aree che potrebbero rallentare le query. È inoltre possibile considerare di [ridurre l'istanza](sql-data-warehouse-manage-monitor.md) per ridurre le dimensioni della cache e i costi. 

**Scenario 4:** Si è verificato un cold cache che potrebbe essere il motivo della lentezza della query. Si consiglia di eseguire nuovamente la query poiché il set di dati di lavoro dovrebbe ora essere memorizzato nella cache. 

> [!IMPORTANT]
> Se la percentuale di riscontri nella cache o la percentuale di utilizzo della cache non viene aggiornata dopo la riesecuzione del carico di lavoro, il working set potrebbe già risiedire in memoria. Vengono memorizzate nella cache solo le tabelle columnstore cluster.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'ottimizzazione delle prestazioni delle query generali, vedere [Monitoraggio dell'esecuzione delle query](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
