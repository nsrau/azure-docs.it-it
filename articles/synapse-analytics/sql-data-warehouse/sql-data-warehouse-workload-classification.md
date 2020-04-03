---
title: Classificazione del carico di lavoro
description: Indicazioni per l'uso della classificazione per gestire la concorrenza, l'importanza e le risorse di calcolo per le query in Analisi synapse di Azure.Guidance for using classification to manage concurrency, importance, and compute resources for queries in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 67f863826a2e9eb1bffcb316754ad5c40a2f2bb1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583143"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Classificazione del carico di lavoro di Azure Synapse AnalyticsAzure Synapse Analytics workload classification

Questo articolo illustra il processo di classificazione del carico di lavoro di assegnazione di un gruppo di carico di lavoro e importanza alle richieste in ingresso con i pool SQL Synapse in Azure Synapse.This article explains the workload classification process of assigning a workload group and importance to incoming requests with Synapse SQL pools in Azure Synapse.

## <a name="classification"></a>Classificazione

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

La classificazione della gestione del carico di lavoro consente di applicare i criteri del carico di lavoro alle richieste tramite l'assegnazione di [classi di risorse](resource-classes-for-workload-management.md#what-are-resource-classes) e [l'importanza.](sql-data-warehouse-workload-importance.md)

Sebbene esistano molti modi per classificare i carichi di lavoro di data warehousing, la classificazione più semplice e più comune è il carico e la query. I dati vengono caricati con le istruzioni insert, update ed delete.  Eseguire una query sui dati utilizzando le selezioni. Una soluzione di data warehousing avrà spesso un criterio del carico di lavoro per l'attività di carico, ad esempio l'assegnazione di una classe di risorse superiore con più risorse. Un criterio del carico di lavoro diverso può essere applicato alle query, ad esempio un'importanza inferiore rispetto alle attività di carico.

È inoltre possibile classificare i carichi di lavoro di carico e query. La sottoclassificazione offre un maggiore controllo dei carichi di lavoro. Ad esempio, i carichi di lavoro di query possono essere costituiti da aggiornamenti di cubi, query del dashboard o query ad hoc. È possibile classificare ognuno di questi carichi di lavoro di query con classi di risorse o impostazioni di importanza diverse. Il carico può anche trarre vantaggio dalla sottoclassificazione. Le trasformazioni di grandi dimensioni possono essere assegnate a classi di risorse più grandi. Una maggiore importanza può essere utilizzata per garantire che i dati di vendita chiave vengano inseriti prima dei dati meteorologici o di un feed di dati sociali.

Non tutte le istruzioni sono classificate in quanto non richiedono risorse o hanno bisogno di importanza per influenzare l'esecuzione.  I comandi DBCC, le istruzioni BEGIN, COMMIT e ROLLBACK TRANSACTION non sono classificati.

## <a name="classification-process"></a>Processo di classificazione

La classificazione per il pool SQL Synapse in Azure Synapse viene ottenuta oggi assegnando gli utenti a un ruolo a cui è assegnata una classe di risorse corrispondente tramite [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). La possibilità di caratterizzare le richieste oltre un account di accesso a una classe di risorse è limitata con questa funzionalità. Un metodo più completo per la classificazione è ora disponibile con la sintassi [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql)  Con questa sintassi, gli utenti del pool SQL Synapse possono assegnare importanza e la quantità di risorse di sistema assegnate a una richiesta tramite il `workload_group` parametro . 

> [!NOTE]
> La classificazione viene valutata in base alle richieste. Più richieste in una singola sessione possono essere classificate in modo diverso.

## <a name="classification-weighting"></a>Ponderazione di classificazione

Come parte del processo di classificazione, la ponderazione è in atto per determinare quale gruppo di carico di lavoro è assegnato.  La ponderazione è basata sui criteri seguenti:

|Parametro di classificatore |Peso   |
|---------------------|---------|
|NOMEMEMBRO:UTENTE      |64       |
|NOMEMEMBRO:RUOLO      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Il `membername` parametro è obbligatorio.  Tuttavia, se il nome membro specificato è un utente del database anziché un ruolo del database, la ponderazione per l'utente è superiore e pertanto viene scelto il classificatore.

Se un utente è membro di più ruoli con diverse classi di risorse assegnate o corrispondenti in più classificatori, all'utente verrà attribuita l'assegnazione della classe di risorse più elevata.  Questo comportamento è coerente con il comportamento di assegnazione della classe di risorse esistente.

## <a name="system-classifiers"></a>Classificatori di sistema

La classificazione del carico di lavoro include classificatori del carico di lavoro del sistema. I classificatori di sistema eseguono il mapping delle appartenenze ai ruoli delle classi di risorse esistenti alle allocazioni di risorse della classe di risorse con priorità normale. I classificatori di sistema non possono essere eliminati. Per visualizzare i classificatori di sistema, è possibile eseguire la query seguente:To view system classifiers, you can run the below query:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Combinazione di assegnazioni di classi di risorse con classificatori

I classificatori di sistema creati per conto dell'utente forniscono un percorso semplice per eseguire la migrazione alla classificazione del carico di lavoro. L'utilizzo di mapping dei ruoli della classe di risorse con precedenza di classificazione può causare errori di classificazione quando si inizia a creare nuovi classificatori con importanza.

Si consideri lo scenario seguente:

- A un data warehouse esistente è assegnato un amministratore di database utente al ruolo della classe di risorse largerc. L'assegnazione della classe di risorse è stata eseguita con sp_addrolemember.
- Il data warehouse viene ora aggiornato con la gestione del carico di lavoro.
- Per testare la nuova sintassi di classificazione, al ruolo del database DBARole (di cui DBAUser è membro), è stato creato un classificatore per eseguirne il mapping a mediumrc e ad alta importanza.
- Quando DBAUser accede ed esegue una query, la query verrà assegnata a largerc. Poiché un utente ha la precedenza sull'appartenenza a un ruolo.

Per semplificare la risoluzione dei problemi di classificazione errata, è consigliabile rimuovere i mapping dei ruoli delle classi di risorse durante la creazione dei classificatori del carico di lavoro.  Il codice seguente restituisce le appartenenze ai ruoli della classe di risorsa esistenti.  Eseguire [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) per ogni nome di membro restituito dalla classe di risorse corrispondente.

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

- Per ulteriori informazioni sulla creazione di un classificatore, vedere [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Vedere la Guida introduttiva su come creare un classificatore di carico di [lavoro Creare un classificatore](quickstart-create-a-workload-classifier-tsql.md)del carico di lavoro.
- Vedere gli articoli [Configure Workload Importance](sql-data-warehouse-how-to-configure-workload-importance.md) relativi alle attività e informazioni su come [gestire e monitorare la gestione del carico di lavoro.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) per visualizzare le query e la loro priorità.