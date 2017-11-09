---
title: Eseguire la migrazione di un Azure Data Warehouse esistente all'archiviazione Premium | Microsoft Docs
description: Istruzioni per la migrazione di un data warehouse esistente all'archiviazione Premium
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 11/29/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 860e50b532b4b0a21d3be54f087730070b0e56bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Eseguire la migrazione di un data warehouse proprio all'archiviazione Premium
Azure SQL Data Warehouse ha recentemente introdotto l'[archiviazione Premium per una maggiore prevedibilità delle prestazioni][premium storage for greater performance predictability]. I data warehouse esistenti attualmente inclusi nell'archiviazione Standard possono essere migrati all'archiviazione Premium. È possibile sfruttare la migrazione automatica oppure, se si preferisce controllare quando eseguire la migrazione (che prevede tempi di inattività), è possibile eseguire la migrazione manualmente.

Se si hanno più data warehouse, usare la [pianificazione della migrazione automatica][automatic migration schedule] per determinare quando verrà eseguita la relativa migrazione.

## <a name="determine-storage-type"></a>Determinare il tipo di archiviazione
Se il data warehouse è stato creato prima delle date riportate di seguito, si sta usando l'archiviazione Standard.

| **Area** | **Data warehouse creato prima di questa data** |
|:--- |:--- |
| Australia orientale |Archiviazione Premium non ancora disponibile |
| Cina orientale |1 novembre 2016 |
| Cina settentrionale |1 novembre 2016 |
| Germania centrale |1 novembre 2016 |
| Germania nord-orientale |1 novembre 2016 |
| India occidentale |Archiviazione Premium non ancora disponibile |
| Giappone occidentale |Archiviazione Premium non ancora disponibile |
| Stati Uniti centro-settentrionali |10 novembre 2016 |

## <a name="automatic-migration-details"></a>Dettagli sulla migrazione automatica
Per impostazione predefinita, la migrazione automatica del database verrà eseguita tra le 18.00 e le 06.00 ora locale dell'area di appartenenza, in base alla [pianificazione della migrazione automatica][automatic migration schedule]. Durante la migrazione non sarà possibile usare il data warehouse esistente. La migrazione richiederà circa un'ora per TB di archiviazione per ogni data warehouse. Durante il processo di migrazione automatica non verranno addebitati costi.

> [!NOTE]
> Al termine della migrazione, il data warehouse sarà di nuovo online e potrà essere riutilizzato.
>
>

Per completare la migrazione, Microsoft si atterrà alla seguente procedura (non è richiesto alcun intervento da parte dell'utente). In questo esempio, immaginare che il data warehouse esistente in archiviazione Standard sia attualmente denominato "MyDW".

1. Microsoft rinomina "MyDW" in "MyDW_DO_NOT_USE_[Timestamp]".
2. Microsoft sospende "MyDW" in "MyDW_DO_NOT_USE_[Timestamp]". Nel mentre, viene eseguito il backup. In caso di problemi, il processo potrebbe essere sospeso e riprendere più volte.
3. Microsoft crea un nuovo data warehouse denominato "MyDW" in archiviazione Premium dal backup eseguito al passaggio 2. "MyDW" non viene visualizzato fino al termine del processo di ripristino.
4. Dopo aver completato il ripristino, "MyDW" torna alle stesse unità data warehouse e allo stato (sospeso o attivo) in cui si trovava prima della migrazione.
5. Al termine della migrazione, Microsoft elimina "MyDW_DO_NOT_USE_[Timestamp]".

> [!NOTE]
> Le impostazioni seguenti non vengono mantenute come parte della migrazione:
>
> * Il controllo a livello di database deve essere abilitato nuovamente.
> * Le regole del firewall a livello di database devono essere aggiunte nuovamente. Le regole del firewall a livello di server non sono interessate.
>
>

### <a name="automatic-migration-schedule"></a>pianificazione della migrazione automatica
I processi di migrazione automatica vengono eseguiti tra le 18.00 e le 06.00 (ora locale per ogni area) durante la seguente pianificazione di interruzione del servizio.

| **Area** | **Data di inizio prevista** | **Data di fine prevista** |
|:--- |:--- |:--- |
| Australia orientale |Non ancora determinata |Non ancora determinata |
| Cina orientale |9 gennaio 2017 |13 gennaio 2017 |
| Cina settentrionale |9 gennaio 2017 |13 gennaio 2017 |
| Germania centrale |9 gennaio 2017 |13 gennaio 2017 |
| Germania nord-orientale |9 gennaio 2017 |13 gennaio 2017 |
| India occidentale |Non ancora determinata |Non ancora determinata |
| Giappone occidentale |Non ancora determinata |Non ancora determinata |
| Stati Uniti centro-settentrionali |9 gennaio 2017 |13 gennaio 2017 |

## <a name="self-migration-to-premium-storage"></a>Migrazione self-service ad archiviazione Premium
Se si preferisce mantenere il controllo sui tempi di inattività, è possibile attenersi alla procedura seguente per eseguire la migrazione di un data warehouse esistente da archiviazione Standard ad archiviazione Premium. Se si sceglie questa opzione, è necessario completare la migrazione self-service prima che inizi la migrazione automatica in tale area. Ciò consente di evitare che la migrazione automatica causi conflitti (vedere la [pianificazione della migrazione automatica][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Istruzioni per la migrazione self-service
Per migrare da sé il data warehouse, utilizzare le funzionalità di backup e ripristino. La parte della migrazione relativa al ripristino dovrebbe richiedere circa un'ora per TB di archiviazione per ogni data warehouse. Per mantenere lo stesso nome dopo il completamento della migrazione, seguire la [procedura di ridenominazione durante la migrazione][steps to rename during migration].

1. [Sospendere][Pause] il data warehouse. Questa operazione richiede un backup automatico.
2. [Eseguire il ripristino][Restore] dallo snapshot più recente.
3. Eliminare il data warehouse esistente in archiviazione Standard. **Se non viene eseguito questo passaggio, si riceverà l'addebito per entrambi i data warehouse.**

> [!NOTE]
> Le impostazioni seguenti non vengono mantenute come parte della migrazione:
>
> * Il controllo a livello di database deve essere abilitato nuovamente.
> * Le regole del firewall a livello di database devono essere aggiunte nuovamente. Le regole del firewall a livello di server non sono interessate.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Rinominare il data warehouse durante la migrazione (facoltativa)
Due database nello stesso server logico non possono avere lo stesso nome. SQL Data Warehouse ora supporta la possibilità di rinominare un data warehouse.

In questo esempio, immaginare che il data warehouse esistente in archiviazione Standard sia attualmente denominato "MyDW".

1. Rinominare "MyDW" utilizzando il seguente comando ALTER DATABASE. (In questo esempio, è necessario rinominarlo "MyDW_BeforeMigration.")  Questo comando arresta tutte le transazioni esistenti e, per avere esito positivo, deve essere eseguito nel database master.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Sospendere][Pause] "MyDW_BeforeMigration." Questa operazione richiede un backup automatico.
3. [Reimpostare][Restore] dallo snapshot più recente un nuovo database con il nome solito (es. "MyDW").
4. Eliminare "MyDW_BeforeMigration". **Se non viene eseguito questo passaggio, si riceverà l'addebito per entrambi i data warehouse.**


## <a name="next-steps"></a>Passaggi successivi
Con il passaggio ad archiviazione Premium, il numero di file BLOB del database nell'architettura sottostante del data warehouse è aumentato. Per ottenere il massimo dei vantaggi delle prestazioni per questa modifica, ricreare gli indici columnstore cluster usando il seguente script. Lo script funziona forzando alcuni dei dati esistenti per i BLOB aggiuntivi. Se non viene eseguita alcuna azione, i dati vengono ovviamente ridistribuiti nel tempo mentre si caricano più dati nelle tabelle.

**Prerequisiti:**

- È necessario eseguire il data warehouse con almeno 1.000 unità data warehouse (vedere [Ridimensionare la potenza di calcolo][scale compute power]).
- L'utente che esegue lo script deve essere nel [ruolo mediumrc][mediumrc role] o superiore. Per aggiungere un utente a questo ruolo, eseguire questo codice: ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create table to control index rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute index rebuilds. If script fails, the below can be re-run to restart where last left off.
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Clean up table created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

In caso di problemi con il data warehouse, [creare un ticket di supporto][create a support ticket] e specificare la migrazione ad archiviazione Premium come possibile causa.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
