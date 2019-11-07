---
title: Convertire una classe di risorse in un gruppo di carico di lavoro
description: Informazioni su come creare un gruppo di carico di lavoro simile a una classe di risorse in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685700"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Convertire le classi di risorse in gruppi del carico di lavoro
I gruppi del carico di lavoro forniscono un meccanismo per isolare e contenere le risorse di sistema.  I gruppi del carico di lavoro consentono inoltre di impostare regole di esecuzione per le richieste in esecuzione.  Una regola di esecuzione del timeout di query consente l'annullamento di query runaway senza l'intervento dell'utente.  Questo articolo illustra come usare una classe di risorse esistente e creare un gruppo di carico di lavoro con una configurazione simile.  Inoltre, viene aggiunta una regola di timeout query facoltativa.

## <a name="understanding-the-existing-resource-class-configuration"></a>Informazioni sulla configurazione della classe di risorse esistente
I gruppi del carico di lavoro richiedono un parametro denominato `REQUEST_MIN_RESOURCE_GRANT_PERCENT` che specifica la percentuale di risorse di sistema complessive allocate per ogni richiesta.  L'allocazione delle risorse viene eseguita per [le classi di risorse](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) allocando gli slot di concorrenza.  Per determinare il valore da specificare per `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, utilizzare la DMV sys. dm _workload_management_workload_groups_stats <link tbd>.  Ad esempio, la query di query seguente restituisce un valore che può essere usato per il parametro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` per creare un gruppo di carico di lavoro simile a staticrc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> I gruppi del carico di lavoro funzionano in base alla percentuale di risorse complessive del sistema.  

Poiché i gruppi del carico di lavoro operano in base a una percentuale di risorse di sistema complessive, quando si aumenta e si riduce la percentuale di risorse allocate alle classi di risorse statiche rispetto alle risorse complessive del sistema.  Ad esempio, staticrc40 in compreso dw1000c alloca il 9,6% delle risorse complessive del sistema.  Alle DW2000c, viene allocato il 19,2%.  Questo modello è simile se si desidera aumentare le prestazioni per la concorrenza rispetto all'allocazione di più risorse per ogni richiesta.   

## <a name="create-workload-group"></a>Crea gruppo di carico di lavoro
Con il `REQUEST_MIN_RESOURCE_GRANT_PERCENT`noto, è possibile utilizzare la sintassi crea gruppo di carico di lavoro <link> per creare il gruppo del carico di lavoro.  Facoltativamente, è possibile specificare un `MIN_PERCENTAGE_RESOURCE` maggiore di zero per isolare le risorse per il gruppo del carico di lavoro.  È anche possibile specificare facoltativamente `CAP_PERCENTAGE_RESOURCE` inferiore a 100 per limitare la quantità di risorse che il gruppo di carico di lavoro può utilizzare.  

L'esempio seguente imposta il `MIN_PERCENTAGE_RESOURCE` per dedicare il 9,6% delle risorse di sistema a `wgDataLoads` e garantisce che una query possa essere eseguita sempre.  Inoltre, `CAP_PERCENTAGE_RESOURCE` è impostato su 38,4% e limita il gruppo del carico di lavoro a quattro richieste simultanee.  Impostando il parametro `QUERY_EXECUTION_TIMEOUT_SEC` su 3600, tutte le query eseguite per più di un'ora verranno annullate automaticamente.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Creare il classificatore
In precedenza, il mapping delle query alle classi di risorse veniva eseguito con [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Per ottenere la stessa funzionalità ed eseguire il mapping delle richieste ai gruppi del carico di lavoro, usare la sintassi di [classificazione di creazione del carico di lavoro](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) .  L'uso di sp_addrolemember consente solo di eseguire il mapping delle risorse a una richiesta basata su un account di accesso.  Un classificatore fornisce opzioni aggiuntive oltre all'account di accesso, ad esempio:
    - label
    - session
    - ora nell'esempio seguente vengono assegnate query dall'account di accesso di `AdfLogin` in cui l' [opzione etichetta](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) è impostata su `factloads` al gruppo di carico di lavoro `wgDataLoads` creato in precedenza.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>Eseguire test con una query di esempio
Di seguito è riportata una query di esempio e una query DMV per verificare che il gruppo di carico di lavoro e il classificatore siano configurati correttamente.

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

Isolamento del carico di lavoro-collegamento da definire come creare un gruppo di carico di lavoro-collegamento TBD