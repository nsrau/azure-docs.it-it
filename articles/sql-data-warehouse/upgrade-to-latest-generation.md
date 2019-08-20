---
title: Aggiornamento alla generazione più recente di Azure SQL Data Warehouse | Microsoft Docs
description: Aggiornare Azure SQL Data Warehouse alla generazione più recente dell'architettura hardware e di archiviazione di Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: 2864e3d29a0beccd2ef52732a85ea1495e1efab8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575295"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Ottimizzare le prestazioni aggiornando SQL Data Warehouse

Aggiornare Azure SQL Data Warehouse alla generazione più recente dell'architettura hardware e di archiviazione di Azure.

## <a name="why-upgrade"></a>Ragioni dell'aggiornamento

È ora possibile eseguire facilmente l'aggiornamento al livello Gen2 con ottimizzazione per il calcolo di SQL Data Warehouse nel portale di Azure per le [aree supportate](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Se l'area non supporta l'aggiornamento automatico, è possibile eseguire l'aggiornamento a un'area supportata o attendere la disponibilità dell'aggiornamento autonomo nella propria area. Eseguire subito l'aggiornamento per sfruttare i vantaggi della generazione più recente di hardware e architettura di archiviazione migliorata di Azure, che include prestazioni più veloci, una maggiore scalabilità e archiviazione a colonne illimitata. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Si applica a

Questo aggiornamento si applica ai data warehouse di livello Gen1 con ottimizzazione per il calcolo nelle [aree supportate](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Prima di iniziare

1. Controllare se l'[area](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) è supportata per la migrazione da Gen1 a Gen2. Tenere presente le date di migrazione automatica. Per evitare conflitti con il processo automatizzato, pianificare la migrazione manuale prima della data di inizio processo automatizzato.
2. Se ci si trova in un'area non è ancora supportata, continuare a controllare che la propria area venga aggiunta oppure [eseguire l'aggiornamento tramite ripristino](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) in un'area supportata.
3. Se l'area è supportata, [eseguire l'aggiornamento tramite il portale di Azure](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Selezionare il livello di prestazioni consigliato** per il data warehouse in base al livello di prestazioni corrente del livello Gen1 con ottimizzazione per il calcolo usando il mapping seguente:

   | Livello Gen1 con ottimizzazione per il calcolo | Livello Gen2 con ottimizzazione per il calcolo |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> I livelli di prestazioni consigliati non sono una conversione diretta. Ad esempio, è consigliabile passare da DW600 DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Eseguire l'aggiornamento in un'area supportata tramite portale di Azure

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> La migrazione da Gen1 a Gen2 tramite il portale di Azure è permanente. Non esiste un processo per tornare a Gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

1. Se il data warehouse di livello Gen1 con ottimizzazione per il calcolo da aggiornare è sospeso, [riprendere il data warehouse](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Azure SQL Data Warehouse deve essere in esecuzione per eseguire la migrazione a Gen2.

2. Questa operazione potrebbe comportare alcuni minuti di inattività. 

3. Identificare tutti i riferimenti del codice ai livelli di prestazioni Gen1 con ottimizzazione per il calcolo e modificarli impostando il livello di prestazioni equivalente Gen2 con ottimizzazione per il calcolo. Di seguito sono riportati due esempi di in cui è necessario aggiornare i riferimenti del codice prima dell'aggiornamento:

   Comando Gen1 PowerShell originale:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Modificato come segue:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" diventa RequestedServiceObjectiveName "DW300**c**"
   >

   Comando Gen1 T-SQL originale:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Modificato come segue:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = 'DW300' diventa SERVICE_OBJECTIVE = 'DW300**c**'

## <a name="start-the-upgrade"></a>Avviare l'aggiornamento

1. Andare al data warehouse di livello Gen1 con ottimizzazione per il calcolo nel portale di Azure. Se il data warehouse di livello Gen1 con ottimizzazione per il calcolo da aggiornare è sospeso, [riprendere il data warehouse](pause-and-resume-compute-portal.md). 
2. Selezionare la scheda **Aggiorna alla seconda generazione** nella scheda attività:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Se la scheda **Aggiorna alla seconda generazione** non è visualizzata sotto la scheda Attività, il tipo di sottoscrizione è limitato nell'area corrente.
    > [Inviare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md) per fare inserire la sottoscrizione nell'elenco elementi consentiti.

3. Assicurarsi che il carico di lavoro abbia completato l'esecuzione e sia inattivo prima dell'aggiornamento. Si verificheranno alcuni minuti di inattività prima che il data warehouse torni online come data warehouse di livello Gen2 con ottimizzazione per il calcolo. **Selezionare l'aggiornamento**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorare l'aggiornamento** controllando lo stato nel portale di Azure:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Il primo passaggio del processo di aggiornamento prevede l'operazione di ridimensionamento ("Aggiornamento - Offline") in cui verranno terminate tutte le sessioni ed interrotte tutte le connessioni. 

   Il secondo passaggio del processo di aggiornamento è la migrazione dei dati ("Aggiornamento - Online"). La migrazione dei dati è un processo in background con flusso irregolare. Questo processo sposta lentamente i dati a colonne dall'architettura di archiviazione precedente alla nuova architettura di archiviazione usando la cache SSD locale. Durante questo periodo, il data warehouse sarà online per l'esecuzione di query e caricamenti. I dati saranno disponibili per le query indipendentemente dal fatto che sia stata o meno completata la migrazione. La migrazione dei dati avviene a velocità variabili a seconda delle dimensioni dei dati, del livello di prestazioni e del numero di segmenti columnstore. 

5. **Raccomandazione facoltativa:** Al termine dell'operazione di ridimensionamento, è possibile velocizzare il processo in background di migrazione dei dati. È possibile forzare immediatamente lo spostamento dei dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie su cui si prevede di eseguire query, con SLO e classe di risorse più grandi. Questa operazione è **offline**, confrontata con il processo un processo in background con flusso irregolare, che può richiedere ore a seconda di numero e dimensione delle tabelle. Tuttavia, al termine dell'operazione la migrazione dei dati sarà molto più veloce per via della nuova architettura di archiviazione migliorata con gruppi di righe di qualità elevata.
 
> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

La query seguente genera i comandi Alter Index Rebuild necessari per velocizzare la migrazione dei dati:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Eseguire l'aggiornamento da un'area geografica di Azure con l'esecuzione del ripristino tramite il portale di Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Creare un il punto di ripristino definito dall'utente usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla SQL Data Warehouse per la quale si desidera creare un punto di ripristino.

3. Nella parte superiore della sezione della panoramica, selezionare **+ Nuovo punto di ripristino**.

    ![Nuovo punto di ripristino](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Specificare un nome per il punto di ripristino.

    ![Nome del punto di ripristino](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Ripristinare un database attivo o sospeso con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al SQL Data Warehouse da cui si desidera eseguire il ripristino.
3. Nella parte superiore della sezione della panoramica, selezionare **+ Ripristino**.

    ![ Panoramica del servizio di ripristino](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Selezionare **Punto di ripristino automatico** o **Punti di ripristino definiti dall'utente**.

    ![Punti di ripristino automatici](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Per i punti di ripristino definiti dall'utente, **selezionare un punto di ripristino** oppure **creare un nuovo punto di ripristino definito dall'utente**. Scegliere un server in un'area geografica Gen2 supportata. 

    ![Punti di ripristino definiti dall'utente](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Eseguire il ripristino da un'area geografica di Azure con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per ripristinare un database, usare il cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) .

> [!NOTE]
> È possibile eseguire un ripristino geografico alla seconda generazione. A tale scopo, specificare ServiceObjectiveName di seconda generazione (ad es. DW1000**c**) come parametro facoltativo.

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Selezionare il database che si desidera ripristinare.
5. Creare la richiesta di ripristino per il database, specificando un parametro ServiceObjectiveName Gen2.
6. Verificare lo stato del database recuperato con il ripristino geografico.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Per configurare il database al termine del ripristino, vedere [Configurare il database dopo il ripristino](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

Il database ripristinato sarà abilitato TDE se il database di origine è abilitato per questa tecnologia.


Se si riscontrano problemi con il data warehouse, creare una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) indicando l'aggiornamento a Gen2 come possibile causa.

## <a name="next-steps"></a>Passaggi successivi

Il data warehouse aggiornato è online. Per sfruttare i vantaggi dell'architettura avanzata, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md).
