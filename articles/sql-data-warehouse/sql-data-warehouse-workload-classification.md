---
title: SQL Data Warehouse classificazione | Microsoft Docs
description: Linee guida per l'uso di classificazione per gestire la concorrenza, priorità e risorse di calcolo per le query in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 888a64de29178834fc47199a033eb6bc62858e57
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617751"
---
# <a name="sql-data-warehouse-workload-classification-preview"></a>Classificazione del carico di lavoro di SQL Data Warehouse (anteprima)

Questo articolo illustra il processo di classificazione del carico di lavoro di SQL Data Warehouse di assegnazione di una classe di risorse e l'importanza alle richieste in ingresso.

> [!Note]
> La classificazione del carico di lavoro è disponibile in anteprima in SQL Data Warehouse Gen2. Anteprima carico di lavoro gestione classificazione e priorità è per le compilazioni con una data di rilascio di 9 aprile 2019 o versione successiva.  Gli utenti consigliabile evitare di usare le compilazioni precedenti a tale data per il test di carico di lavoro gestione.  Per determinare se la compilazione è in grado di gestione del carico di lavoro, eseguire select @@version quando si è connessi all'istanza di SQL Data Warehouse.

## <a name="classification"></a>classificazione

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Classificazione di gestione del carico di lavoro consente di essere applicato alle richieste tramite l'assegnazione di criteri di carico di lavoro [le classi di risorse](resource-classes-for-workload-management.md#what-are-resource-classes) e [importanza](sql-data-warehouse-workload-importance.md).

Sebbene esistano diversi modi per classificare i carichi di lavoro di data warehousing, la classificazione più semplice e più comune è carico ed eseguire query. Si caricano dati con istruzioni delete, update e insert.  I dati usando istruzioni SELECT è eseguire una query. Una soluzione di data warehousing avrà spesso un criterio di carico di lavoro per l'attività di caricamento, ad esempio l'assegnazione di una classe di risorse superiore con più risorse. È stato possibile applicare un criterio del carico di lavoro diverse a query, ad esempio minore importanza rispetto per caricare le attività.

È anche possibile subclassify carichi di lavoro di carico e query. Sottoclassificazione offre maggiore controllo dei carichi di lavoro. Ad esempio, i carichi di lavoro di query può includere gli aggiornamenti del cubo, le query dashboard o query ad-hoc. È possibile classificare ciascuno di questi carichi di lavoro di query con diverse classi di risorse o le impostazioni di priorità. Caricamento può trarre vantaggio da sottoclassificazione. Trasformazioni di grandi dimensioni possono essere assegnate a classi di risorse più grandi. Un'importanza superiore è utilizzabile per verificare che i dati di vendita chiave siano caricatore prima i dati meteo o un feed di dati basati su social network.

Non tutte le istruzioni sono classificate come non richiedono risorse né necessario importanza per influenzare l'esecuzione.  I comandi DBCC, istruzioni BEGIN, COMMIT e ROLLBACK TRANSACTION non vengono classificate.

## <a name="classification-process"></a>Processo di classificazione

Classificazione in SQL Data Warehouse avviene oggi stesso, ovvero assegnando utenti a un ruolo che dispone di una classe di risorse corrispondente assegnata con [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). La possibilità di caratterizzare le richieste di là di un account di accesso a una classe di risorse è limitata con questa funzionalità. Un metodo più completo per la classificazione è ora disponibile con la [CLASSIFICATORE del carico di lavoro creare](/sql/t-sql/statements/create-workload-classifier-transact-sql) sintassi.  Con questa sintassi, gli utenti di SQL Data Warehouse possono assegnare priorità e una classe di risorse alle richieste.  

> [!NOTE]
> Classificazione viene valutata in una singola richiesta. Più richieste in una singola sessione possono essere classificate in modo diverso.

## <a name="classification-precedence"></a>Precedenza di classificazione

Come parte del processo di classificazione, la precedenza è in grado di determinare quale classe di risorse viene assegnata. Classificazione in base a un utente del database ha la precedenza sull'appartenenza al ruolo. Se si crea un classificatore che viene eseguito il mapping utente del database di UserA alla classe di risorse di mediumrc. Quindi, eseguire il mapping del database RoleA (di cui l'utente a è un membro) per la classe di risorse largerc. La funzione di classificazione che esegue il mapping utente del database per la classe di risorse mediumrc avrà la precedenza tramite la funzione di classificazione che esegue il mapping del database RoleA per la classe di risorse largerc.

Se un utente è membro di più ruoli con diverse classi di risorse assegnato o una corrispondenza con più di classificatori, l'utente ha l'assegnazione della classe di risorse più elevato.  Questo comportamento è coerente con il comportamento di assegnazione classe di risorse esistente.

## <a name="system-classifiers"></a>Classificatori di sistema

Classificazione del carico di lavoro dispone di classificatori di carico di lavoro di sistema. I classificatori di sistema di eseguire il mapping le appartenenze ai ruoli di classe risorsa esistente per le allocazioni di risorse di classe di risorse con priorità normale. Classificatori di sistema non possono essere eliminati. Per visualizzare i classificatori di sistema, è possibile eseguire la seguente query:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>La combinazione di assegnazioni di classe con classificatori risorsa

Classificatori di sistema creati per tuo conto forniscono un percorso semplice per eseguire la migrazione alla classificazione del carico di lavoro. Usa mapping dei ruoli di classe di risorse con la precedenza di classificazione, può causare errata classificazione come iniziare a creare nuovi classificatori con priorità.

Si consideri lo scenario seguente:

Data warehouse esistente •an dispone di un utente del database che dbauser assegnato al ruolo di classe della risorsa largerc. L'assegnazione della classe di risorse è stata eseguita con sp_addrolemember.
Data warehouse di •the è ora aggiornata con la gestione del carico di lavoro.
• Per sostituire testare la nuova sintassi di classificazione, il ruolo del database DBARole (ovvero DBAUser un membro di), dispone di un classificatore creato per la loro esecuzione del mapping mediumrc e priorità alta.
• Se DBAUser accede e viene eseguita una query, la query verrà assegnata a largerc. Poiché un utente ha la precedenza su un'appartenenza a ruoli.

Per semplificare la risoluzione dei problemi di errata classificazione, è consigliabile che rimuoverlo mapping dei ruoli di classe di risorse durante la creazione di classificatori di carico di lavoro.  Il codice seguente restituisce la risorsa esistente le appartenenze ai ruoli di classe.  Eseguire [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) per ogni nome di membro restituito dalla classe di risorse corrispondente.

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

Per altre informazioni sulla classificazione del carico di lavoro di SQL Data Warehouse e importanza, vedere [crea un classificatore di carico di lavoro](quickstart-create-a-workload-classifier-tsql.md) e [importanza di SQL Data Warehouse](sql-data-warehouse-workload-importance.md). Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) per visualizzare le query e la loro priorità.
