---
title: Aggiornamento alla generazione più recente di Azure SQL Data Warehouse | Microsoft Docs
description: Aggiornare Azure SQL Data Warehouse alla generazione più recente dell'architettura hardware e di archiviazione di Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 58d65ef05ed872bb357070de9866253baea5dc70
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777808"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Ottimizzare le prestazioni aggiornando SQL Data Warehouse
Aggiornare Azure SQL Data Warehouse alla generazione più recente dell'architettura hardware e di archiviazione di Azure.

## <a name="why-upgrade"></a>Ragioni dell'aggiornamento
È ora possibile eseguire l'aggiornamento senza problemi a SQL Data Warehouse di seconda generazione nel portale di Azure. Se si ha un data warehouse di prima generazione, è consigliabile eseguire l'aggiornamento. Con l'aggiornamento, è possibile usare la generazione più recente di hardware di Azure e un'architettura di archiviazione migliorata. È possibile sfruttare i vantaggi di prestazioni più veloci, una maggiore scalabilità e archiviazione a colonne illimitata. 

## <a name="applies-to"></a>Si applica a
Questo aggiornamento si applica ai data warehouse di prima generazione.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Prima di iniziare
> [!NOTE]
> Se il data warehouse di prima generazione esistente non è presente in un'area in cui è disponibile la seconda generazione, è possibile il [ripristino delle replica geografica alla seconda generazione](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) tramite PowerShell in un'area supportata.
> 
>

1. Se il data warehouse di prima generazione da aggiornare è sospeso, [riprenderne l'esecuzione](pause-and-resume-compute-portal.md).
2. Questa operazione potrebbe comportare alcuni minuti di inattività. 



## <a name="start-the-upgrade"></a>Avviare l'aggiornamento

1. Andare al data warehouse di prima generazione nel portale di Azure e fare clic su **Upgrade to Gen2** (Aggiorna alla seconda generazione): ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Per impostazione predefinita, **selezionare il livello di prestazioni consigliato** per il data warehouse in base al livello di prestazioni corrente nella prima generazione usando il mapping seguente:
    
| Prima generazione | Seconda generazione |
| :----------------------: | :-------------------: |
|      DW100 - DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Assicurarsi che il carico di lavoro abbia completato l'esecuzione e sia inattivo prima dell'aggiornamento. Si verificheranno alcuni minuti di inattività prima che il data warehouse torni online come data warehouse di seconda generazione. **Fare clic su Aggiorna**. Il prezzo del livello di prestazioni della seconda generazione è attualmente dimezzato per il periodo di anteprima:
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorare l'aggiornamento** controllando lo stato nel portale di Azure:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Il primo passaggio del processo di aggiornamento prevede l'operazione di ridimensionamento ("Aggiornamento - Offline") in cui verranno terminate tutte le sessioni ed interrotte tutte le connessioni. 
   
   Il secondo passaggio del processo di aggiornamento è la migrazione dei dati ("Aggiornamento - Online"). La migrazione dei dati è un processo in background a cascata online, che sposta lentamente i dati a colonne dall'architettura di archiviazione precedente alla nuova architettura di archiviazione sfruttando la cache SSD locale. Durante questo periodo, il data warehouse sarà online per l'esecuzione di query e caricamenti. Tutti i dati saranno disponibili per le query indipendentemente dal fatto che sia stata o meno completata la migrazione. La migrazione dei dati avviene a una velocità variabile a seconda delle dimensioni dei dati, del livello di prestazioni e del numero di segmenti columnstore. 

5. **Trovare il data warehouse di seconda generazione** usando il pannello SQL database browse (Sfoglia database SQL). 

> [!NOTE]
> Esiste attualmente un problema quando i data warehouse di seconda generazione non vengono visualizzati nel pannello per sfogliare SQL Data Warehouse. Usare il pannello SQL database browse (Sfoglia database SQL) per trovare il data warehouse di seconda generazione appena aggiornato. Microsoft sta lavorando attivamente su questa correzione.
> 

6. **Raccomandazione facoltativa:** per accelerare il processo in background di migrazione dei dati, è consigliabile forzare immediatamente lo spostamento dei dati eseguendo [ALTER INDEX REBUILD](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) su tutte le tabelle columnstore, con SLO e classe di risorse più grande. Questa operazione è offline rispetto al processo in background a cascata, tuttavia la migrazione dei dati sarà molto più veloce quando sarà poi possibile sfruttare appieno la nuova architettura di archiviazione migliorata dopo il completamento con gruppi di righe di alta qualità. 

La query seguente genera i comandi ALTER INDEX REBUILD necessari per accelerare il processo di migrazione dei dati:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>Passaggi successivi
Il data warehouse aggiornato è online. Per sfruttare i vantaggi dell'architettura avanzata, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md).
 
