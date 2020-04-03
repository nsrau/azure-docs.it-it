---
title: Configurare la priorità del carico di lavoro
description: Informazioni su come impostare l'importanza a livello di richiesta in Azure Synapse Analytics.Learn how to set request level importance in Azure Synapse Analytics.
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
ms.openlocfilehash: 4aa0e7da1e77334a8b9048dab4a461133f220bd1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582457"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Configurare l'importanza del carico di lavoro in Azure Synapse AnalyticsConfigure workload importance in Azure Synapse Analytics

L'impostazione dell'importanza in Synapse SQL for Azure Synapse consente di influenzare la pianificazione delle query. Le query con maggiore importanza verranno pianificate per l'esecuzione prima delle query con priorità inferiore. Per assegnare importanza alle query, è necessario creare un classificatore del carico di lavoro.

## <a name="create-a-workload-classifier-with-importance"></a>Creare un classificatore di carico di lavoro con importanzaCreate a Workload Classifier with Importance

Spesso in uno scenario di data warehouse si dispone di utenti che necessitano delle query per essere eseguite rapidamente.  L'utente potrebbe essere dirigenti della società che hanno bisogno di eseguire report o l'utente potrebbe essere un analista che esegue una query ad hoc. Creare un classificatore di carico di lavoro per assegnare importanza a una query.  Negli esempi riportati di seguito viene utilizzata la nuova sintassi del [classificatore del carico di lavoro](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) di creazione per creare due classificatori.  Membername può essere un singolo utente o un gruppo. Le classificazioni dei singoli utenti hanno la precedenza sulle classificazioni dei ruoli. Per trovare gli utenti del data warehouse esistenti, eseguire:To find existing data warehouse users, run:

```sql
Select name from sys.sysusers
```

Per creare un classificatore di carico di lavoro, per un utente con maggiore importanza eseguire:To create a workload classifier, for a user with higher importance run:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Per creare un classificatore del carico di lavoro per un utente che esegue query ad hoc con priorità inferiore eseguire:To create a workload classifier for a user running adhoc queries with lower importance run:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulla gestione del carico di lavoro, vedere [Classificazione del carico di lavoroFor more](sql-data-warehouse-workload-classification.md) information about workload management, see Workload Classification
- Per altre informazioni sull'importanza, vedere [Importanza del carico di lavoroFor more](sql-data-warehouse-workload-importance.md) information on Importance, see Workload Importance

> [!div class="nextstepaction"]
> [Passare a Gestire e monitorare l'importanza del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
