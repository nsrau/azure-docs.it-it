---
title: Convertire una classe di risorse in un gruppo di carico di lavoro
description: Informazioni su come creare un gruppo di carico di lavoro simile a una classe di risorse in Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8032e8809f7849ab7497da7821788c017adff12d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212055"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Convertire le classi di risorse in gruppi di carico di lavoro

I gruppi di carico di lavoro forniscono un meccanismo per isolare e contenere le risorse di sistema.  I gruppi di carico di lavoro consentono inoltre di impostare regole di esecuzione per le richieste eseguite al loro interno.  Una regola di esecuzione del timeout delle query consente l'annullamento delle query con eccessivo tempo di esecuzione senza intervento da parte dell'utente.  Questo articolo illustra come usare una classe di risorse esistente e creare un gruppo di carico di lavoro con una configurazione simile.  Inoltre, viene aggiunta una regola di timeout delle query facoltativa.

> [!NOTE]
> Per indicazioni sull'uso di gruppi di carico di lavoro e classi di risorse contemporaneamente, vedere la sezione [Combinazione di assegnazioni di classi di risorse con classificatori](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) dell'articolo [Classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md).

## <a name="understanding-the-existing-resource-class-configuration"></a>Informazioni sulla configurazione delle classi di risorse esistenti

I gruppi di carico di lavoro richiedono un parametro denominato `REQUEST_MIN_RESOURCE_GRANT_PERCENT` che specifica la percentuale di risorse di sistema complessive allocate per ogni richiesta.  L'allocazione delle risorse viene eseguita per [classi di risorse](resource-classes-for-workload-management.md#what-are-resource-classes) allocando gli slot di concorrenza.  Per determinare il valore da specificare per `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, usare la DMV sys.dm_workload_management_workload_groups_stats <link tbd>.  Ad esempio, la query seguente restituisce un valore che può essere usato per il parametro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` per creare un gruppo di carico di lavoro simile a staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> I gruppi di carico di lavoro operano in base alla percentuale di risorse di sistema complessive.  

Poiché i gruppi di carico di lavoro operano in base a una percentuale di risorse di sistema complessive, quando si ridimensiona il carico di lavoro, cambia la percentuale di risorse allocate alle classi di risorse statiche rispetto alle risorse di sistema complessive.  Ad esempio, staticrc40 con DW1000c alloca il 19,2% delle risorse di sistema complessive.  Con DW2000c, viene allocato il 9,6%.  Questo modello è simile se si vuole ridimensionare il carico di lavoro per la concorrenza rispetto all'allocazione di più risorse per richiesta.

## <a name="create-workload-group"></a>Creare il gruppo di carico di lavoro

Con il valore noto di `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, è possibile usare la sintassi CREATE WORKLOAD GROUP <link> per creare il gruppo di carico di lavoro.  È possibile specificare facoltativamente un valore di `MIN_PERCENTAGE_RESOURCE` maggiore di zero per isolare le risorse per il gruppo di carico di lavoro.  È anche possibile specificare facoltativamente un `CAP_PERCENTAGE_RESOURCE` minore di 100 per limitare la quantità di risorse che il gruppo di carico di lavoro può utilizzare.  

L'esempio seguente imposta `MIN_PERCENTAGE_RESOURCE` per dedicare il 9,6% delle risorse di sistema a `wgDataLoads` e garantisce che una query possa essere eseguita sempre.  Inoltre, `CAP_PERCENTAGE_RESOURCE` è impostato su 38,4% e limita il gruppo di carico di lavoro a quattro richieste simultanee.  Impostando il parametro `QUERY_EXECUTION_TIMEOUT_SEC` su 3600, tutte le query eseguite per più di un'ora verranno annullate automaticamente.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Creare il classificatore

In precedenza, il mapping delle query alle classi di risorse veniva eseguito con [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Per ottenere la stessa funzionalità ed eseguire il mapping delle richieste ai gruppi di carico di lavoro, usare la sintassi [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  L'uso di sp_addrolemember consentiva solo di eseguire il mapping delle risorse a una richiesta in base a un account di accesso.  Un classificatore fornisce ulteriori opzioni oltre all'account di accesso, ad esempio:
    - label
    - sessione
    - time Nell'esempio seguente vengono assegnate query dall'account di accesso `AdfLogin` che hanno anche l'etichetta [OPTION LABEL](sql-data-warehouse-develop-label.md) impostata su `factloads` al gruppo di carico di lavoro `wgDataLoads` creato in precedenza.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Eseguire test con una query di esempio

Di seguito sono riportate una query di esempio e una query DMV per verificare che il gruppo di carico di lavoro e il classificatore siano configurati correttamente.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Passaggi successivi

- [Isolamento del carico di lavoro](sql-data-warehouse-workload-isolation.md)
- [Guida pratica: creare un gruppo di carico di lavoro](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
