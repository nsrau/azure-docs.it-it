---
title: Eseguire la migrazione di un Azure Data Warehouse esistente all'archiviazione Premium | Microsoft Docs
description: Istruzioni per la migrazione di un data warehouse esistente all'archiviazione Premium
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: ''
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 03/15/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 3b43bc17b7f9cf80a9520c5c573be3a48d82e4e7
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Eseguire la migrazione di un data warehouse proprio all'archiviazione Premium
Azure SQL Data Warehouse ha recentemente introdotto l'[archiviazione Premium per una maggiore prevedibilità delle prestazioni][premium storage for greater performance predictability]. I data warehouse esistenti attualmente inclusi nell'archiviazione Standard possono essere migrati all'archiviazione Premium. È possibile sfruttare la migrazione automatica oppure, se si preferisce controllare quando eseguire la migrazione (che prevede tempi di inattività), è possibile eseguire la migrazione manualmente.

Se si hanno più data warehouse, usare la [pianificazione della migrazione automatica][automatic migration schedule] per determinare quando verrà eseguita la relativa migrazione.

## <a name="determine-storage-type"></a>Determinare il tipo di archiviazione
Se il data warehouse è stato creato prima delle date riportate di seguito, si sta usando l'archiviazione Standard.

| **Area** | **Data warehouse creato prima di questa data** |
|:--- |:--- |
| Australia orientale |1 gennaio 2018 |
| Cina orientale |1 novembre 2016 |
| Cina settentrionale |1 novembre 2016 |
| Germania centrale |1 novembre 2016 |
| Germania nord-orientale |1 novembre 2016 |
| India occidentale |1 febbraio 2018 |
| Giappone occidentale |1 febbraio 2018 |
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
| Australia orientale |19 marzo 2018 |20 marzo 2018 |
| Cina orientale |Migrazione già eseguita |Migrazione già eseguita |
| Cina settentrionale |Migrazione già eseguita |Migrazione già eseguita |
| Germania centrale |Migrazione già eseguita |Migrazione già eseguita |
| Germania nord-orientale |Migrazione già eseguita |Migrazione già eseguita |
| India occidentale |19 marzo 2018 |20 marzo 2018 |
| Giappone occidentale |19 marzo 2018 |20 marzo 2018 |
| Stati Uniti centro-settentrionali |Migrazione già eseguita |Migrazione già eseguita |

## <a name="self-migration-to-premium-storage"></a>Migrazione self-service ad archiviazione Premium
Se si preferisce mantenere il controllo sui tempi di inattività, è possibile attenersi alla procedura seguente per eseguire la migrazione di un data warehouse esistente da archiviazione Standard ad archiviazione Premium. Se si sceglie questa opzione, è necessario completare la migrazione self-service prima che inizi la migrazione automatica in tale area. Ciò consente di evitare che la migrazione automatica causi conflitti (vedere la [pianificazione della migrazione automatica][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Istruzioni per la migrazione self-service
Per migrare da sé il data warehouse, utilizzare le funzionalità di backup e ripristino. La parte della migrazione relativa al ripristino dovrebbe richiedere circa un'ora per TB di archiviazione per ogni data warehouse. Per mantenere lo stesso nome dopo il completamento della migrazione, seguire la [procedura di ridenominazione durante la migrazione][steps to rename during migration].

1. [Sospendere][Pause] il data warehouse. 
2. [Eseguire il ripristino][Restore] dallo snapshot più recente.
3. Eliminare il data warehouse esistente in archiviazione Standard. **Se non viene eseguito questo passaggio, si riceverà l'addebito per entrambi i data warehouse.**

> [!NOTE]
>
> Quando si ripristina il data warehouse, verificare che il punto di ripristino più recente sia successivo alla sospensione del data warehouse.
>
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
2. [Sospendere][Pause] "MyDW_BeforeMigration." 
3. [Reimpostare][Restore] dallo snapshot più recente un nuovo database con il nome solito (es. "MyDW").
4. Eliminare "MyDW_BeforeMigration". **Se non viene eseguito questo passaggio, si riceverà l'addebito per entrambi i data warehouse.**


## <a name="next-steps"></a>Passaggi successivi
Con il passaggio ad archiviazione Premium, il numero di file BLOB del database nell'architettura sottostante del data warehouse è aumentato. Per ottenere il massimo dei vantaggi delle prestazioni per questa modifica, ricreare gli indici columnstore cluster usando il seguente script. Lo script funziona forzando alcuni dei dati esistenti per i BLOB aggiuntivi. Se non viene eseguita alcuna azione, i dati vengono ovviamente ridistribuiti nel tempo mentre si caricano più dati nelle tabelle.

In caso di problemi con il data warehouse, [creare un ticket di supporto][create a support ticket] e specificare la migrazione ad archiviazione Premium come possibile causa.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: quickstart-scale-compute-portal.md
[mediumrc role]: resource-classes-for-workload-management.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
