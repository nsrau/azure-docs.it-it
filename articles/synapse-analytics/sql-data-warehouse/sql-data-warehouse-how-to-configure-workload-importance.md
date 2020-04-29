---
title: Configurare la priorità del carico di lavoro
description: Informazioni su come impostare l'importanza del livello di richiesta in Azure sinapsi Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633355"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configurare l'importanza del carico di lavoro in Azure sinapsi Analytics

L'impostazione dell'importanza in sinapsi SQL per la sinapsi di Azure consente di influenzare la pianificazione delle query. Le query con priorità più elevata verranno pianificate per l'esecuzione prima di quelle con priorità più bassa. Per assegnare importanza alle query, è necessario creare un classificatore del carico di lavoro.

## <a name="create-a-workload-classifier-with-importance"></a>Creare un classificatore del carico di lavoro con importanza

Spesso in uno scenario di data warehouse sono disponibili utenti che necessitano di una rapida esecuzione delle query.  È possibile che l'utente sia dirigenti della società che deve eseguire i report o che l'utente sia un analista che esegue una query ad hoc. Viene creato un classificatore del carico di lavoro per assegnare importanza a una query.  Negli esempi riportati di seguito viene usata la nuova sintassi [create workload Classificator](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per creare due classificatori. `Membername`può essere un singolo utente o un gruppo. Le classificazioni di singoli utenti hanno la precedenza sulle classificazioni dei ruoli. Per trovare gli utenti di data warehouse esistenti, eseguire:

```sql
Select name from sys.sysusers
```

Per creare un classificatore del carico di lavoro, per un utente con maggiore importanza eseguire:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Per creare un classificatore del carico di lavoro per un utente che esegue query ad hoc con priorità più bassa:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla gestione del carico di lavoro, vedere [classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md)
- Per ulteriori informazioni sull'importanza, vedere [priorità del carico di lavoro](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Passare a gestire e monitorare l'importanza del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
