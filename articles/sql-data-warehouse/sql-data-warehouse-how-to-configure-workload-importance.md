---
title: Configurare la priorità del carico di lavoro
description: Informazioni su come impostare l'importanza del livello di richiesta in Azure sinapsi Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196798"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configurare l'importanza del carico di lavoro in Azure sinapsi Analytics

L'impostazione dell'importanza in SQL Analytics per la sinapsi di Azure consente di influenzare la pianificazione delle query. Le query con priorità più elevata verranno pianificate per l'esecuzione prima di quelle con priorità più bassa. Per assegnare importanza alle query, è necessario creare un classificatore del carico di lavoro.

## <a name="create-a-workload-classifier-with-importance"></a>Creare un classificatore del carico di lavoro con importanza

Spesso in uno scenario di data warehouse sono disponibili utenti che necessitano di una rapida esecuzione delle query.  È possibile che l'utente sia dirigenti della società che deve eseguire i report o che l'utente sia un analista che esegue una query ad hoc. Viene creato un classificatore del carico di lavoro per assegnare importanza a una query.  Negli esempi riportati di seguito viene usata la nuova sintassi [create workload Classificator](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) per creare due classificatori.  MemberName può essere un singolo utente o un gruppo. Le classificazioni di singoli utenti hanno la precedenza sulle classificazioni dei ruoli. Per trovare gli utenti di data warehouse esistenti, eseguire:

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
