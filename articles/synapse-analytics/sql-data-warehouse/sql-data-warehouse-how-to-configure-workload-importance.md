---
title: Configurare la priorità del carico di lavoro
description: Informazioni su come impostare la priorità del livello di richiesta in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 83170f4090909e3edcc163312383773d088d8c57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212123"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configurare la priorità del carico di lavoro in Azure Synapse Analytics

Tramite l'impostazione della priorità in Synapse SQL per Azure Synapse, è possibile influenzare la pianificazione delle query. Le query con priorità più elevata verranno pianificate per essere eseguite prima di quelle con priorità più bassa. Per assegnare priorità alle query, è necessario creare un classificatore del carico di lavoro.

## <a name="create-a-workload-classifier-with-importance"></a>Creare un classificatore del carico di lavoro con priorità

Accade spesso che in uno scenario di data warehouse siano presenti utenti, in un sistema sovraccarico, che devono eseguire rapidamente le query.  È possibile che gli utenti siano dirigenti della società che deve eseguire i report, o analisti che eseguono query ad hoc. Per assegnare la priorità alle query viene creato un classificatore del carico di lavoro.  Negli esempi seguenti viene usata la sintassi [create workload classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per creare due classificatori. `Membername` può essere un utente singolo o un gruppo.  Per trovare gli utenti di data warehouse esistenti, eseguire:

```sql
Select name from sys.sysusers
```

Per creare un classificatore del carico di lavoro per un utente con priorità elevata eseguire:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Per creare un classificatore del carico di lavoro per un utente che esegue query ad hoc con priorità più bassa eseguire:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla gestione del carico di lavoro, vedere [Classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md)
- Per altre informazioni sulla priorità, vedere [Priorità del carico di lavoro](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Passare a Gestire e monitorare la priorità del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
