---
title: Configurare la priorità del carico di lavoro in Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come impostare la priorità a livello di richiesta.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7121d2f022f9c9a5bbc02f04955d2857f3ec986f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241228"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Configurare la priorità del carico di lavoro in Azure SQL Data Warehouse

L'impostazione di priorità in SQL Data Warehouse consente di influenzare la pianificazione delle query. Le query con un'importanza superiore verranno pianificate da eseguire prima le query con priorità inferiore. Per assegnare priorità alle query, è necessario creare un classificatore di carico di lavoro.

## <a name="create-a-workload-classifier-with-importance"></a>Creare un classificatore di carico di lavoro con priorità

Spesso in uno scenario di data warehouse sono presenti utenti che necessitano di eseguire rapidamente le query.  L'utente potrebbe essere dirigenti della società che necessitano di eseguire i report oppure l'utente potrebbe essere un analista che esegue una query ad hoc. Si crea un classificatore di carico di lavoro per assegnare una priorità per una query.  Gli esempi seguenti usano il nuovo [creare carichi di lavoro classificatore](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) sintassi per creare due classificatori.  Nome membro può essere un singolo utente o un gruppo. Le classificazioni di singoli utenti hanno la precedenza sulle classificazioni di ruolo. Per trovare gli utenti di warehouse dati esistenti, eseguire:

```sql
Select name from sys.sysusers
```

Per creare un classificatore di carico di lavoro, eseguire per un utente con un'importanza superiore:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Per creare un classificatore di carico di lavoro per un utente di eseguire query ad hoc con importanza inferiore eseguire:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Fasi successive
- Per altre informazioni sulla gestione del carico di lavoro, vedere [classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md)
- Per altre informazioni sull'importanza, vedere [importanza del carico di lavoro](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Passare da gestire e monitorare l'importanza del carico di lavoro ](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
