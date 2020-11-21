---
title: Ottimizzare la cache di Gen2
description: Altre informazioni sul monitoraggio della cache Gen2 usando il portale di Azure.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 041751b5b23dbb3153f1ae638303579a860c0e5b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020164"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Come monitorare la cache adattiva

In questo articolo viene descritto come monitorare e risolvere i problemi relativi alle prestazioni di esecuzione delle query per determinare se il carico di lavoro utilizza in modo ottimale la cache adattiva per i pool SQL dedicati.

L'architettura di archiviazione del pool SQL dedicata esegue automaticamente il livello dei segmenti columnstore più frequenti in una cache che risiede su SSD basati su NVMe. Se le query recuperano i segmenti che risiedono nella cache, si otterranno prestazioni migliori.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Risolvere i problemi usando il portale di Azure

È possibile usare monitoraggio di Azure per visualizzare le metriche della cache per risolvere i problemi relativi alle prestazioni delle query. Passare prima al portale di Azure e fare clic su **monitoraggio**, **metriche** e **selezionare un ambito**:

![Screenshot Mostra selezionare un ambito selezionato da metriche nel portale di Azure.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Utilizzare le barre di ricerca ed elenco a discesa per individuare il pool SQL dedicato. Quindi selezionare Applica.

![Screenshot mostra il riquadro selezionare un ambito in cui è possibile selezionare il data warehouse.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Le metriche chiave per la risoluzione dei problemi relativi alla cache sono la percentuale di **riscontri nella** cache e la **percentuale di utilizzo** della cache. Selezionare **percentuale riscontri cache** , quindi usare il pulsante **Aggiungi metrica** per aggiungere la **percentuale di utilizzo della cache**. 

![Metrica della cache](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Riscontro nella cache e percentuale usata

La matrice seguente descrive scenari in base ai valori della metrica della cache:

|                                | **Elevata percentuale di riscontro nella cache** | **Bassa percentuale di riscontro nella cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Elevata percentuale usata della cache** |          Scenario 1           |          Scenario 2          |
| **Bassa percentuale usata della cache**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:**. Uso ottimale della cache. [Risolvere i problemi](sql-data-warehouse-manage-monitor.md) di altre aree che potrebbero rallentare le query.

**Scenario 2:**. Il set di dati di lavoro corrente non può entrare nella cache, ciò comporta una bassa percentuale di riscontro nella cache dovuta a letture fisiche. Si consiglia di aumentare il livello della prestazione ed eseguire nuovamente il carico di lavoro per popolare la cache.

**Scenario 3:** È probabile che la query venga eseguita lentamente per motivi non relativi alla cache. [Risolvere i problemi](sql-data-warehouse-manage-monitor.md) di altre aree che potrebbero rallentare le query. È inoltre possibile considerare di [ridurre l'istanza](sql-data-warehouse-manage-monitor.md) per ridurre le dimensioni della cache e i costi. 

**Scenario 4:** Si è verificato un cold cache che potrebbe essere il motivo della lentezza della query. Si consiglia di eseguire nuovamente la query poiché il set di dati di lavoro dovrebbe ora essere memorizzato nella cache. 

> [!IMPORTANT]
> Se la percentuale di riscontri nella cache o la percentuale di utilizzo della cache non viene aggiornata dopo aver rieseguito il carico di lavoro, è possibile che il working set si trovi già nella memoria. Solo le tabelle columnstore cluster vengono memorizzate nella cache.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'ottimizzazione delle prestazioni delle query generali, vedere [Monitoraggio dell'esecuzione delle query](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
