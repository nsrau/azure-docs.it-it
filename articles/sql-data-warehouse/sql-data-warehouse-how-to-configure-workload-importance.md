---
title: Configurare la priorità del carico di lavoro
description: Informazioni su come impostare l'importanza del livello di richiesta.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692699"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Configurare l'importanza del carico di lavoro in Azure SQL Data Warehouse

L'impostazione dell'importanza nella SQL Data Warehouse consente di influenzare la pianificazione delle query. Le query con priorità più elevata verranno pianificate per l'esecuzione prima di quelle con priorità più bassa. Per assegnare importanza alle query, è necessario creare un classificatore del carico di lavoro.

## <a name="create-a-workload-classifier-with-importance"></a>Creare un classificatore del carico di lavoro con importanza

Spesso in uno scenario di data warehouse sono disponibili utenti che necessitano di una rapida esecuzione delle query.  È possibile che l'utente sia dirigenti della società che deve eseguire i report o che l'utente sia un analista che esegue una query ad hoc. Viene creato un classificatore del carico di lavoro per assegnare importanza a una query.  Negli esempi riportati di seguito viene usata la nuova sintassi [create workload Classificator](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) per creare due classificatori.  MemberName può essere un singolo utente o un gruppo. Le classificazioni di singoli utenti hanno la precedenza sulle classificazioni dei ruoli. Per trovare gli utenti di data warehouse esistenti, eseguire:

```sql
Select name from sys.sysusers
```

Per creare un classificatore del carico di lavoro, per un utente con maggiore importanza eseguire:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Per creare un classificatore del carico di lavoro per un utente che esegue query ad hoc con priorità più bassa:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni sulla gestione del carico di lavoro, vedere [classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md)
- Per ulteriori informazioni sull'importanza, vedere [priorità del carico di lavoro](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Passare a gestire e monitorare l'importanza del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
