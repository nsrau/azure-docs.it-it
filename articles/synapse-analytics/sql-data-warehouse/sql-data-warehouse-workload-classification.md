---
title: Classificazione del carico di lavoro
description: Linee guida per l'uso della classificazione per gestire la concorrenza, l'importanza e le risorse di calcolo per le query in Azure sinapsi Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 266eebc8322b5fc648180c0524abc973a4b60373
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212378"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Classificazione del carico di lavoro di Azure sinapsi Analytics

Questo articolo illustra il processo di classificazione del carico di lavoro per l'assegnazione di un gruppo di carico di lavoro e l'importanza alle richieste in ingresso con i pool SQL sinapsi in Azure sinapsi.

## <a name="classification"></a>Classificazione

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Classificazione di gestione del carico di lavoro consente di applicare i criteri del carico di lavoro alle richieste tramite l'assegnazione di [classi di risorse](resource-classes-for-workload-management.md#what-are-resource-classes) e [importanza](sql-data-warehouse-workload-importance.md).

Sebbene esistano molti modi per classificare i carichi di lavoro di data warehouse, la classificazione più semplice e più comune è il caricamento e la query. I dati vengono caricati con le istruzioni INSERT, Update e DELETE.  Si esegue una query sui dati usando Selects. Una soluzione di data warehousing avrà spesso un criterio del carico di lavoro per l'attività di caricamento, ad esempio l'assegnazione di una classe di risorse superiore con più risorse. Un criterio del carico di lavoro diverso può essere applicato alle query, ad esempio un'importanza inferiore rispetto alle attività di caricamento.

È anche possibile sottoclassificare i carichi di lavoro di query e di carico. La sottoclassificazione offre un maggiore controllo sui carichi di lavoro. I carichi di lavoro di query possono ad esempio essere costituiti da aggiornamenti del cubo, query del dashboard o query ad hoc. È possibile classificare ognuno di questi carichi di lavoro di query con diverse classi di risorse o impostazioni di importanza. Il carico può anche trarre vantaggio dalla sottoclassificazione. Le trasformazioni di grandi dimensioni possono essere assegnate a classi di risorse più grandi. È possibile usare una maggiore importanza per garantire che i dati delle vendite chiave siano loader prima dei dati meteorologici o di un feed di dati di social networking.

Non tutte le istruzioni sono classificate poiché non richiedono risorse o hanno importanza per influenzare l'esecuzione.  Le istruzioni DBCC Command, BEGIN, COMMIT e ROLLBACK TRANSACTION non sono classificate.

## <a name="classification-process"></a>Processo di classificazione

La classificazione per il pool SQL sinapsi in Azure sinapsi viene eseguita oggi assegnando gli utenti a un ruolo a cui è assegnata una classe di risorse corrispondente usando [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). La possibilità di caratterizzare le richieste oltre un accesso a una classe di risorse è limitata a questa funzionalità. Un metodo più completo per la classificazione è ora disponibile con la sintassi di [creazione del classificatore del carico di lavoro](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  Con questa sintassi, gli utenti del pool SQL sinapsi possono assegnare importanza e la quantità di risorse di sistema assegnate a una richiesta tramite il `workload_group` parametro.

> [!NOTE]
> La classificazione viene valutata in base alle singole richieste. Più richieste in una singola sessione possono essere classificate in modo diverso.

## <a name="classification-weighting"></a>Ponderazione della classificazione

Come parte del processo di classificazione, è necessario ponderare per determinare quale gruppo di carico di lavoro è assegnato.  La ponderazione è basata sui criteri seguenti:

|Parametro di classificatore |Peso   |
|---------------------|---------|
|MEMBERNAME: UTENTE      |64       |
|MEMBERNAME: RUOLO      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Il `membername` parametro è obbligatorio.  Tuttavia, se il membroname specificato è un utente del database anziché un ruolo del database, la ponderazione per l'utente è maggiore e pertanto viene scelto il classificatore.

Se un utente è membro di più ruoli con diverse classi di risorse assegnate o corrispondenti in più classificatori, all'utente verrà attribuita l'assegnazione della classe di risorse più elevata.  Questo comportamento è coerente con il comportamento di assegnazione della classe di risorse esistente.

## <a name="system-classifiers"></a>Classificatori di sistema

Classificazione del carico di lavoro con classificatori del carico di lavoro. I classificatori di sistema mappano le appartenenze ai ruoli della classe di risorse esistenti alle allocazioni di risorse della classe di risorse con priorità normale. I classificatori di sistema non possono essere eliminati. Per visualizzare i classificatori di sistema, è possibile eseguire la query seguente:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Combinazione di assegnazioni di classi di risorse con classificatori

I classificatori di sistema creati per conto dell'utente forniscono un percorso semplice per eseguire la migrazione alla classificazione del carico di lavoro. L'uso di mapping dei ruoli della classe di risorse con precedenza della classificazione può causare una classificazione errata quando si inizia a creare nuovi classificatori con importanza.

Si consideri lo scenario seguente:

- Un data warehouse esistente dispone di un utente di database DBAUser assegnato al ruolo della classe di risorse largerc. L'assegnazione della classe di risorse è stata eseguita con sp_addrolemember.
- Il data warehouse è ora aggiornato con la gestione del carico di lavoro.
- Per testare la nuova sintassi di classificazione, il ruolo del database DBARole (di cui è membro DBAUser), dispone di un classificatore creato per il mapping a mediumrc e a priorità alta.
- Quando DBAUser accede ed esegue una query, la query verrà assegnata a largerc. Poiché un utente ha la precedenza rispetto a un'appartenenza a un ruolo.

Per semplificare la risoluzione dei problemi di classificazione, è consigliabile rimuovere i mapping dei ruoli della classe di risorse durante la creazione dei classificatori del carico di lavoro.  Il codice seguente restituisce le appartenenze ai ruoli della classe di risorse esistenti.  Eseguire [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per ogni nome di membro restituito dalla classe di risorse corrispondente.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla creazione di un classificatore, vedere la pagina relativa alla creazione di un [classificatore del carico di lavoro (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  
- Vedere la Guida introduttiva su come creare un classificatore del carico di lavoro [creare un classificatore del carico di lavoro](quickstart-create-a-workload-classifier-tsql.md).
- Vedere gli articoli sulle procedure per [configurare l'importanza del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md) e come [gestire e monitorare la gestione del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per visualizzare le query e la loro priorità.
