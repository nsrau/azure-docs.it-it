---
title: Ottimizzare cache Gen2 | Microsoft Docs
description: Altre informazioni sul monitoraggio della cache Gen2 usando il portale di Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 26791aecb2ca57b31358d3385d07230c73c84904
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61474420"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Come monitorare la cache Gen2
L'architettura di archiviazione Gen2 automaticamente livella i segmenti columnstore più frequentemente sottoposti a query in una cache che si trova in unità SSD basate su NVMe, progettata per i data warehouse Gen2. Vengono realizzate prestazioni migliori quando le query recuperano i segmenti che si trovano nella cache. Questo articolo descrive come monitorare e risolvere i problemi di prestazione delle query lente, determinando se il carico di lavoro sta sfruttando in modo ottimale la cache Gen2.  
## <a name="troubleshoot-using-the-azure-portal"></a>Risolvere i problemi usando il portale di Azure
È possibile usare Monitoraggio di Azure per visualizzare la metrica della cache Gen2 per risolvere i problemi di prestazione delle query. Innanzitutto andare sul portale di Azure e fare clic su Monitoraggio:

![Monitoraggio di Azure](./media/sql-data-warehouse-cache-portal/cache_0.png)

Selezionare il pulsante metrica e completare **Sottoscrizione**, **Risorsa**, **gruppo**, **Tipo di risorsa** e **Nome risorsa** del data warehouse.

Le metriche chiave per la risoluzione dei problemi della cache Gen2 sono **Percentuale riscontro nella cache** e **Percentuale della cache usata**. Configurare il grafico della metrica di Azure per visualizzare queste due metriche.

![Metrica della cache](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Riscontro nella cache e percentuale usata
La matrice seguente descrive scenari in base ai valori della metrica della cache:

|                                | **Elevata percentuale di riscontro nella cache** | **Bassa percentuale di riscontro nella cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Elevata percentuale usata della cache** |          Scenario 1           |          Scenario 2          |
| **Bassa percentuale usata della cache**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** La cache viene usata in modo ottimale. [Risoluzione dei problemi](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) di altre aree che possono rallentare le query.

**Scenario 2:** Il set di dati di lavoro corrente non può entrare nella cache, ciò comporta una bassa percentuale di riscontro nella cache dovuta a letture fisiche. Si consiglia di aumentare il livello della prestazione ed eseguire nuovamente il carico di lavoro per popolare la cache.

**Scenario 3:** È probabile che la query venga eseguita lentamente per motivi non relativi alla cache. [Risoluzione dei problemi](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) di altre aree che possono rallentare le query. È inoltre possibile considerare di [ridurre l'istanza](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) per ridurre le dimensioni della cache e i costi. 

**Scenario 4:** La cache vuota potrebbe essere il motivo della lentezza della query. Si consiglia di eseguire nuovamente la query poiché il set di dati di lavoro dovrebbe ora essere memorizzato nella cache. 

**Importante: se la percentuale di riscontro nella cache o la percentuale usata della cache non viene aggiornata dopo aver eseguito nuovamente il carico di lavoro, il set di lavoro può già trovarsi in memoria. Tenere presente che solo le tabelle columnstore cluster vengono memorizzate nella cache.**

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'ottimizzazione delle prestazioni delle query generali, vedere [Monitoraggio dell'esecuzione delle query](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
