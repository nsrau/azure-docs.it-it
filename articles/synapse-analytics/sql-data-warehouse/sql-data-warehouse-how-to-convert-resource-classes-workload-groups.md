---
title: Convertire la classe di risorse in un gruppo di carico di lavoroConvert resource class to a workload group
description: Informazioni su come creare un gruppo di carico di lavoro simile a una classe di risorse in Azure SQL Data Warehouse.Learn how to create a workload group that is similar to a resource class in Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a249dfc4f04fbd7b6b73a0e9f37d53106bf82efd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633348"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Convertire le classi di risorse in gruppi di carico di lavoroConvert Resource Classes to Workload Groups

I gruppi di carico di lavoro forniscono un meccanismo per isolare e contenere le risorse di sistema.  Inoltre, i gruppi di carico di lavoro consentono di impostare le regole di esecuzione per le richieste in esecuzione in essi.  Una regola di esecuzione del timeout delle query consente di annullare le query di runaway senza l'intervento dell'utente.  Questo articolo illustra come accettare una classe di risorse esistente e creare un gruppo di carico di lavoro con una configurazione simile.  Inoltre, viene aggiunta una regola di timeout di query facoltativa.

## <a name="understanding-the-existing-resource-class-configuration"></a>Informazioni sulla configurazione della classe di risorse esistenteUnderstanding the existing resource class configuration

I gruppi di `REQUEST_MIN_RESOURCE_GRANT_PERCENT` carico di lavoro richiedono un parametro denominato che specifica la percentuale di risorse di sistema complessive allocate per richiesta.  L'allocazione delle risorse viene eseguita per le [classi di risorse](resource-classes-for-workload-management.md#what-are-resource-classes) allocando gli slot di concorrenza.  Per determinare il valore `REQUEST_MIN_RESOURCE_GRANT_PERCENT`da specificare <link tbd> per , utilizzare la DMV sys.dm_workload_management_workload_groups_stats.  Ad esempio, la query di query seguente restituisce un valore che può essere utilizzato per il parametro per creare un gruppo di carico di lavoro simile a staticrc40.For example, the below query query returns a value that can be used for the `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parameter to create a workload group similar to staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> I gruppi di carico di lavoro operano in base alla percentuale di risorse di sistema complessive.  

Poiché i gruppi di carico di lavoro operano in base alla percentuale delle risorse di sistema complessive, man mano che si aumenta e aumentala la percentuale di risorse allocate alle classi di risorse statiche rispetto alle modifiche complessive delle risorse di sistema.  Ad esempio, staticrc40 in DW1000c alloca il 9,6% delle risorse di sistema complessive.  A DW2000c viene allocato il 19,2%.  Questo modello è simile se si desidera aumentare la scalabilità verticale per la concorrenza e allocare più risorse per richiesta.

## <a name="create-workload-group"></a>Crea gruppo di carico di lavoroCreate Workload Group

Con il `REQUEST_MIN_RESOURCE_GRANT_PERCENT`noto , è <link> possibile utilizzare la sintassi CREATE WORKLOAD GROUP per creare il gruppo del carico di lavoro.  Facoltativamente, è `MIN_PERCENTAGE_RESOURCE` possibile specificare un oggetto maggiore di zero per isolare le risorse per il gruppo di carico di lavoro.  Inoltre, è possibile `CAP_PERCENTAGE_RESOURCE` specificare facoltativamente meno di 100 per limitare la quantità di risorse che il gruppo di carico di lavoro può utilizzare.  

Nell'esempio seguente `MIN_PERCENTAGE_RESOURCE` viene impostato il per dedicare il `wgDataLoads` 9,6% delle risorse di sistema a e garantisce che una query sarà in grado di eseguire tutte le volte.  Inoltre, `CAP_PERCENTAGE_RESOURCE` è impostato su 38.4% e limita questo gruppo di carico di lavoro a quattro richieste simultanee.  Impostando il `QUERY_EXECUTION_TIMEOUT_SEC` parametro su 3600, tutte le query eseguite per più di 1 ora verranno annullate automaticamente.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Creare il classificatore

In precedenza, il mapping delle query alle classi di risorse veniva eseguito con [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Per ottenere la stessa funzionalità ed eseguire il mapping delle richieste ai gruppi di carico di lavoro, utilizzare la sintassi [CREATE WORKLOAD CLASSIFIER.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)  L'utilizzo di sp_addrolemember solo consentito di eseguire il mapping delle risorse a una richiesta in base a un account di accesso.  Un classificatore fornisce opzioni aggiuntive oltre all'accesso, ad esempio:A classifier provides additional options besides login, such as:
    - label
    - sessione
    - tempo Nell'esempio seguente vengono `AdfLogin` assegnate query dall'account `factloads` di accesso `wgDataLoads` per le cui [opzioni sono](sql-data-warehouse-develop-label.md) impostate anche per il gruppo di carico di lavoro creato in precedenza.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Eseguire il test con una query di esempioTest with a sample query

Di seguito è riportata una query di esempio e una query DMV per garantire che il gruppo di carico di lavoro e il classificatore siano configurati correttamente.

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

- [Isolamento del carico di lavoroWorkload Isolation](sql-data-warehouse-workload-isolation.md)
- [Creazione di un collegamento al gruppo del carico di lavoroHow-To Create a Workload Group-link](quickstart-configure-workload-isolation-tsql.md)
