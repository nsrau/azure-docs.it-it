---
title: Classificazione del carico di lavoro
description: Linee guida per l'uso della classificazione per gestire le risorse di concorrenza, importanza e calcolo per le query in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 15ca4b9fe3c40b7bf49d86464858747642e3cb5a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685377"
---
# <a name="azure-sql-data-warehouse-workload-classification"></a>Classificazione del carico di lavoro Azure SQL Data Warehouse

Questo articolo illustra il processo di classificazione del carico di lavoro SQL Data Warehouse per l'assegnazione di una classe di risorse e l'importanza alle richieste in ingresso.

## <a name="classification"></a>Classificazione

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Classificazione di gestione del carico di lavoro consente di applicare i criteri del carico di lavoro alle richieste tramite l'assegnazione di [classi di risorse](resource-classes-for-workload-management.md#what-are-resource-classes) e [importanza](sql-data-warehouse-workload-importance.md).

Sebbene esistano molti modi per classificare i carichi di lavoro di data warehouse, la classificazione più semplice e più comune è il caricamento e la query. I dati vengono caricati con le istruzioni INSERT, Update e DELETE.  Si esegue una query sui dati usando Selects. Una soluzione di data warehousing avrà spesso un criterio del carico di lavoro per l'attività di caricamento, ad esempio l'assegnazione di una classe di risorse superiore con più risorse. Un criterio del carico di lavoro diverso può essere applicato alle query, ad esempio un'importanza inferiore rispetto alle attività di caricamento.

È anche possibile sottoclassificare i carichi di lavoro di query e di carico. La sottoclassificazione offre un maggiore controllo sui carichi di lavoro. I carichi di lavoro di query possono ad esempio essere costituiti da aggiornamenti del cubo, query del dashboard o query ad hoc. È possibile classificare ognuno di questi carichi di lavoro di query con diverse classi di risorse o impostazioni di importanza. Il carico può anche trarre vantaggio dalla sottoclassificazione. Le trasformazioni di grandi dimensioni possono essere assegnate a classi di risorse più grandi. È possibile usare una maggiore importanza per garantire che i dati delle vendite chiave siano loader prima dei dati meteorologici o di un feed di dati di social networking.

Non tutte le istruzioni sono classificate poiché non richiedono risorse o hanno importanza per influenzare l'esecuzione.  Le istruzioni DBCC Command, BEGIN, COMMIT e ROLLBACK TRANSACTION non sono classificate.

## <a name="classification-process"></a>Processo di classificazione

La classificazione in SQL Data Warehouse viene eseguita oggi assegnando gli utenti a un ruolo a cui è assegnata una classe di risorse corrispondente usando [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). La possibilità di caratterizzare le richieste oltre un accesso a una classe di risorse è limitata a questa funzionalità. Un metodo più completo per la classificazione è ora disponibile con la sintassi di [creazione del classificatore del carico di lavoro](/sql/t-sql/statements/create-workload-classifier-transact-sql) .  Con questa sintassi, SQL Data Warehouse gli utenti possono assegnare importanza e una classe di risorse alle richieste.  

> [!NOTE]
> La classificazione viene valutata in base alle singole richieste. Più richieste in una singola sessione possono essere classificate in modo diverso.

## <a name="classification-precedence"></a>Precedenza classificazione

Come parte del processo di classificazione, per determinare quale classe di risorse è stata assegnata è disponibile la precedenza. La classificazione basata su un utente del database ha la precedenza sull'appartenenza ai ruoli. Se si crea un classificatore che esegue il mapping dell'utente del database utente a alla classe di risorse mediumrc. Eseguire quindi il mapping del ruolo del database rolea, di cui l'UtenteA è membro, alla classe di risorse largerc. Il classificatore che esegue il mapping dell'utente del database alla classe di risorse mediumrc avrà la precedenza sul classificatore che esegue il mapping del ruolo del database rolea alla classe di risorse largerc.

Se un utente è membro di più ruoli con classi di risorse diverse assegnate o corrispondenti in più classificatori, all'utente viene assegnata l'assegnazione della classe di risorse più elevata.  Questo comportamento è coerente con il comportamento di assegnazione della classe di risorse esistente.

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

Per semplificare la risoluzione dei problemi di classificazione, è consigliabile rimuovere i mapping dei ruoli della classe di risorse durante la creazione dei classificatori del carico di lavoro.  Il codice seguente restituisce le appartenenze ai ruoli della classe di risorse esistenti.  Eseguire [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) per ogni nome di membro restituito dalla classe di risorse corrispondente.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla creazione di un classificatore, vedere la pagina relativa alla creazione di un [classificatore del carico di lavoro (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Vedere la Guida introduttiva su come creare un classificatore del carico di lavoro [creare un classificatore del carico di lavoro](quickstart-create-a-workload-classifier-tsql.md).
- Vedere gli articoli sulle procedure per [configurare l'importanza del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md) e come [gestire e monitorare la gestione del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) per visualizzare le query e la loro priorità.